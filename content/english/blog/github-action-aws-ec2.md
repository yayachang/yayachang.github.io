---
title: "GitHub Actions 自動化部署到 Amazon EC2"
meta_title: "利用 Github Actions自動部署到Amazon EC2"
description: "設定 GitHub Actions，自動將 Ktor 專案的 app.jar 上傳至 S3，再將其部署到 EC2 並重啟應用程式。"
date: '2025-01-20T08:00:00+08:00'
image: "/images/github-actions-aws-ec2-s3.png"
categories: ["技術筆記"]
author: "Yaya"
tags: ["Github", "AWS","自動化"]
draft: false
---


從 GitHub Actions 自動化部署到 Amazon EC2 並整合 S3 的流程



---


以下說明如何設定 GitHub Actions，自動將 Ktor 專案的 `app.jar` 上傳至 S3，再將其部署到 EC2 並重啟應用程式。

---
####  Github的deply.yml

```yml
name: Build and Deploy to EC2
on:
  push:
    branches:
      - main  # push to main branch

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      # Step 1: check code
      - name: Checkout Code
        uses: actions/checkout@v3

      # Step 2: jav enviroment
      - name: Setup Java
        uses: actions/setup-java@v3
        with:
          distribution: 'temurin'
          java-version: '17'

      # Step 3: build shadowJar
      - name: Build with Gradle
        run: ./gradlew shadowJar

      # Step 4: upload app.jar to S3
      - name: Upload app.jar to S3
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_REGION: ${{ secrets.AWS_REGION }}
        run: |
          aws s3 cp build/libs/app.jar s3://your-s3/app.jar

      # Step 5: EC2 download app.jar then restart
      - name: Restart Ktor Service using SSM
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_REGION: ${{ secrets.AWS_REGION }}
        run: |
          aws ssm send-command \
            --instance-ids "your-ec2-instance-id" \
            --document-name "AWS-RunShellScript" \
            --comment "Deploy and Restart Ktor Service" \
            --parameters 'commands=["sudo aws s3 cp s3://your-s3/app.jar /opt/your-app/app.jar", "sudo systemctl restart ktor.service"]'
```

#### 1. **IAM 角色與權限設定**
為確保 GitHub Actions 與 AWS 資源間的安全通信，需建立 IAM 角色並分配必要的權限。

##### **GitHub Actions 使用者的 IAM 權限政策**
以下是允許 GitHub Actions 使用者操作 SSM 與 S3 的範例政策：

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ssm:SendCommand",
        "ssm:GetCommandInvocation"
      ],
      "Resource": [
        "arn:aws:ec2:<region>:<account_id>:instance/<instance_id>",
        "arn:aws:ssm:<region>:<account_id>:document/AWS-RunShellScript"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject",
        "ssm:SendCommand",
        "ssm:GetCommandInvocation"
      ],
      "Resource": [
        "arn:aws:s3:::<bucket_name>/*",
        "arn:aws:ssm:*:*:document/AWS-RunShellScript",
        "arn:aws:ssm:*:*:instance/<instance_id>"
      ]
    }
  ]
}
```

##### **存取金鑰管理**
為 GitHub Actions 使用者建立存取金鑰：
1. 前往 **IAM > 使用者 > GitHub Actions User > 安全認證**。
2. 建立存取金鑰，用於程式化存取。
3. 在 GitHub Actions 中使用以下環境變數：
    - `AWS_ACCESS_KEY_ID`
    - `AWS_SECRET_ACCESS_KEY`

> **注意：** 請妥善保存存取金鑰。如果遺失，需停用舊金鑰並重新建立。

---

#### 2. **S3 Bucket 設定**
確保 S3 Bucket 可用於存取與儲存 `app.jar` 檔案。

##### **S3 存取政策**
為 EC2 實例設定權限，以存取 S3 Bucket 中的檔案。

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<account_id>:role/<instance_role>"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::<bucket_name>/*"
    }
  ]
}
```

##### **訪問控制**
根據需求，設定 IP 白名單或其他安全控制，以確保資源的安全性。

---

#### 3. **EC2 實例角色與權限**
為 EC2 實例分配角色，確保具備 SSM 和 S3 的相關權限。

##### **EC2 實例的權限政策**
將以下政策附加至 EC2 實例的角色：

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ssm:SendCommand",
        "ssm:GetCommandInvocation",
        "ssmmessages:CreateControlChannel",
        "ssmmessages:OpenControlChannel",
        "ssmmessages:CreateDataChannel",
        "ssmmessages:OpenDataChannel"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetBucketLocation",
        "s3:GetObject"
      ],
      "Resource": [
        "arn:aws:s3:::<bucket_name>",
        "arn:aws:s3:::<bucket_name>/*"
      ]
    }
  ]
}
```

---

#### 4. **GitHub Actions 部署流程**
以下為 GitHub Actions 的 YAML 範例，實現自動化部署：

```yaml
name: 部署到 EC2

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout 程式碼
        uses: actions/checkout@v3

      - name: 設定 AWS CLI
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: <region>

      - name: 上傳至 S3
        run: |
          aws s3 cp ./build/libs/app.jar s3://<bucket_name>/app.jar

      - name: 部署至 EC2
        run: |
          aws ssm send-command \
            --document-name "AWS-RunShellScript" \
            --targets "Key=instanceIds,Values=<instance_id>" \
            --parameters '{"commands":["aws s3 cp s3://<bucket_name>/app.jar /path/to/app.jar", "systemctl restart my-app"]}' \
            --region <region>
```

---

#### 5. **安全性最佳實踐**
- 使用最小權限政策，確保所有角色僅具備必要的操作權限。
- 定期旋轉存取金鑰，減少安全風險。
- 使用 AWS CloudTrail 監控所有活動，及時檢測異常。

---

此流程可實現從 GitHub Actions 到 Amazon EC2 的高效、安全自動化部署。請將 `<region>`、`<account_id>`、`<bucket_name>`、`<instance_id>` 和 `<instance_role>` 等占位符替換為實際 AWS 資源的詳細資訊。
