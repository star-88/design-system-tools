# Figma Variable Binder

自動化完成三件事：將 Figma Variables Collection 的 token 綁定到指定主元件、透過 PrimeNG Aura 主題來源補全未綁定的顏色變數、並以繁體中文為每個變數撰寫簡潔說明。

---

## 開始前：確認必要資訊

若使用者尚未提供以下資訊，請逐一詢問後再執行：

| 資訊 | 說明 |
|------|------|
| Figma 檔案 URL | 要操作的 Figma 檔案連結 |
| Figma 元件名稱 | Main Component 的確切名稱（例如：`Button`） |
| PrimeNG 元件識別名稱 | 用於 GitHub 查詢的小寫名稱（例如：`button`、`toggleswitch`） |
| CSV 記錄檔路徑 | 預設為 `~/Desktop/figma-variable-log.csv` |

---

## 執行流程

### Step 1：將 Variables 綁定到主元件

1. 使用 `use_figma` 取得檔案中 Variables Collection 的所有變數。
2. 以元件名稱為前綴（小寫加點，例如 `button.`），篩選出屬於此元件的所有變數。
3. 取得 Figma 中對應的 Main Component 節點。
4. 根據變數名稱的後綴判斷要綁定到元件的哪個屬性，並透過 Figma Plugin API（`setBoundVariable`）執行綁定。

**命名後綴對應屬性規則（常見情形）：**

| 變數名稱後綴 | 對應 Figma 屬性 | 說明 |
|-------------|---------------|------|
| `background` / `bg` | `fills`（frame/container） | 背景色 |
| `color` / `foreground` / `text` | `fills`（文字圖層） | 文字色 |
| `border` / `borderColor` | `strokes` | 邊框色 |
| `radius` | `cornerRadius` | 圓角 |
| `shadow` | `effects` | 陰影 |

若命名後綴不在上表中，根據語意判斷最合適的屬性。若無法判斷，**暫停並詢問使用者**。

5. 記錄所有成功綁定的變數名稱，以供後續步驟使用。

---

### Step 2：補全未綁定的 Color 變數

1. 確認 Variables Collection 中屬於此元件的 **Color 類型**變數，是否仍有未綁定的項目。
2. 若有未綁定的 Color 變數：
   - 擷取 PrimeNG Aura 主題的參考檔案：
     ```
     https://raw.githubusercontent.com/primefaces/primeuix/main/packages/themes/src/presets/aura/{componentName}/index.ts
     ```
     將 `{componentName}` 替換為使用者提供的 PrimeNG 元件識別名稱。
   - 若該 URL 回傳 404 或無法取得內容 → **立即暫停，告知使用者並詢問後續做法，不要自行假設繼續執行**。
   - 解析檔案內的 token 名稱與語意角色，判斷每個未綁定 Color 變數應對應到元件的哪個屬性。
   - 執行剩餘的 Color 變數綁定。
3. 若所有 Color 變數均已綁定，跳過此步驟，直接進入 Step 3。

---

### Step 3：以繁體中文撰寫變數說明

1. 從 PrimeNG V20 官方文件擷取此元件的 design token 說明。
   - 建議搜尋路徑：`https://primeng.org/theming` 或元件專屬頁面。
2. 對照每個變數名稱，找出對應的官方說明。
3. 將說明改寫為**繁體中文**，遵守以下原則：
   - **簡潔**：一句話說清楚，不超過 25 個字
   - **白話**：使用設計師熟悉的詞彙，避免程式術語
     - ✅「按鈕在 hover 狀態下的背景顏色」
     - ❌「button component background fill property on hover state」
   - **情境感**：說明這個變數在元件的哪個部位或哪個互動狀態下生效
4. 使用 `use_figma` 將說明寫入 Figma 中該變數的 description 欄位。

---

## 任務結束後：回報摘要

完成三個步驟後，向使用者報告：

**1. 是否新增了新變數**
明確說明這次任務是否在該元件的 Variables Collection 中**新建**了任何變數（與「綁定既有變數」不同）。

**2. 綁定摘要**
列出成功綁定的變數總數與清單。

**3. 未完成項目（若有）**
說明哪些變數未能綁定，以及原因。

---

## CSV 任務紀錄

每次任務完成後，將結果追加寫入 CSV 紀錄檔（若不存在則先建立並加上標題列）。

**欄位定義：**

```
執行日期,元件名稱,變數名稱,操作類型,變數說明(繁中)
```

**操作類型說明：**

| 值 | 意義 |
|----|------|
| `新增` | 此次任務新建了這個變數 |
| `綁定` | 既有變數，此次成功綁定到元件 |
| `補充說明` | 變數原本已綁定，此次補寫了繁中說明 |

**範例資料列：**

```
2026-05-24,Button,button.background,綁定,按鈕的預設背景顏色
2026-05-24,Button,button.hoverBackground,新增,按鈕在 hover 狀態下的背景顏色
2026-05-24,Button,button.borderColor,補充說明,按鈕邊框的顏色
```

---

## 重要原則

- 每次執行只處理**一個指定元件**，不批量處理整個 Figma 檔案。
- PrimeNG 元件識別名稱一律由使用者提供，不要自行推測。
- 遇到 Figma API 錯誤，清楚告知使用者錯誤內容，並詢問是否繼續。
- 無法判斷的綁定對應關係，一律暫停詢問，不擅自決定。
- **所有新建變數必須對應到 PrimeNG Aura 官方 token**；若元件某個屬性在 PrimeNG token 清單中找不到對應項目，**不建立變數、不強行綁定**，直接跳過，並在任務摘要的「未完成項目」中說明原因。
