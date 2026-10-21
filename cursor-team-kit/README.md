<a id="cursor-team-kit-plugin"></a>

# Cursor Team Kit 外掛

適用於 CI、程式碼審查、發布與測試可靠性的內部風格工作流程。此套件設計為即插即用，無需整合第三方服務。

<a id="installation"></a>

## 安裝

```bash
/add-plugin cursor-team-kit
```

<a id="components"></a>

## 元件

<a id="skills"></a>

### 技能

| 技能 | 說明 |
|:------|:------------|
| `loop-on-ci` | 監控 CI 執行並針對失敗專案進行迭代，直到檢查透過為止 |
| `review-and-ship` | 執行結構化審查、提交變更並開啟 PR |
| `pr-review-canvas` | 產生互動式 HTML PR 導覽，包含標註與分類後的差異 |
| `verify-this` | 使用基準/處理產出物來證明或反駁宣告，並給出明確結論 |
| `control-cli` | 建置或調整本地測試框架，以驅動並分析互動式 CLI 或 TUI |
| `control-ui` | 建置或調整本地瀏覽器/CDP 測試框架，用於 Web 或 Electron UI |
| `make-pr-easy-to-review` | 清理雜亂的 PR 歷史、改善描述並新增審查者指引 |
| `run-smoke-tests` | 執行 Playwright 冒煙測試並分類失敗專案 |
| `fix-ci` | 找出失敗的 CI 工作、檢查紀錄並套用針對性修復 |
| `new-branch-and-pr` | 建立新分支、完成工作並開啟拉取請求 |
| `get-pr-comments` | 擷取並摘要來自活躍拉取請求的審查意見 |
| `check-compiler-errors` | 執行編譯與型別檢查指令並回報失敗專案 |
| `what-did-i-get-done` | 將指定時間範圍內建立的 commit 摘要為簡潔的狀態更新 |
| `weekly-review` | 產生每週已發布工作回顧，包含錯誤修復、技術債務與新增功能亮點 |
| `fix-merge-conflicts` | 解決合併衝突、驗證建置/測試並摘要決策 |
| `deslop` | 移除 AI 產生的低品質程式碼並清理程式碼風格 |
| `workflow-from-chats` | 從對話中提取持久的工作偏好，轉化為技能、規則或檔案 |
| `thermo-nuclear-code-quality-review` | 執行異常嚴格的維護性審查（code-judo、1k 行規則、義大利麵式程式碼、邊界） |

<a id="agents"></a>

### 代理

| 代理 | 說明 |
|:------|:------------|
| `ci-watcher` | 監控 GitHub Actions 執行並返回簡潔的透過/失敗摘要 |
| `thermo-nuclear-code-quality-review` | 執行熱核程式碼品質評分標準的任務子代理，針對差異進行評估 |

<a id="rules"></a>

### 規則

| 規則 | 說明 |
|:-----|:------------|
| `typescript-exhaustive-switch` | 要求對 union/enum 進行窮舉 switch 處理 |
| `no-inline-imports` | 將匯入保留在模組頂層，以確保可讀性與一致性 |

<a id="license"></a>

## 授權條款

MIT
