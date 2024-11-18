---
title: "怎麼用Github加上Hugo架個人網站？"
meta_title: ""
description: "this is meta description"
date: '2024-11-17T14:06:22+08:00'
image: "/images/hugo_github.png"
categories: ["Github", "Hugo"]
author: "Yaya"
tags: ["Github", "Hugo"]
draft: false
---

最近看到有人用 GitHub + Hugo 架設網站，覺得有趣就試試看。
花了半天研究，加上 AI 幫忙，終於弄出個樣子。
用了 HugoPlate 和 GitHub，成功架設了個人網站。
記錄下安裝步驟，避免哪天突然壞掉。
原來這樣的技術還能拿來開課，真是開眼界！原來市場這麼需要這些簡單易懂實用的技術。
學到不少，也算是一次有趣的嘗試！

## **Mac 環境 Hugo 安裝操作手冊**

## **安裝 Homebrew**
- 確保您的 Mac 環境已安裝 Homebrew。如果尚未安裝，請按照以下步驟進行操作。

  - **Homebrew 官方網站：**  
    [https://brew.sh/](https://brew.sh/)

  - **安裝指令：**
    ```bash
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    ```

---

## **安裝 Hugo**
- 安裝完 Homebrew 後，您可以使用 Homebrew 安裝 Hugo。

  - **指令：**
    ```bash
    brew install hugo
    ```

---

## **選擇 Hugo 主題**
- 推薦主題：[Hugoplate](https://themes.gohugo.io/themes/hugoplate/)  
  此主題簡單易用且適合新手。

---

## **安裝 Go**
- Hugoplate 主題需要 Go 編譯器。使用 Homebrew 安裝 Go：

  - **指令：**
    ```bash
    brew install go
    ```

---

## **建立 Hugo 專案**
- 按照官網的步驟，開始建立 Hugo 專案。

  - **指令：**
    ```bash
    hugo new site my-hugoplate-site
    ```

- 進入專案目錄：
  ```bash
  cd my-hugoplate-site

## **初始化版本控制並下載主題**
- 初始化 Git，並將 Hugoplate 主題作為子模組添加：

  - **指令：**
    ```bash
    git init
    git submodule add https://github.com/zeon-studio/hugoplate.git themes/hugoplate
    ```

---

## **安裝 Hugoplate 所需的依賴**
- 進入主題目錄，執行以下指令以完成安裝和配置：

  - **進入主題目錄：**
    ```bash
    cd themes/hugoplate
    ```

  - **運行項目初始化：**
    ```bash
    npm run project-setup
    ```

  - **安裝依賴：**
    ```bash
    npm install
    ```

  - **啟動本地開發伺服器：**
    ```bash
    npm run dev
    ```

- 此時，您的開發伺服器將在本地運行，預設地址為 [http://localhost:1313](http://localhost:1313)。

---

## **產生文章**
- 使用以下指令新增一篇文章：

  - **指令：**
    ```bash
    hugo new blog/your-post-title.md
    ```

- 編輯該檔案，撰寫文章內容。

---

## **部署到 GitHub Pages**
- 將網站部署到 GitHub Pages：

  - **步驟 1：** 修改專案根目錄的 `hugo.toml` 配置文件中的 `baseURL`：
    ```toml
    baseURL = "https://yayachang.github.io/"
    ```

  - **步驟 2：** 生成靜態文件：
    ```bash
    hugo
    ```

  - **步驟 3：** 將 `public` 資料夾中的內容放入您的 GitHub Repository（例如 `yayachang.github.io`），然後執行以下指令：
    ```bash
    cd public
    git init
    git add .
    git commit -m "Deploy site"
    git push -u origin main
    ```

---

## **訪問您的網站**
- 部署完成後，您可以通過以下地址訪問您的網站：  
  [https://yayachang.github.io/](https://yayachang.github.io/)

---

完成！ 🎉 現在您已成功安裝並部署了一個 Hugo 網站！如需進一步修改，請返回專案並更新內容後重新部署。
