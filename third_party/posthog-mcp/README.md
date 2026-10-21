# PostHog MCP

Cursor 外掛，透過 PostHog 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [PostHog](https://posthog.com)。

從聊天中查詢產品與網站分析、執行 HogQL/SQL 對你的專案、發布與逐步推出功能旗標、建立與分析實驗、分類錯誤追蹤問題，以及處理 Session Replay、調查、儀錶板、紀錄與 CDP 目的地。

這是僅限 URL 的聯結器，連到 PostHog 託管的 MCP 伺服器。PostHog 也在 marketplace 發布其更豐富的外掛（`posthog`，包含內建的技能、指令與 hooks）；請安裝其中一個，不要同時安裝兩者。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor Settings → Plugins**。
2. 搜尋 **PostHog MCP**。
3. 點選 **Install**，然後完成 PostHog 登入提示。

或在聊天中執行 `/add-plugin posthog-mcp`。

## MCP

```json
{
  "mcpServers": {
    "posthog": {
      "type": "http",
      "url": "https://mcp.posthog.com/mcp"
    }
  }
}
```

驗證方式是 OAuth。Cursor 在外掛連線時會提示 PostHog 登入，不需要設定 client ID 或 API 金鑰。每個使用者用自己的 PostHog 帳號登入，PostHog 的驗證伺服器會將連線導到該帳號正確的資料區域（美國或歐盟）。

<a id="before-you-connect"></a>

## 連線前

你需要一個 PostHog 帳號（[註冊](https://app.posthog.com/signup)）。連線 MCP 伺服器並呼叫其工具是免費的；標準的 PostHog 使用與計費仍適用於你的查詢所消耗的資料。有些工具會在內部執行 LLM（例如，調查回應摘要與追蹤摘要），並可能以 PostHog AI 支出計費；這些工具僅在組織設定中啟用 AI 資料處理時才會暴露。詳見 [FAQ](https://posthog.com/docs/model-context-protocol/faq)。

一個連線以一個帳號登入，並有一個活動的組織與專案；`switch-organization` 與 `switch-project` 會改變活動的上下文。

<a id="what-agents-can-do"></a>

## 代理能做什麼

| 類別 | 能力 |
| --- | --- |
| 產品分析 | `insight-query` 執行趨勢、漏斗、留存、路徑、生命週期與黏性查詢，並有 `query-trends`、`query-funnel`、`query-retention`、`query-paths` 及其 `-actors` 變體供直接存取；`insights-list`、`insight-get`、`insight-create` 與 `insight-update` 管理已儲存的洞察 |
| SQL 與資料結構 | `execute-sql` 對專案執行 HogQL 查詢；`read-data-schema` 描述可供查詢的事件、屬性與 warehouse 表格 |
| 儀錶板 | `dashboards-get-all`、`dashboard-get`、`dashboard-create`、`dashboard-update` 與 tile 工具（`dashboard-create-tile`、`dashboard-reorder-tiles`）建立與編輯儀錶板 |
| 功能旗標 | `feature-flag-get-all`、`feature-flag-get-definition`、`create-feature-flag`、`update-feature-flag`、`feature-flag-enable` / `feature-flag-disable`、`feature-flag-set-release-condition-rollout`、`feature-flag-roll-out-to-everyone` 與 `scheduled-changes-*` 用於定時推出 |
| 實驗 | `experiment-list`、`experiment-get`、`experiment-create`、`experiment-create-from-prompt`、`experiment-launch`、`experiment-pause`、`experiment-end`、`experiment-ship-variant` 與 `experiment-results-get` |
| 錯誤追蹤 | `query-error-tracking-issues-list`、`query-error-tracking-issue` 與 `query-error-tracking-issue-events` 讀取問題與堆疊追蹤；`error-tracking-issues-partial-update`、`error-tracking-issues-assign-partial-update`、`error-tracking-issues-merge-create` 以及指派、群組、嚴重度與抑制規則工具管理分類 |
| Session Replay | `query-session-recordings-list` 與 `session-recording-get` 尋找與檢查錄製；播放列表工具將其群組 |
| 調查 | `surveys-get-all`、`survey-get`、`survey-create`、`survey-launch`、`survey-stop`、`survey-stats`、`surveys-responses-list` 與 `surveys-summarize-responses-create` |
| 紀錄與追蹤 | `query-logs`、`logs-patterns`、`logs-anomalies-scan` 與 `logs-alerts-*`；`query-apm-spans`、`apm-trace-get` 與 `apm-spans-*` 聚合用於分散式追蹤 |
| 網站分析 | `query-web-overview`、`query-web-stats`、`query-web-vitals`、`web-analytics-weekly-digest` 與熱點圖工具 |
| 人物與同類群組 | `persons-list`、`persons-retrieve`、`persons-property-set`、`cohorts-list`、`cohorts-create` 與 `cohorts-add-persons-to-static-cohort-partial-update` |
| CDP 與資料倉儲 | `cdp-functions-list`、`cdp-functions-create`、`cdp-function-templates-list` 與目的地與轉換的修訂工具；`external-data-sources-list`、`data-warehouse-source-setup`、`view-create` 與 `view-run` 用於 warehouse 來源與已儲存的檢視 |
| 工作區與檔案 | `organizations-get`、`projects-get`、`switch-organization`、`switch-project`、`docs-search` 與 `generate-app-url` |

伺服器暴露超過 70 個類別中的 1,000 多個工具，包括 AI 可觀測性、警示、註記、計費、對話、端點、notebook、工作流程與更多。請參閱 [工具參考](https://posthog.com/docs/model-context-protocol/tools) 的完整列表。託管的 runtime 是工具名稱與結構的來源真相。

<a id="notes"></a>

## 注意事項

- 許多工具會寫入你的專案：它們會建立、更新、啟用與刪除旗標、實驗、洞察、同類群組、調查與 CDP 函式，而 `persons-bulk-delete` 與 `session-recording-bulk-delete` 會移除資料。在讓代理執行每個寫入之前，請檢視它，並注意來自查詢結果中不受信任內容的 prompt injection。
- 寫入會進入活動專案。在讓代理更改旗標或實驗之前，確認連線所在的組織與專案。
- 工具呼叫會透過 PostHog API 執行，並共用其每個團隊的速率限制。MCP 伺服器代理到你的 PostHog 例項，不會儲存你的分析資料。
- Cursor 會逐一註冊每個工具（PostHog 的「tools mode」）。其他客戶端可能預設為單一 `exec` 工具；檔案描述 `mode`、`readonly`、`features` 與 `tools` 查詢引數用於限定一個 session。

<a id="docs"></a>

## 檔案

- MCP 概覽：https://posthog.com/docs/model-context-protocol
- 工具參考：https://posthog.com/docs/model-context-protocol/tools
- FAQ 與進階設定：https://posthog.com/docs/model-context-protocol/faq
- API 參考：https://posthog.com/docs/api
- 伺服器 URL：https://mcp.posthog.com/mcp

Logo 是 PostHog 的官方標誌。

<a id="license"></a>

## 授權

MIT
