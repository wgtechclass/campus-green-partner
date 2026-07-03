# SDD｜《校園綠夥伴：環境復甦任務》系統設計文件

## 1. 系統概述

本系統為一款網頁式任務遊戲，用於支援國小高年級四週校園環境行動課程。

系統包含學生端與教師端。學生端提供小隊建立、綠夥伴養成、任務回報、能力值累積與成果卡產生。教師端提供小隊管理、任務資料查看、熱點統計、成果卡檢視與 AI 分析輔助。

---

## 2. 技術架構

### 2.1 建議技術棧

| 層級 | 技術 |
|---|---|
| 前端 | HTML、CSS、JavaScript |
| 後端 | Google Apps Script Web App |
| 資料庫 | Google Sheets |
| 圖片儲存 | Google Drive，MVP 可暫時不用 |
| 部署 | Google Apps Script Web App 或 Netlify + GAS API |
| AI 分析 | MVP 採手動複製資料至 ChatGPT；第二版可串接 API |

### 2.2 系統架構

```text
學生 iPad / 教師電腦
        ↓
前端網頁 App
        ↓
Google Apps Script API
        ↓
Google Sheets 資料庫
        ↓
教師端儀表板 / AI 分析輔助
```

---

## 3. 系統模組

### 3.1 前端模組

| 模組 | 說明 |
|---|---|
| App Router | 控制頁面切換 |
| Team Module | 小隊建立、讀取小隊資料 |
| Pet Module | 綠夥伴選擇、命名、成長顯示 |
| Mission Module | 任務地圖、任務狀態 |
| Submission Module | 任務回報表單 |
| Score Module | 顯示能力值與徽章 |
| Result Card Module | 產生成果卡 |
| Teacher Dashboard Module | 教師端統計與管理 |

### 3.2 後端模組

| 模組 | 說明 |
|---|---|
| API Router | 接收 GET / POST 請求 |
| Team Service | 建立與查詢小隊 |
| Submission Service | 儲存任務回報 |
| Score Service | 計算能力值與徽章 |
| Mission Service | 管理任務週次與狀態 |
| Stats Service | 統計熱點、垃圾類型、任務完成率 |
| Result Service | 整理成果卡資料 |
| Settings Service | 管理班級、區域、任務設定 |

---

## 4. 前端頁面設計

### 4.1 頁面路由

| 路由 | 頁面 | 使用者 |
|---|---|---|
| `/` | 遊戲首頁 | 學生 |
| `/team` | 小隊建立頁 | 學生 |
| `/pet` | 綠夥伴選擇頁 | 學生 |
| `/map` | 任務地圖頁 | 學生 |
| `/mission/:week` | 任務回報頁 | 學生 |
| `/growth` | 綠夥伴成長頁 | 學生 |
| `/result` | 成果卡頁 | 學生 |
| `/teacher` | 教師端首頁 | 教師 |
| `/teacher/teams` | 小隊管理 | 教師 |
| `/teacher/submissions` | 回報紀錄 | 教師 |
| `/teacher/stats` | 熱點分析 | 教師 |
| `/teacher/results` | 成果卡管理 | 教師 |
| `/teacher/settings` | 系統設定 | 教師 |

---

## 5. 資料表設計

系統使用 Google Sheets 作為資料庫。建議建立以下工作表。

---

### 5.1 `Settings` 工作表

用途：儲存系統基本設定。

| 欄位 | 說明 |
|---|---|
| key | 設定名稱 |
| value | 設定值 |
| note | 備註 |

範例：

| key | value | note |
|---|---|---|
| teacherCode | 1234 | 教師管理碼 |
| currentWeek | 1 | 目前開放週次 |
| gameTitle | 校園綠夥伴：環境復甦任務 | 遊戲名稱 |

---

### 5.2 `Classes` 工作表

用途：儲存班級選項。

| 欄位 | 說明 |
|---|---|
| classId | 班級 ID |
| className | 班級名稱 |
| grade | 年級 |
| active | 是否啟用 |

---

### 5.3 `Areas` 工作表

用途：儲存校園調查區域。

| 欄位 | 說明 |
|---|---|
| areaId | 區域 ID |
| areaName | 區域名稱 |
| description | 區域說明 |
| active | 是否啟用 |

