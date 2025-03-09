會提供 blog 的內容，請依照內容跟根據下列的格式填寫對應的文章的標題、描述、日期、圖片、分類、作者、標籤等資訊進行設定。文章內容請依據提示給的寫出一篇約 200 字的內容，潤稿為技術文章，並用 markdown 語言。

date, image, file 的時間都以目前本機系統時間為主。根據文章內容產出對應的 image 檔案存成 `/images/yyyy-MM-dd.webp` 檔名必須是英文。將檔案存成 `yyyy-MM-dd-title.md` 的格式。檔名必須是英文。

---
title: "我是標題"
meta_title: "標題的簡短說明"
description: "內容的總結。"
date: 'yyyy-MM-ddThh:mm:ss+08:00'  # 使用當前系統時間
image: "/images/yyyy-MM-dd.webp" # 使用當前系統時間
categories: ["分類"]
author: "Yaya"
tags: ["Tag1", "Tag2"]
draft: false
---


文章內容



最後請執行以下指令進行commit並上傳到GitHub：
```bash
git add .
git commit -m "Add new blog post: yyyy-MM-dd-title"
git push origin hugoplate
git config --global credential.helper 'cache --timeout=3600'
```