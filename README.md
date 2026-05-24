# Design System Tools

設計系統工具集，提供 Claude Code 自訂指令，協助團隊將 Figma Variables 與 PrimeNG Aura token 系統化管理。

---

## 目錄結構

```
.claude/
└── commands/
    └── figma-variable-binder.md   # Figma 變數綁定指令
```

---

## 快速開始

### 1. Clone 專案

```bash
git clone https://github.com/star-88/design-system-tools.git
cd design-system-tools
```

### 2. 確認已安裝 Claude Code

前往 [claude.ai/code](https://claude.ai/code) 下載，並確認已開啟 Figma MCP 插件。

### 3. 在 Claude Code 中使用指令

在專案目錄下開啟 Claude Code，輸入：

```
/project:figma-variable-binder
```

依照提示提供以下資訊即可開始執行：

| 資訊 | 範例 |
|------|------|
| Figma 檔案 URL | `https://www.figma.com/design/xxxx/...` |
| Figma 元件名稱 | `Button`、`Checkbox` |
| PrimeNG 元件識別名稱 | `button`、`checkbox` |
| CSV 記錄檔路徑 | `~/Desktop/figma-variable-log.csv`（預設） |

---

## 指令說明

### `figma-variable-binder`

自動化完成三件事：

1. **綁定**：將 Variables Collection 中的 token 綁定到 Figma 主元件（Main Component）
2. **補全**：對照 PrimeNG Aura 官方 token，補建缺漏的 Color 變數並綁定
3. **說明**：以繁體中文為每個變數撰寫說明，寫入 Figma description 欄位

> **重要原則**：所有新建變數必須對應到 PrimeNG Aura 官方 token，找不到對應項目則不建立、不強行綁定。

---

## 更新指令規則

```bash
# 拉取最新版本
git pull

# 修改規則
# 編輯 .claude/commands/figma-variable-binder.md

# 提交並推送
git add .claude/commands/figma-variable-binder.md
git commit -m "update: 說明修改原因"
git push
```

其他成員執行 `git pull` 即可同步最新規則。

---

## 注意事項

- **不要 commit** 任何含有 token、API key 等敏感資訊的檔案
- 修改指令規則前，建議先在 GitHub Issue 討論，避免衝突
- CSV 紀錄檔（`figma-variable-log.csv`）為本地產出，不需納入版控

---

## 相關資源

- [PrimeNG Aura 主題 token 來源](https://github.com/primefaces/primeuix/tree/main/packages/themes/src/presets/aura)
- [PrimeNG V20 官方文件](https://primeng.org/theming)
- [Claude Code 文件](https://claude.ai/code)