---

### 5.4 `Pets` 工作表

用途：儲存綠夥伴角色資料。

| 欄位 | 說明 |
|---|---|
| petId | 綠夥伴 ID |
| petName | 預設名稱 |
| description | 角色描述 |
| trait | 角色特色 |
| imageStage1 | 種子期圖片 |
| imageStage2 | 幼體期圖片 |
| imageStage3 | 成長期圖片 |
| imageStage4 | 守護期圖片 |
| active | 是否啟用 |

---

### 5.5 `Teams` 工作表

用途：儲存學生小隊資料。

| 欄位 | 說明 |
|---|---|
| teamId | 小隊唯一 ID |
| createdAt | 建立時間 |
| className | 班級 |
| groupNo | 組別 |
| teamName | 隊名 |
| selectedPetId | 選擇的綠夥伴 ID |
| customPetName | 學生自訂綠夥伴名稱 |
| stage | 目前進化階段 |
| stamina | 體力值 |
| scout | 偵查值 |
| cleanse | 淨化值 |
| wisdom | 智慧值 |
| influence | 影響力 |
| badges | 已獲得徽章 |
| lastUpdated | 最後更新時間 |

---

### 5.6 `Missions` 工作表

用途：儲存四週任務設定。

| 欄位 | 說明 |
|---|---|
| missionId | 任務 ID |
| week | 週次 |
| title | 任務名稱 |
| story | 任務故事 |
| instruction | 任務說明 |
| requiredFields | 必填欄位 |
| unlockCondition | 解鎖條件 |
| active | 是否啟用 |

---

### 5.7 `Submissions` 工作表

用途：儲存學生任務回報。

| 欄位 | 說明 |
|---|---|
| submissionId | 回報 ID |
| createdAt | 提交時間 |
| teamId | 小隊 ID |
| className | 班級 |
| groupNo | 組別 |
| week | 週次 |
| missionId | 任務 ID |
| areaName | 調查地點 |
| problemFound | 發現問題 |
| trashTypes | 垃圾類型 |
| amountLevel | 數量感受 |
| possibleReason | 可能原因 |
| improvementIdea | 改善想法 |
| photoNote | 照片說明 |
| photoUrl | 照片網址 |
| reflection | 小組反思 |
| scoreApplied | 是否已計分 |

---

### 5.8 `ScoresLog` 工作表

用途：儲存能力值變化紀錄。

| 欄位 | 說明 |
|---|---|
| logId | 紀錄 ID |
| createdAt | 建立時間 |
| teamId | 小隊 ID |
| sourceType | 分數來源，例如 teamCreated、submission、finalResult |
| sourceId | 對應資料 ID |
| staminaDelta | 體力變化 |
| scoutDelta | 偵查變化 |
| cleanseDelta | 淨化變化 |
| wisdomDelta | 智慧變化 |
| influenceDelta | 影響力變化 |
| note | 備註 |

---

### 5.9 `Results` 工作表

用途：儲存成果卡內容。

| 欄位 | 說明 |
|---|---|
| resultId | 成果卡 ID |
| createdAt | 建立時間 |
| teamId | 小隊 ID |
| mainArea | 主要調查地點 |
| mainFinding | 主要發現 |
| mainReason | 主要原因 |
| proposal | 改善提案 |
| slogan | 宣導口號 |
| commitment | 行動承諾 |
| finalStage | 最終型態 |
| summaryText | 系統整理後的成果摘要 |

---

## 6. API 設計

### 6.1 API 基本格式

前端透過 `fetch()` 呼叫 Google Apps Script Web App。

所有請求使用 JSON 格式。

```json
{
  "action": "actionName",
  "payload": {}
}
```

所有回應格式：

```json
{
  "success": true,
  "data": {},
  "message": ""
}
```

錯誤回應格式：

```json
{
  "success": false,
  "data": null,
  "message": "錯誤訊息"
}
```

---

## 7. API Actions

### 7.1 `getInitialData`

用途：取得首頁需要的基本資料。

回傳：

- 遊戲設定
- 班級清單
- 校園區域清單
- 綠夥伴清單
- 任務清單

---

### 7.2 `createTeam`

用途：建立小隊。

輸入：

