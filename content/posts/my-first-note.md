---
date: 2026-05-26T14:52:39+09:00
draft: false
title: '極簡的 Hugo 初始化流程'
featured: true
---

這是一份極簡的 Hugo 初始化流程，專門為「不想處理相依性地獄、只想寫 Markdown」的你設計。

### 1. 安裝 Hugo (確保你安裝的是 `extended` 版本，這是最穩定的)
*   **Mac:** `brew install hugo`
*   **Windows:** `winget install Hugo.Hugo.Extended`
*   **驗證:** 輸入 `hugo version`，看到 `extended` 字樣即成功。

### 2. 建立新專案 (在終端機執行)
```bash
# 建立一個名為 my-site 的資料夾
hugo new site my-site

# 進入資料夾
cd my-site

# 初始化 Git
git init
```

### 3. 加入一個極簡主題 (這是最穩定的方式)
我們選擇一個最基礎、不會壞掉的主題：
```bash
# 加入 Ananke 主題作為子模組 (Submodule)
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke

# 在設定檔中啟用主題
echo "theme = 'ananke'" >> hugo.toml
```

### 4. 建立你的第一篇 AI 筆記
```bash
# 建立一個新的 Markdown 檔案
hugo new content/posts/my-first-note.md
```
此時，打開 `content/posts/my-first-note.md`，你會看到裡面有預設的設定，直接在下方貼上 AI 給你的內容即可。

### 5. 本地測試 (這一步非常重要，可以確保 AI 產出的格式沒問題)
```bash
hugo server -D
```
執行後，打開瀏覽器訪問 `http://localhost:1313`，你就能看到網站原型了。

### 6. 設定 GitHub Actions (自動化部署)
在你的專案根目錄下建立目錄與檔案：
`mkdir -p .github/workflows`
`touch .github/workflows/deploy.yml`

用編輯器打開 `.github/workflows/deploy.yml`，貼上以下這段：

```yaml
name: Deploy Hugo
on:
  push:
    branches:
      - main
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: true
          fetch-depth: 0
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: 'latest'
      - name: Build
        run: hugo --minify
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

### 7. 推送到 GitHub
```bash
git add .
git commit -m "Initial commit"
# 在 GitHub 建立好你的 Repository，然後執行：
git remote add origin https://github.com/你的帳號/你的倉庫名.git
git branch -M main
git push -u origin main
```

---

### 未來你的「每日通勤」工作流：
1. **AI 產出內容。**
2. **存入檔案：** `hugo new content/posts/2023-10-27.md`，貼入內容。
3. **提交更新：**
   ```bash
   git add .
   git commit -m "Update daily note"
   git push origin main
   ```
4. **結束。** 接下來你就不用管了，GitHub 幾十秒內會自動編譯並更新網站。

**提示：** 如果你發現 `git submodule` 很煩人，你可以把 `.gitmodules` 檔案刪掉，並把 `themes/ananke` 的內容直接複製一份放進 `themes/` 資料夾，這樣這就是一個完全「純淨」的倉庫，連子模組的同步問題都不會有。