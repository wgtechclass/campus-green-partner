# 校園綠夥伴：環境復甦任務

這是依照 `校園綠夥伴_SPEC.md` 與 `校園綠夥伴_SDD.md` 製作的 MVP 第一版。

## 已建立檔案

- `index.html`：網頁入口
- `styles.css`：畫面樣式
- `app.js`：學生端、教師端、任務、計分、徽章與成果卡邏輯
- `apps-script/Code.gs`：Google Apps Script 後端與 Google Sheets 初始化程式

## 本機試跑

直接用瀏覽器開啟 `index.html` 即可試用。未連接 Google Sheets 時，資料會先存在同一台裝置的瀏覽器中。

預設教師管理碼：

```text
1234
```

## 學生端功能

- 建立小隊
- 找回小隊
- 選擇與命名綠夥伴
- 查看四週任務地圖
- 提交任務回報
- 自動計算能力值
- 自動解鎖徽章
- 查看綠夥伴成長狀態
- 建立與更新成果卡

## 教師端功能

- 查看小隊總數、回報數、成果卡數
- 查看小隊列表
- 查看任務回報
- 查看熱點與垃圾類型統計
- 查看成果卡
- 複製 AI 分析 prompt
- 設定目前開放週次與教師管理碼

## 接 Google Sheets

1. 建立一份 Google 試算表。
2. 在試算表中開啟 Apps Script。
3. 將 `apps-script/Code.gs` 的內容貼入 Apps Script 編輯器。
4. 執行 `initializeSheets()`，授權後會建立必要工作表與預設資料。
5. 部署為 Web App。
6. 複製 `config.example.js` 為 `config.local.js`。
7. 將部署網址填入 `config.local.js` 中的 `window.GREEN_PARTNER_API_URL`。

```js
window.GREEN_PARTNER_API_URL = "你的 Google Apps Script Web App 網址";
```

`config.local.js` 不應提交到 GitHub，避免公開可寫入 Google Sheets 的後端入口。本專案已在 `.gitignore` 排除這個檔案。

## 備註

第一版不使用學生個人帳號、不使用 GPS、不串接 AI API。教師端的 AI 功能採手動複製資料到 ChatGPT 的流程。