```json
{
  "className": "六年一班",
  "groupNo": "第1組",
  "teamName": "綠光小隊",
  "selectedPetId": "pet_leaf_001",
  "customPetName": "阿葉隊長"
}
```

處理：

1. 檢查班級、組別、隊名是否存在
2. 建立 `teamId`
3. 寫入 `Teams`
4. 給予初始能力值
5. 解鎖甦醒徽章

---

### 7.3 `getTeam`

用途：查詢小隊資料。

輸入：

```json
{
  "teamId": "T202607030001"
}
```

回傳：

- 小隊基本資料
- 綠夥伴資料
- 能力值
- 徽章
- 任務完成狀態

---

### 7.4 `submitMission`

用途：提交任務回報。

輸入：

```json
{
  "teamId": "T202607030001",
  "week": 2,
  "missionId": "M2",
  "areaName": "操場",
  "problemFound": "操場旁邊有飲料杯和包裝紙",
  "trashTypes": ["飲料杯", "食物包裝"],
  "amountLevel": "中等",
  "possibleReason": "下課時很多人在操場活動，垃圾桶距離比較遠",
  "improvementIdea": "可以在操場入口放提醒標語",
  "photoNote": "照片中可看到操場旁的飲料杯",
  "photoUrl": "",
  "reflection": "我們發現垃圾常出現在人多的地方"
}
```

處理：

1. 建立 submissionId
2. 寫入 `Submissions`
3. 呼叫 `calculateScore`
4. 更新 `Teams` 能力值
5. 檢查徽章與進化階段
6. 回傳更新後的小隊資料

---

### 7.5 `getTeamSubmissions`

用途：取得某小隊的所有任務回報。

---

### 7.6 `getDashboardData`

用途：取得教師端儀表板資料。

回傳：

- 小隊總數
- 回報總數
- 各週完成數
- 各區域回報數
- 垃圾類型統計
- 班級進度
- 最新回報紀錄

---

### 7.7 `getStats`

用途：取得統計資料。

回傳：

- 校園區域熱點排行
- 垃圾類型排行
- 可能原因文字列表
- 改善建議文字列表
- 各組能力值排行

---

### 7.8 `createResultCard`

用途：建立成果卡。

輸入：

```json
{
  "teamId": "T202607030001",
  "mainArea": "操場",
  "mainFinding": "操場旁常有飲料杯和食物包裝",
  "mainReason": "下課人潮多，垃圾桶距離較遠",
  "proposal": "在操場入口設置提醒標語，並由高年級設計宣導口號",
  "slogan": "多走三步，校園乾淨一大步",
  "commitment": "我們小組願意每週提醒同學正確丟垃圾"
}
```

處理：

1. 寫入 `Results`
2. 增加影響力與淨化值
3. 解鎖校園守護徽章
4. 更新綠夥伴最終階段
5. 回傳成果卡資料

---

### 7.9 `getAllResults`

用途：教師端查看所有成果卡。

---

## 8. 能力值計算邏輯

### 8.1 初始能力值

小隊建立後：

| 能力值 | 初始值 |
|---|---:|
| 體力值 | 5 |
| 偵查值 | 0 |
| 淨化值 | 0 |
| 智慧值 | 0 |
| 影響力 | 5 |

### 8.2 任務提交計分

每次 `submitMission` 後依欄位完整度給分。

| 條件 | 加分 |
|---|---|
| 有填寫調查地點 | 體力值 +5 |
| 有填寫發現問題 | 偵查值 +10 |
| 有選擇垃圾類型 | 智慧值 +5 |
| 有填寫可能原因 | 智慧值 +10 |
| 有填寫改善想法 | 淨化值 +10 |
| 有填寫反思 | 影響力 +5 |

### 8.3 成果卡計分

建立成果卡後：

| 條件 | 加分 |
|---|---|
| 完成主要發現 | 偵查值 +10 |
| 完成原因分析 | 智慧值 +10 |
| 完成改善提案 | 淨化值 +15 |
| 完成宣導口號 | 影響力 +15 |
| 完成行動承諾 | 影響力 +10 |

---

## 9. 進化階段判斷

### 9.1 階段規則

