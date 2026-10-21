<a id="source-playbooks"></a>

# 來源 playbook

why 技能會針對每個可用的證據類別產生一個調查者，每個調查者會閱讀以下特定來源的 playbook。這些 playbook 是常見 MCP 的具體範例。請針對同一類別中的不同 MCP 進行調整。

| 類別 | Playbook | 它所記錄的範例 MCP |
|---|---|---|
| 來源控制歷史 | [`code-archaeology.md`](./sources/code-archaeology.md) | git、`gh` |
| 問題 / 票證追蹤 | [`linear.md`](./sources/linear.md) | Linear（可調整為 Jira、GitHub Issues、Plane、Shortcut） |
| 長篇檔案 | [`notion.md`](./sources/notion.md) | Notion（可調整為 Confluence、Google Docs、Coda） |
| 實時團隊聊天 | [`slack.md`](./sources/slack.md) | Slack（可調整為 Discord、Microsoft Teams、Mattermost） |
| 基礎設施可觀察性 | [`datadog.md`](./sources/datadog.md) | Datadog（可調整為 New Relic、Honeycomb、Grafana、Splunk） |
| 錯誤 / 例外追蹤 | [`sentry.md`](./sources/sentry.md) | Sentry（可調整為 Rollbar、Bugsnag、Airbrake） |
| 產品分析資料倉儲 | [`databricks.md`](./sources/databricks.md) | Databricks SQL（可調整為 Snowflake、BigQuery、ClickHouse、dbt） |

跨領域：

- [`incident-postmortem.md`](./sources/incident-postmortem.md)。如果目標程式碼看起來有防禦性（null 檢查、重試、逾時、速率限制、功能旗標、出口保護、OOM 處理），請加入此項。
