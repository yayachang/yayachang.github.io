---
title: "只要commit md檔案，Github Action自動部署上傳更新網站"
meta_title: ""
description: "this is meta description"
date: '2024-11-18T23:25:00+08:00'
image: "/images/hugo_github.png"
categories: ["Github", "Hugo"]
author: "Yaya"
tags: ["Github", "Hugo"]
draft: false
---

Hugoplate 主題已大幅簡化 Hugo 網站的部署流程。
只需完成上述設定，之後每次提交新的 Markdown 文件，
GitHub Actions 都會自動構建並部署您的網站。
這篇文章的誕生正是基於這套便利的流程， 希望它能幫助更多人快速上手 Hugo 和 GitHub Pages 的自動部署！

# **踩雷提醒**

不想每次都手動運行 `npm run build` 並複製 `public` 到 `yayachang.github.io`

其實 Hugoplate 已經預先配置好 **GitHub Actions**，可以輕鬆實現自動部署。

只需將 `themes/hugoplate` 資料夾中的所有文件 **完整推送** 到遠端（包含隱藏的 `.github/workflows/main.yml`），

即可啟用自動部署功能。注意，直接使用 Mac Finder 複製時，可能會漏掉隱藏文件。

我還有把branch main改成hugoplate ,因為想保留原本有留在main的資料 

branches: ["hugoplate"]


---


此篇就是透過Github Action Deploy完成的！ 🎉 。