| 階段 | 條件 |
|---|---|
| 種子期 | 小隊已建立 |
| 幼體期 | 至少完成 1 筆第 2 週任務回報 |
| 成長期 | 至少完成 1 筆第 3 週熱點分析任務 |
| 守護期 | 已建立成果卡 |

### 9.2 階段更新流程

每次提交任務或成果卡後執行：

1. 查詢小隊所有任務紀錄
2. 判斷是否達成新階段條件
3. 若達成，更新 `Teams.stage`
4. 若有對應徽章，更新 `Teams.badges`

---

## 10. 徽章解鎖邏輯

徽章儲存在 `Teams.badges` 欄位，可用逗號分隔或 JSON 字串。

### 10.1 解鎖規則

| 徽章 | 條件 |
|---|---|
| 甦醒徽章 | createTeam 成功 |
| 校園偵查徽章 | 完成第 2 週任務 |
| 熱點分析徽章 | 完成第 3 週任務 |
| 改善提案徽章 | 任務回報中有改善想法 |
| 校園守護徽章 | 完成成果卡 |
| 影響力徽章 | 成果卡中有宣導口號 |

---

## 11. 前端狀態管理

### 11.1 LocalStorage

學生端可使用 `localStorage` 儲存目前小隊資訊。

| key | 說明 |
|---|---|
| greenPartner_teamId | 小隊 ID |
| greenPartner_className | 班級 |
| greenPartner_groupNo | 組別 |
| greenPartner_teamName | 隊名 |

用途：

- 避免學生每次都重新輸入
- 讓同一台 iPad 可繼續查看小隊資料

### 11.2 注意事項

因為 iPad 可能共用，首頁應提供：

- 重新選擇小隊
- 清除目前小隊
- 進入已有小隊

---

## 12. 教師端統計設計

### 12.1 儀表板指標

| 指標 | 計算方式 |
|---|---|
| 小隊總數 | `Teams` 筆數 |
| 任務回報總數 | `Submissions` 筆數 |
| 完成果卡數 | `Results` 筆數 |
| 熱點最多區域 | 依 `areaName` 統計 |
| 最常見垃圾類型 | 依 `trashTypes` 統計 |
| 各週任務完成數 | 依 `week` 統計 |
| 各班參與狀況 | 依 `className` 統計 |

---

## 13. AI 輔助設計

### 13.1 MVP 不串接 AI API

第一版採取半自動 AI 協作流程：

1. 教師端統計頁整理學生資料
2. 系統提供「複製給 AI 分析」按鈕
3. 系統產出固定 prompt
4. 教師貼到 ChatGPT
5. 將 AI 分析結果貼回系統或用於課堂討論

### 13.2 AI 分析 Prompt 範本

系統可產生以下 prompt：

```text
你是一位國小高年級環境教育與綜合活動課程顧問。以下是學生在《校園綠夥伴：環境復甦任務》中提交的校園觀察資料。請協助整理：

1. 最常出現垃圾或環境問題的校園區域
2. 最常見的垃圾類型
3. 學生提出的可能原因
4. 學生提出的改善方法
5. 哪些改善方法最具體、最可行
6. 可以提供給學生的回饋與追問
7. 可以作為全校宣導的重點句子

請用繁體中文、國小高年級能理解的方式整理。
```

---

## 14. UI 設計原則

### 14.1 視覺風格

| 項目 | 設計方向 |
|---|---|
| 主色 | 綠色、淺黃色、天空藍 |
| 風格 | 可愛、清楚、任務感 |
| 字體 | 清楚易讀，適合國小學生 |
| 按鈕 | 大按鈕、明確圖示 |
| 卡片 | 使用任務卡、徽章卡、成果卡形式 |

### 14.2 學生端 UI 原則

- 每頁只做一件主要事情
- 表單欄位不要太多
- 任務說明要短
- 按鈕文字要明確
- 提交後要有立即回饋
- 能力值變化要可視化

### 14.3 教師端 UI 原則

- 資料表格清楚
- 統計結果可一眼理解
- 可複製資料
- 可依班級、組別、週次篩選
- 可查看單組完整歷程

---

## 15. 錯誤處理

### 15.1 前端錯誤

