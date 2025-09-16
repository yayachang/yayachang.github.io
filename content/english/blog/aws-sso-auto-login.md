---
title: "自動化登入 AWS SSO Login"
meta_title: "自動刷新 AWS SSO Session：排程腳本全攻略"
description: "透過簡單的 Bash 腳本搭配 cron 排程，自動偵測並重新登入 AWS SSO，避免手動重複登入的麻煩。"
date: '2025-03-10T08:00:00+08:00'
image: "/images/aws-sso-auto-login.svg"
categories: ["技術筆記"]
author: "Yaya"
tags: ["AWS", "SSO", "自動化"]
draft: false
---

AWS SSO 的使用者最怕的就是 **Session 一過期就得重新開瀏覽器登入**。如果你一天要切換多個 AWS 帳號或環境，頻繁的手動驗證真的會讓人崩潰。於是我動手寫了一個簡單的排程腳本，只要 Session 過期就自動觸發 `aws sso login`，讓工作流程回到自動化的懷抱。

## Step 1. 確認 AWS SSO Profile 設定

AWS CLI 會把 SSO 設定寫在 `~/.aws/config`。先檢查你要自動刷新的 Profile 名稱，確保後續腳本能對應正確。

```bash
cat ~/.aws/config
```

常見的設定範例如下，`sso_session` 與 `sso_account_id` 等欄位都應該是你已經設定好的值：

```ini
[profile default]
sso_session = my-sso-session
sso_account_id = 123456789012
sso_role_name = AdministratorAccess
region = ap-northeast-1
output = json
```

把想要自動登入的 profile 名稱記起來，下一步會把它寫入腳本中。

## Step 2. 建立自動刷新腳本

接著建立 `aws-sso-refresh.sh`，原理是先執行一個需要憑證的 AWS 指令（這裡用 `aws s3 ls`），若回傳錯誤就呼叫 `aws sso login` 重新登入。

```bash
#!/bin/bash
# 設定要使用的 SSO profile 名稱
PROFILE="default"

# 嘗試呼叫一個 S3 指令確認 session 是否有效
aws s3 ls --profile "$PROFILE" >/dev/null 2>&1
STATUS=$?

if [ $STATUS -ne 0 ]; then
  echo "$(date '+%Y-%m-%d %H:%M:%S') - Session expired or not found. Re-login..."
  aws sso login --profile "$PROFILE"
else
  echo "$(date '+%Y-%m-%d %H:%M:%S') - Session still valid."
fi
```

上面使用 `$(date '+%Y-%m-%d %H:%M:%S')` 讓日誌格式更清楚，也記得把 `PROFILE` 改成你自己的名稱。

## Step 3. 開啟執行權限

```bash
chmod +x aws-sso-refresh.sh
```

## Step 4. 加入 crontab 排程

設定每 6 小時執行一次腳本，並把輸出寫入 log 方便追蹤。

```bash
crontab -e
```

在編輯器中加入以下排程：

```cron
0 */6 * * * /home/youruser/aws-sso-refresh.sh >> /home/youruser/aws-sso-refresh.log 2>&1
```

換成你自己的路徑即可。若你常常忘記維護 log，可以改成 `journalctl` 或設定 logrotate，避免檔案無限長大。

## 注意事項與延伸

- **第一次仍需手動登入**：AWS CLI 會打開瀏覽器讓你授權，之後腳本才有辦法自動刷新。
- **SSO 最長 7 天會過期**：就算排程跑得再勤勞，只要超過 7 天沒有手動登入，AWS 還是會要求重新授權。
- **多 Profile 情境**：若你需要刷新的 profile 不只一個，可以將上述腳本包成函式，或是以陣列迭代多個 profile 名稱。
- **錯誤監控**：建議搭配簡單的通知機制，例如當 `aws sso login` 返回非 0 狀態時，寄送 Email 或串接 Slack Webhook，避免在服務中斷時才發現。

搞定以上四個步驟之後，就能把「每六小時確認一次 Session」這種無聊的事情交給機器。希望這份筆記也能幫你省下不少時間，把心力放在真正重要的事情上！
