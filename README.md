# 🛒 Belanja Allmart - 印尼看護雲端採購系統

一個專為家庭打造的跨語言對照採購網頁。旨在解決雇主與印尼看護之間因語言隔閡導致的食材、生活用品採購溝通痛點，並結合雲端資料庫實現無紙化、跨裝置的狀態管理。

---

## 🎯 核心痛點解決
- **語言雙語對照**：所有品項皆有印尼文（Bahasa Indonesia）與中文雙語對照，方便看護點選，也方便雇主核對。
- **防止溝通遺漏**：看護隨時可在手機上勾選需求並填寫備註，一鍵送出，告別傳統紙筆容易遺失的問題。
- **免伺服器成本**：100% 利用免費雲端資源（GitHub Pages + Google Sheets）架設，達成商業級全端系統的流暢體驗。

---

## 🛠️ 技術棧 (Tech Stack)
- **Frontend**: HTML5, CSS3 (響應式手機介面), JavaScript (Vanilla JS)
- **Deployment**: GitHub Pages
- **Backend / Database**: Google Apps Script (GAS) + Google Sheets API

---

## 📊 系統架構與資料流 (Data Flow)

本系統採用「雙前端、單後端整合」的免伺服器成本架構：

1. 看護端操作：於手機瀏覽器打開 `index.html` (GitHub Pages)，勾選品項、填寫備註並送出 (POST)。
2. 雇主端管理：於手機/電腦打開 `admin.html` (GitHub Pages)，輸入前端暗號解鎖後，即時讀取 (GET) 雲端清單。
3. 雲端資料庫：Google Apps Script (GAS) 作為後端 API 樞紐，負責處理：
   - 寫入新採購需求（來自看護端）
   - 讀取並打包歷史清單成 JSON 格式（來自雇主端）
   - 根據特定列號 (Row) 變更採購狀態欄位（來自雇主端）

---

## 🔗 重要連結備忘 (Links)
> ⚠️ *注意：請妥善保管此說明書，切勿流出 Web App URL 以防資料庫遭惡意寫入。*

- **前台網頁網址 (GitHub Pages)**: `https://elijah-lin0804.github.io/belanja-allmart/`
- **後台資料庫 (Google Sheets)**: `[請貼上您的 Google 試算表瀏覽連結]`
- **Google Web App URL (API)**: `https://script.google.com/macros/s/.../exec`

---

## 🚀 部署與維護指南 (Deployment & Maintenance)

### 1. 本地開發與更新
若需要修改食材品項、微調介面，請於本地修改 `index.html`，並使用 Git 進行版本控制：
```bash
# 檢查狀態
git status

# 提交並推送到 GitHub (Pages 會在 1 分鐘內自動更新)
git add index.html
git commit -m "update: 微調食材品項名稱"
git push

```
### 2. 後台 Apps Script 原始碼備忘
若不小心刪除 Google 試算表的指令碼，請重新將以下程式碼貼入「擴充功能 -> Apps Script」：
```bash
JavaScript
function doPost(e) {
  try {
    var params = JSON.parse(e.postData.contents);
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    sheet.appendRow([params.timestamp, params.items, params.notes, "未處理"]);
    return ContentService.createTextOutput(JSON.stringify({"status": "success"})).setMimeType(ContentService.MimeType.JSON);
  } catch(error) {
    return ContentService.createTextOutput(JSON.stringify({"status": "error", "message": error.toString()})).setMimeType(ContentService.MimeType.JSON);
  }
}
```

※ 重新部署時，務必將權限設為「所有人 (Anyone)」，並更新 index.html 中的網址。

## 📝 未來優化目標 (To-Do List)
- [ ] 試算表優化：建立「狀態」欄位的下拉式選單（未處理/採購中/已完成），並設定條件式格式自動變色。
- [ ] 主動通知機制：串接 LINE Notify，當看護送出清單時，雇主手機可即時收到 LINE 叮咚通知。
- [ ] 多語系擴充：預留未來若有英文或其他語系需求的彈性架構。

### 🔄 已完成優化紀錄 (Changelog)
- [x] (2026/05) 底部發送按鈕動態顯示邏輯：解決確認清單與選擇品項同時出現的視覺混淆，支援滾動自動隱藏與恢復。 
- [x] **前端優化**：新增底部選單「自動防遮擋與滾動動態隱藏邏輯」，全面提升行動裝置看品項時的視覺可視範圍。
- [x] **全端升級**：建立 `admin.html` 雇主專屬後台管理網頁。
  - 實作前端密碼鎖機制，保障家庭採購隱私。
  - 採用卡片式響應式排版，支援【未處理】、【採購中】、【已完成】一鍵即時同步 Google 試算表。
  - 升級後端 GAS，打通 `doGet` 與 `doPost` 的雙向 API 溝通。