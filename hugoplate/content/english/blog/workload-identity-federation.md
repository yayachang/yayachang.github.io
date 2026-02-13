---
title: "告別 Service Account 金鑰：Workload Identity Federation 完整入門指南"
meta_title: "告別 Service Account 金鑰：Workload Identity Federation 完整入門指南"
description: "你是否曾經把 GCP Service Account 的 JSON 金鑰存在專案裡，然後心虛地加進 .gitignore？這篇文章會告訴你一個更安全、更優雅的做法。"
date: '2026-02-13T00:00:00+08:00'
image: "/images/workload-identity-federation.png"
categories: ["Cloud"]
author: "Yaya"
tags: ["gcp", "aws", "security", "wif", "workload-identity"]
draft: false
---

> 你是否曾經把 GCP Service Account 的 JSON 金鑰存在專案裡，然後心虛地加進 `.gitignore`？這篇文章會告訴你一個更安全、更優雅的做法。

## 問題：Service Account 金鑰的痛點

在串接 Google Cloud API（例如 Google Play Developer API、Google Sheets API）時，最常見的做法是：

1. 在 GCP Console 建立 Service Account
2. 下載 JSON 金鑰檔案
3. 把金鑰存在環境變數、Secrets Manager 或設定檔裡
4. 程式讀取金鑰後呼叫 API

這個流程能動，但有幾個根本性的問題：

- **金鑰是永久有效的**：一旦洩漏，攻擊者可以無限期使用
- **需要手動輪替**：你得定期重新產生金鑰並更新部署
- **金鑰會四處流竄**：開發環境、CI/CD、Secrets Manager⋯⋯每多一個地方就多一個風險
- **組織政策可能直接禁止**：許多企業啟用了 `iam.disableServiceAccountKeyCreation`，根本不讓你建金鑰

那有沒有辦法**不用金鑰**，就能讓你的 AWS 上的服務去呼叫 GCP API？

答案就是 **Workload Identity Federation（WIF）**。

---

## 核心概念：用「身份交換」取代「密碼」

### 一個生活化的比喻

想像你是台灣人，要去日本的銀行辦事。銀行不認識你，但他們信任台灣的外交部。

流程是這樣的：

1. **你拿出台灣護照**（你在 AWS 的身份）
2. **日本銀行打電話給台灣外交部確認**（Google 向 AWS 驗證你的身份）
3. **確認OK，銀行發給你一張臨時通行證**（Google 發給你一個短期 Token）
4. **你用臨時通行證辦事**（用 Token 呼叫 GCP API）

整個過程**沒有交換密碼**，只有身份的驗證和信任的傳遞。

### 技術對照

| 比喻 | 技術概念 |
|------|----------|
| 台灣護照 | AWS IAM Role 的臨時憑證（STS Token） |
| 台灣外交部 | AWS Security Token Service (STS) |
| 日本銀行 | Google Cloud IAM |
| 銀行信任外交部 | Workload Identity Pool 的 AWS Provider |
| 臨時通行證 | Google 的 OAuth 2.0 Access Token |

---

## 架構圖

```
你的 AWS 服務（EC2 / ECS / Lambda）
        │
        │ ① 我是 AWS 帳號 123456 的 Role "my-server-role"
        ▼
┌─────────────────────────────────┐
│  Google Security Token Service  │
│  (sts.googleapis.com)           │
│                                 │
│  ② 讓我驗證一下...               │
│     → 呼叫 AWS STS 確認身份      │
│     → 檢查 Workload Identity    │
│       Pool 的信任設定            │
│  ③ OK，你的 AWS 身份是合法的      │
└────────────┬────────────────────┘
             │
             │ ④ 用驗證過的身份模擬 Service Account
             ▼
┌─────────────────────────────────┐
│  GCP Service Account            │
│  sa@project.iam.gserviceaccount │
│                                 │
│  ⑤ 產生短期 Access Token         │
│     （有效期 1 小時）             │
└────────────┬────────────────────┘
             │
             │ ⑥ 用 Access Token 呼叫 API
             ▼
┌─────────────────────────────────┐
│  Google Play Developer API      │
│  （或任何 GCP API）              │
└─────────────────────────────────┘
```

---

## 三個你一定要懂的元件

### 1. Workload Identity Pool（身份池）

**把它想成一個「外國人登記處」。**

它是 GCP 專案裡的一個容器，用來管理外部身份（AWS、Azure、GitHub Actions 等）的信任關係。

```
一個 GCP 專案可以有多個 Pool
    └── Pool: "production-pool"
    └── Pool: "staging-pool"
```

