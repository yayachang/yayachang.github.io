---
title: "Android SignIn Exception"
meta_title: ""
description: "this is meta description"
date: '2024-11-29T20:00:00+08:00'
image: "/images/android-google-error.png"
categories: ["Android"]
author: "Yaya"
tags: ["android", "firebase", "google"]
draft: false
---
今天試著建立一個新專案，想把 Google 登入功能串接進 Android 專案。因為之前的專案已經實現過類似功能，就打算直接把程式碼複製過來用，並將原本的 Java 和 XML 改成 Kotlin 和 Compose，感覺應該不會太難。💻

確實，現在有 AI 協助，做這些轉換的效率提升不少。不過麻煩的是，近年來 Android 不斷棄用舊技術，加上 Gradle 設定格式頻繁變更，就算有 AI，也經常給出過時的專案配置，還是得自己調整才能正常運行。（每次都不禁想，新手要搞懂這些 Gradle 的設定到底有多難！🤯）

當程式碼和 UI 差不多完成時，開始測試 Google 登入功能，卻一直跳出 `APIException`。一開始想到可能是 Firebase 的 `google-services.json` 沒有放到 `app` 資料夾下📂；又想到新專案的 `applicationId` 必須和之前的專案對應一致，才能確保可以正常登入。等這些都確認無誤後，還是出現了 `APIException: 10` 錯誤，提示可能是 SHA 憑證沒有對應好。心想一直用同一台電腦，應該不至於有這種問題。🤔

後來嘗試用 Gradle 的 `signingReport` 指令檢查，才發現 debug 憑證的 SHA 值竟然被換掉了！😱 猜測可能是因為最近安裝了新的 Android Studio，或者硬碟空間不足清理暫存檔案時被刪除了。

最後重新在Firebase新增新的SHA憑證，重新下載json設定，重新Build後終於成功跑通！🎉 結案！


