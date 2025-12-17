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

**Kotlin Code:**
```kotlin
// 讀取 Web Client ID
val serverClientId = BuildConfig.GOOGLE_CLIENT_ID_WEB

GoogleSignInOptions.Builder(GoogleSignInOptions.DEFAULT_SIGN_IN)
    .requestIdToken(serverClientId) // ✅ 正確：填入 Web Client ID
    .requestEmail()
    .build()
```

---

## 3. iOS 平台實作

iOS 的運作機制比較直觀，需要同時明確宣告「我是誰」和「我要傳給誰」。

* **機制**：iOS App 必須使用 iOS Client ID 來發起登入請求（證明自己是合法的 iOS App），同時指定 Web Client ID 作為 serverClientID 以便後端驗證。

### 常見錯誤
* ❌ **錯誤**：把 clientID 設定成 Web Client ID。
    * *後果*：Google 會報錯，因為 Web Client ID 沒有綁定 iOS 的 Bundle ID。
* ❌ **錯誤**：忘記設定 serverClientID。
    * *後果*：雖然能登入，但拿到的 Token 無法被後端或 Android 通用驗證。

### KMM 設定範例 (`iosApp`)

**iOSApp.swift:**
```swift
import GoogleSignIn

// ... 在設定 GIDConfiguration 時

let config = GIDConfiguration(
    clientID: "your-ios-client-id.apps.googleusercontent.com", // ✅ 填入 iOS Client ID (發起登入)
    serverClientID: "your-web-client-id.apps.googleusercontent.com" // ✅ 填入 Web Client ID (接收 Token)
)
```

---

## 4. 總結對照表

| 設定項目 | Android 專案 | iOS 專案 |
| :--- | :--- | :--- |
| Console 需建立 | Android ID + Web ID | iOS ID + Web ID |
| 程式碼主要變數 | GOOGLE_CLIENT_ID_WEB (變數名) | clientID |
| 變數實際值 (Value) | Web Client ID | iOS Client ID |
| Server ID (接收端) | 同上 (填 Web ID) | serverClientID (填 Web ID) |
| 關鍵口訣 | 後台設兩組，Code 裡只填 Web ID | 後台設兩組，Code 裡分別填 iOS ID 和 Web ID |