### 2. Provider（身份提供者）

**把它想成登記處裡的「國家窗口」。**

每個 Pool 裡面可以有多個 Provider，每個 Provider 對應一個外部身份來源。

```
Pool: "production-pool"
    └── Provider: "aws-provider"     ← 信任 AWS 帳號 123456789
    └── Provider: "github-provider"  ← 信任 GitHub repo xxx/yyy
```

Provider 的設定決定了：
- **信任誰**：哪個 AWS 帳號？哪個 GitHub repo？
- **映射什麼身份**：把 AWS 的 Role ARN 對應到什麼 GCP 屬性？

### 3. Service Account + IAM 綁定

**把它想成「這個外國人可以用哪個本地人的名義辦事」。**

外部身份本身在 GCP 裡沒有任何權限。你需要授權它「扮演」(impersonate) 某個 Service Account，而這個 Service Account 才有實際的 API 權限。

```
AWS Role "my-server-role"
    → 被允許扮演 → GCP Service Account "play-api@project.iam.gserviceaccount.com"
        → 擁有 → Google Play Developer API 的權限
```

---

## 實作步驟

### Step 0：前置準備

確認你有：
- 一個 GCP 專案（記下 Project ID 和 Project Number）
- 一個 AWS 帳號（記下 Account ID）
- 你的服務跑在 AWS 上，且有 IAM Role
- 本機安裝了 `gcloud` CLI

### Step 1：建立 Workload Identity Pool

```bash
gcloud iam workload-identity-pools create my-aws-pool \
  --project="YOUR_PROJECT_ID" \
  --location="global" \
  --display-name="AWS Workload Pool"
```

### Step 2：建立 AWS Provider

```bash
gcloud iam workload-identity-pools providers create-aws my-aws-provider \
  --project="YOUR_PROJECT_ID" \
  --location="global" \
  --workload-identity-pool="my-aws-pool" \
  --account-id="YOUR_AWS_ACCOUNT_ID" \
  --display-name="AWS Provider"
```

其中 `--account-id` 是你的 12 位數 AWS 帳號 ID。

### Step 3：建立 Service Account（如果還沒有）

```bash
gcloud iam service-accounts create play-api-sa \
  --project="YOUR_PROJECT_ID" \
  --display-name="Google Play API Service Account"
```

> 不需要給這個 Service Account 任何 GCP 專案層級的角色。它的 Google Play 權限是在 Google Play Console 裡授予的。

### Step 4：授權 Pool 扮演 Service Account

```bash
gcloud iam service-accounts add-iam-policy-binding \
  "play-api-sa@YOUR_PROJECT_ID.iam.gserviceaccount.com" \
  --project="YOUR_PROJECT_ID" \
  --role="roles/iam.workloadIdentityUser" \
  --member="principalSet://iam.googleapis.com/projects/YOUR_PROJECT_NUMBER/locations/global/workloadIdentityPools/my-aws-pool/attribute.aws_role/arn:aws:sts::YOUR_AWS_ACCOUNT_ID:assumed-role/YOUR_AWS_ROLE_NAME"
```

> **注意**：`--member` 裡的 `YOUR_AWS_ROLE_NAME` 是你的 EC2/ECS 服務使用的 IAM Role 名稱。這樣只有特定 Role 能扮演這個 Service Account。

### Step 5：產生 credential-config.json

```bash
gcloud iam workload-identity-pools create-cred-config \
  "projects/YOUR_PROJECT_NUMBER/locations/global/workloadIdentityPools/my-aws-pool/providers/my-aws-provider" \
  --service-account="play-api-sa@YOUR_PROJECT_ID.iam.gserviceaccount.com" \
  --aws \
  --output-file=credential-config.json
```

這會產生一個設定檔（不是金鑰！），內容大致如下：

```json
{
  "type": "external_account",
  "audience": "//iam.googleapis.com/projects/123456/locations/global/workloadIdentityPools/my-aws-pool/providers/my-aws-provider",
  "subject_token_type": "urn:ietf:params:aws:token-type:aws4_request",
  "service_account_impersonation_url": "https://iamcredentials.googleapis.com/v1/projects/-/serviceAccounts/play-api-sa@project.iam.gserviceaccount.com:generateAccessToken",
  "token_url": "https://sts.googleapis.com/v1/token",
  "credential_source": {
    "environment_id": "aws1",
    "region_url": "http://169.254.169.254/latest/meta-data/placement/availability-zone",
    "url": "http://169.254.169.254/latest/meta-data/iam/security-credentials",
    "regional_cred_verification_url": "https://sts.{region}.amazonaws.com?Action=GetCallerIdentity&Version=2011-06-15"
  }
}
```