| 狀況 | 處理 |
|---|---|
| 未選班級 | 顯示「請選擇班級」 |
| 未輸入隊名 | 顯示「請輸入小隊名稱」 |
| 未選綠夥伴 | 顯示「請選擇一位綠夥伴」 |
| 表單必填未完成 | 標示未填欄位 |
| API 失敗 | 顯示「資料送出失敗，請再試一次」 |

### 15.2 後端錯誤

| 狀況 | 處理 |
|---|---|
| action 不存在 | 回傳錯誤訊息 |
| teamId 不存在 | 回傳小隊不存在 |
| Sheet 不存在 | 回傳系統設定錯誤 |
| 寫入失敗 | 回傳寫入失敗 |
| 欄位缺漏 | 回傳缺少必要欄位 |

---

## 16. 初始化函式設計

Google Apps Script 應提供 `initializeSheets()` 函式，用於建立所有必要工作表與標題列。

需要建立的工作表：

1. `Settings`
2. `Classes`
3. `Areas`
4. `Pets`
5. `Teams`
6. `Missions`
7. `Submissions`
8. `ScoresLog`
9. `Results`

初始化內容應包含：

- 預設班級範例
- 預設校園區域
- 預設綠夥伴
- 預設四週任務
- 預設教師管理碼

---

## 17. 開發順序建議

### Phase 1：資料基礎

1. 建立 Google Sheets
2. 撰寫 `initializeSheets()`
3. 建立 GAS API Router
4. 完成 `getInitialData`

### Phase 2：學生端核心

1. 首頁
2. 小隊建立
3. 綠夥伴選擇
4. 任務地圖
5. 任務回報
6. 成長頁

### Phase 3：計分與徽章

1. 能力值計算
2. 徽章解鎖
3. 進化階段更新
4. 分數紀錄寫入

### Phase 4：教師端

1. 教師端儀表板
2. 小隊資料表
3. 回報紀錄表
4. 熱點統計
5. 成果卡管理

### Phase 5：成果卡

1. 學生填寫成果卡
2. 系統產生成果摘要
3. 顯示成果卡頁面
4. 教師端查看所有成果卡

### Phase 6：AI 輔助

1. 教師端產生 AI 分析 prompt
2. 一鍵複製學生資料
3. 一鍵複製成果摘要 prompt
4. 保留未來 API 串接介面

---

## 18. 測試項目

### 18.1 學生端測試

| 測試項目 | 預期結果 |
|---|---|
| 建立小隊 | 成功寫入 Teams |
| 選擇綠夥伴 | Teams 記錄 selectedPetId |
| 提交任務 | 成功寫入 Submissions |
| 任務提交後 | 能力值更新 |
| 達成條件 | 徽章解鎖 |
| 完成果卡 | 寫入 Results |
| 返回成長頁 | 可看到最新能力值 |

### 18.2 教師端測試

| 測試項目 | 預期結果 |
|---|---|
| 輸入管理碼 | 可進入教師端 |
| 查看小隊 | 顯示所有 Teams |
| 查看回報 | 顯示所有 Submissions |
| 查看統計 | 區域與垃圾類型統計正確 |
| 查看成果 | 顯示所有 Results |
| 複製 AI prompt | 可取得完整文字 |

---

## 19. 安全與權限

### 19.1 MVP 權限設計

- 學生端不需登入
- 學生以班級與組別進入
- 教師端使用簡易管理碼
- 不使用個人資料
- 不收集學生個人姓名
- 不使用定位資訊

### 19.2 資料隱私

本系統僅儲存：

- 班級
- 組別
- 隊名
- 任務紀錄
- 環境觀察資料
- 小組成果

不儲存：

- 學生姓名
- 學號
- 個人照片
- 精準定位
- 個人帳號密碼

---

## 20. 未來擴充設計

第二版可擴充：

| 功能 | 說明 |
|---|---|
| AI API 自動回饋 | 依學生回報自動產生綠夥伴回饋 |
| 校園地圖模式 | 將校園區域做成互動地圖 |
| QR Code 任務點 | 學生掃描指定地點 QR Code 開啟任務 |
| 寵物動畫 | 根據階段顯示簡單動畫 |
| 成果牆 | 展示所有小組成果 |
| 海報產生器 | 根據成果卡產生宣導海報 |
| 全校報告產生器 | 自動整理成果給行政或家長 |
