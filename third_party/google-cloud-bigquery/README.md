# Google Cloud BigQuery

Cursor 外掛，透過 Google Cloud BigQuery 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Google Cloud BigQuery](https://docs.cloud.google.com/bigquery/docs/use-bigquery-mcp)。

探索資料集和資料表，並執行 SQL 查詢。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Google Cloud BigQuery**。
3. 點選 **安裝**，然後完成 Google Cloud BigQuery 登入提示。

或在聊天中執行 `/add-plugin google-cloud-bigquery`。

## MCP

```json
{
  "mcpServers": {
    "google-cloud-bigquery": {
      "type": "http",
      "url": "https://bigquery.googleapis.com/mcp"
    }
  }
}
```

驗證方式為 OAuth。當外掛連線時，Cursor 會提示 Google Cloud BigQuery 登入，不需要設定客戶端 ID 或個人存取令牌。

<a id="before-you-connect"></a>

## 連線前

您需要一個已啟用 BigQuery API 的 Google Cloud 專案，且您的 Google 帳號需要該專案的 MCP 工具使用者角色 (`roles/mcp.toolUser`)，以及 BigQuery 作業使用者與資料檢視者角色。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 探索 | 列出資料集和資料表，並讀取其元資料與結構 |
| 查詢 | 執行僅讀 SQL，包括 BigQuery ML 函式 |
| 寫入 | 當帳號允許時執行 DML 和 DDL |
| 作業 | 取得結果並取消正在執行的查詢作業 |

託管執行環境是工具名稱與結構的來源基準。

<a id="notes"></a>

## 注意事項

- 查詢費用會計入傳遞給工具的專案 ID。
- 管理員可以使用 IAM 拒絕政策來阻止具寫入能力的 `execute_sql` 工具，同時保留僅讀查詢可用。
- Grok 也提供此連線器，使用服務身份驗證；此外掛僅設定使用者 OAuth 流程。

<a id="docs"></a>

## 文件

- 使用 BigQuery MCP 伺服器：https://docs.cloud.google.com/bigquery/docs/use-bigquery-mcp
- 工具參考：https://docs.cloud.google.com/bigquery/docs/reference/mcp
- 伺服器 URL：https://bigquery.googleapis.com/mcp

標誌為 Google Cloud 架構圖示集中的官方 BigQuery 產品圖示，位於帶有白色邊距的白色方塊上。

<a id="license"></a>

## 授權

MIT
