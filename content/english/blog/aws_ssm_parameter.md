---
title: "AWS SSM Parameter Store 設定"
meta_title: "AWS SSM Parameter Store 設定與使用技術指南"
description: "AWS Systems Manager Parameter Store 是一種安全的配置管理服務，用於存儲應用程序參數（如數據庫憑證、API 密鑰）。本指南將涵蓋如何在本地和正式環境中安全高效地讀取參數。"
date: '2025-01-13T12:00:00+08:00'
image: "/images/aws_ssm.webp"
categories: ["技術分享"]
author: "Yaya"
tags: ["AWS","SSM"]
draft: false
---


**AWS SSM Parameter Store 設定與使用技術指南**
=====================================

**目錄**
------

1.  前言
2.  本地開發環境設置（使用 AWS SSO）
   -   安裝 AWS CLI
   -   配置 AWS SSO
   -   本地運行應用程式
3.  正式環境部署（使用 EC2 IAM Role）
   -   創建 IAM Role
   -   為 EC2 實例分配角色
   -   測試部署
4.  關鍵依譜與程式範例
5.  注意事項與最佳實踐

* * * * *

**1\. 前言**
----------

AWS Systems Manager Parameter Store 是一種安全的配置管理服務，用於儲存應用程式參數（如數據庫應用證明、API 密鑰）。本指南將演示如何在本地和正式環境中安全高效地讀取參數。

* * * * *

**2\. 本地開發環境設置（使用 AWS SSO）**
----------------------------

### **2.1 安裝 AWS CLI**

1.  下載並安裝 AWS CLI（版本需為 v2 或更高）：\
    [AWS CLI 安裝指南](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)

2.  確認安裝是否成功：

    ```
    aws --version
    ```

### **2.2 配置 AWS SSO**

1.  執行下列命令開始配置 AWS SSO：

    ```
    aws configure sso
    ```

   -   **SSO Start URL**: 填入您的 SSO 登錄 URL（如 `https://your-sso-url.awsapps.com/start`）。
   -   **SSO Region**: 填寫與您資源一致的 AWS 區域（如 `ap-northeast-3`）。
   -   **SSO Session Name**: 建議使用 `default`。
   -   **Account ID**: 輸入您的 AWS 帳戶 ID。
   -   **Role Name**: 選擇您的角色（如 `PowerUserAccess`）。
   -   **Default Region**: 填入應用程式需要的區域（如 `ap-northeast-3`）。
2.  登錄 AWS SSO：

    ```
    aws sso login
    ```

    此命令會在默認瀏覽器中啟動 SSO 登錄流程，完成後應用將庫在 `~/.aws/cli/cache`。

3.  確認應用密鑰是否有效：

    ```
    aws sts get-caller-identity --profile default
    ```

### **2.3 配置 `~/.aws/config`**

下列是範例配置：

```
[profile PowerUserAccess-sso_account_id]
sso_session = default
sso_account_id = your_sso_account_id
sso_role_name = PowerUserAccess
region = ap-northeast-3

[sso-session default]
sso_start_url = https://your-sso-url.awsapps.com/start
sso_region = ap-northeast-3
sso_registration_scopes = sso:account:access

[default]
sso_session = default
sso_account_id = your_sso_account_id
sso_role_name = PowerUserAccess
sso_start_url = https://your-sso-url.awsapps.com/start
sso_region = ap-northeast-3
region = ap-northeast-3
```

### **2.4 添加必要依賴庫**

確保專案中包含下列 AWS SDK 依賴庫（以 Kotlin 為例）：

```
dependencies {
    implementation("software.amazon.awssdk:ssm:2.20.20") // SSM 客戶端
    implementation("software.amazon.awssdk:sso:2.20.20") // SSO 支援
    implementation("software.amazon.awssdk:ssooidc:2.20.20") // SSO OIDC 支援
    implementation("software.amazon.awssdk:auth:2.20.20") // 密鑰管理
}
```

### **2.5 本地測試應用程式**

#### **程式碼範例**

```
import software.amazon.awssdk.auth.credentials.DefaultCredentialsProvider
import software.amazon.awssdk.services.ssm.SsmClient
import software.amazon.awssdk.services.ssm.model.GetParameterRequest
import software.amazon.awssdk.regions.Region

object AWSUtil {
    private val ssmClient: SsmClient = SsmClient.builder()
        .credentialsProvider(DefaultCredentialsProvider.create()) // 使用 SSO 載入密鑰
        .region(Region.AP_NORTHEAST_3)
        .build()

    fun getParameter(parameterName: String): String {
        val request = GetParameterRequest.builder()
            .name(parameterName)
            .with

```
**3\. 正式環境部署（使用 EC2 IAM Role）**
-------------------------------

* 這部分是參考GPT寫的，因為實際我在建立EC2時似乎就已經有一個角色，所以就直接再多加一個AmazonSSMReadOnlyAccess權限就好了

### **3.1 創建 IAM Role**

1.  前往 AWS 管理控制台 > IAM > Roles。

2.  點擊 **Create role**，選擇信任實體為 **EC2**。

3.  編輯信任政策為：
    ```
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Principal": {
            "Service": "ec2.amazonaws.com"
          },
          "Action": "sts:AssumeRole"
        }
      ]
    }
    ```

4.  附加許可政策：

   -   使用 AWS 管理策略 `AmazonSSMReadOnlyAccess`，或
   -   自定義策略：
   ```
      {
         "Version": "2012-10-17",
         "Statement": [
           {
             "Effect": "Allow",
             "Action": [
               "ssm:GetParameter",
               "ssm:GetParameters",
               "ssm:GetParametersByPath",
               "ssm:DescribeParameters"
             ],
             "Resource": "arn:aws:ssm:ap-northeast-3:your-account-id:parameter/*"
           }
         ]
       }
   ```

### **3.2 為 EC2 分配角色**

1.  前往 AWS 管理控制台 > EC2 > Instances。
2.  選擇目標實例，點擊 **Actions > Security > Modify IAM role**。
3.  選擇剛剛創建的角色並保存。

* * * * *

**4\. 程式範例**
-----------------

1.  **使用 `DefaultCredentialsProvider` 加載憑證** AWS SDK 會自動識別在本地（SSO）和正式環境（IAM Role）的憑證來源。

2.  **讀取 SSM 參數**

   -   **參數配置**：將參數存儲為 SecureString。
   -   **讀取參數**：

   ```
       val password = AWSUtil.getParameter("/myapp/db/password")
       val jdbcUrl = AWSUtil.getParameter("/myapp/db/jdbcUrl")`
   ```
* * * * *

**5\. 注意事項與最佳實踐**
-----------------

1.  **限制 IAM Role 的許可範圍**：

   -   僅授權必要的 `ssm:GetParameter` 操作，並指定特定參數的 ARN。
2.  **分環境管理配置**：

   -   本地使用 AWS SSO 登錄，正式環境使用 EC2 IAM Role。
3.  **參數命名規範**：
   ```
   使用 `/<project>/<environment>/<parameter>` 格式存儲參數，例如 `/myapp/prod/db/password`。
   ```
4.  **監控與審核**：
   -   定期審核 IAM Role 的使用和 SSM 的存取記錄。