### Step 6：在程式碼裡使用

Google Auth 函式庫的 `GoogleCredentials.fromStream()` 會自動辨識 JSON 裡的 `"type"` 欄位：

```kotlin
// 不管是 Service Account JSON 還是 WIF credential config，
// 同一行程式碼都能處理：
val credentials = GoogleCredentials
    .fromStream(ByteArrayInputStream(credentialJson.toByteArray()))
    .createScoped(listOf(AndroidPublisherScopes.ANDROIDPUBLISHER))
```

| `type` 值 | 行為 |
|-----------|------|
| `"service_account"` | 直接用 JSON 裡的私鑰簽署 JWT |
| `"external_account"` | 從 AWS 取得臨時憑證 → 交換 GCP Token |

**你不需要改任何程式邏輯，只要換掉存在 Secrets Manager 裡的 JSON 內容即可。**

---

## 安全性比較

| | Service Account 金鑰 | Workload Identity Federation |
|---|---|---|
| **憑證類型** | 永久私鑰 | 無靜態密鑰 |
| **有效期** | 永久（直到手動撤銷） | Access Token 1 小時自動過期 |
| **洩漏風險** | 金鑰檔外洩 = 完整存取 | credential-config.json 外洩 ≠ 任何存取權（還需要 AWS 身份） |
| **輪替** | 手動 | 不需要（自動） |
| **最小權限** | 需要自律管理 | 可綁定特定 AWS Role |
| **合規** | 可能被組織政策禁止 | 符合零信任架構 |

---

## 本地開發怎麼辦？

WIF 依賴 AWS 的 Instance Metadata（`169.254.169.254`），本地開發環境沒有這個。有兩種解法：

### 方法 A：本地用 Service Account 金鑰，Production 用 WIF

如果你的個人 GCP 專案沒有禁止金鑰建立：

```
local.conf  → SSM 裡存 Service Account JSON（type: "service_account"）
prod.conf   → SSM 裡存 credential-config.json（type: "external_account"）
```

程式碼完全不用改，`GoogleCredentials.fromStream()` 自動處理。

### 方法 B：本地用 gcloud 的 Application Default Credentials

```bash
gcloud auth application-default login
```

然後程式改用：

```kotlin
val credentials = GoogleCredentials.getApplicationDefault()
    .createScoped(listOf(AndroidPublisherScopes.ANDROIDPUBLISHER))
```

這會使用你個人 Google 帳號的身份（需要有對應 API 權限）。

---

## 常見問題

### Q：credential-config.json 洩漏了怎麼辦？

**不用太緊張。** 這個檔案只是一份「設定」，裡面沒有任何密鑰。攻擊者還需要同時擁有你的 AWS IAM Role 權限才能使用它。但基於最佳實踐，還是建議存在 Secrets Manager 裡。

### Q：我的 AWS 服務不是跑在 EC2 上，是 ECS / Lambda / EKS？

都支援。只要你的服務有綁定 IAM Role（ECS 用 Task Role、Lambda 用 Execution Role），WIF 都能透過 AWS STS 驗證身份。

### Q：可以限制只有特定 AWS Role 才能存取嗎？

可以。在 Step 4 的 `--member` 裡指定精確的 Role ARN，而不是整個 AWS 帳號。

### Q：每次 API 呼叫都要做一次身份交換嗎？

不用。Google Auth 函式庫會自動快取 Access Token，在過期前自動刷新。你的程式碼感受不到任何差異。

### Q：除了 AWS，還支援哪些外部身份來源？

- AWS
- Azure Active Directory
- GitHub Actions（OIDC）
- GitLab CI/CD
- Kubernetes（任何支援 OIDC 的叢集）
- 任何符合 OIDC / SAML 2.0 的身份提供者

---

## 總結

| 你以前做的 | 你現在可以做的 |
|-----------|--------------|
| 建立 Service Account → 下載 JSON 金鑰 → 到處搬移金鑰 | 建立 WIF Pool → 信任 AWS 帳號 → 程式自動用 AWS 身份換 GCP Token |
| 擔心金鑰洩漏 | 沒有金鑰可以洩漏 |
| 定期輪替金鑰 | Token 自動 1 小時過期，無需輪替 |
| 被組織政策擋住 | 完全符合零信任要求 |

Workload Identity Federation 的核心思想就是：**不要搬運密碼，而是建立信任關係**。這和現代的零信任安全架構是完全一致的。
