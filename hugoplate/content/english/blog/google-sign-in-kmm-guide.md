---
title: "Google Sign-In 配置指南 (KMM 專用)"
meta_title: "Google Sign-In 配置指南 (KMM 專用)"
description: "釐清在 KMM (Kotlin Multiplatform) 專案中實作 Google Sign-In 時，Android 與 iOS 平台對於 Client ID 的混淆點與正確配置方式"
date: '2025-12-17T07:10:00+08:00'
image: "/images/2025-12-17T07:10:00.png"
categories: ["Android", "Kotlin"]
author: "Yaya"
tags: ["android", "kotlin", "kmm", "google-sign-in", "authentication"]
draft: false
---

# Google Sign-In 配置指南 (KMM 專用)

這份文件旨在釐清在 KMM (Kotlin Multiplatform) 專案中實作 Google Sign-In 時，Android 與 iOS 平台對於 Client ID 的混淆點與正確配置方式。

## 核心觀念：身分 (Identity) vs. 接收者 (Audience)

Google Sign-In 涉及兩個核心驗證步驟：
1.  **你是誰 (Identity)**：驗證發起請求的 App 是否合法。
2.  **給誰用 (Audience)**：登入後產生的 Token 是要發給哪個後端伺服器使用的。

無論是 Android 還是 iOS，最終產生的 Token **接收者** 通常都是同一個（你的後端伺服器），因此都需要指向 **Web Client ID**。

---

## 1. Google Cloud Console 設定

在開始寫程式碼之前，請確保 Console 設定正確：

| 憑證類型 | 必要性 | 用途 | 備註 |
| :--- | :--- | :--- | :--- |
| **Web Client ID** | **必須** | 代表「接收 Token 的後端伺服器」。 | 所有平台通用。 |
| **Android Client ID** | **必須** | 綁定 Package Name 與 **SHA-1** 簽章。 | 用於 Android 驗證 App 身分。 |
| **iOS Client ID** | **必須** | 綁定 **Bundle ID** (如 `com.app.id`)。 | 用於 iOS 驗證 App 身分。 |

---

## 2. Android 平台實作

Android 的運作機制是「隱性驗證，顯性指定接收者」。

* **機制**：Google Play Services 會在背景自動檢查 App 的 Package Name 和簽章 (SHA-1) 是否符合 Console 上的 **Android Client ID** 設定。
* **程式碼需求**：你 **不需要** 在程式碼中填入 Android Client ID。你需要填的是 **Web Client ID**，告訴 Google 這個 Token 是要開給誰的。

### 常見錯誤 (Error 28444)
* ❌ **錯誤**：在 `setServerClientId` 填入 Android Client ID。
    * *原因*：Android Client ID 不能當作 Audience。
* ❌ **錯誤**：SHA-1 簽章不符 (Debug/Release key 不同)。

### KMM 設定範例 (`androidApp`)

**local.properties:**
```properties
# 這裡是填 Web Client ID (.apps.googleusercontent.com)
GOOGLE_CLIENT_ID_WEB="your-web-client-id.apps.googleusercontent.com"
```
