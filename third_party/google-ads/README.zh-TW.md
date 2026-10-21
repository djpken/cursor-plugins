# Google Ads

Cursor 外掛，透過 Cursor 的遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將 Grok Bot 連線到 [Google Ads](https://ads.google.com)。

使用 GAQL 報告帳戶和廣告活動、研究關鍵字和目標物件，並進行廣告活動變更。每次變更都會先以 `validate_mutate`（`validateOnly`）檢查，再由 `mutate` 套用。

<a id="install"></a>

## 安裝

1. 開啟 **Grok Bot 設定 → 外掛**。
2. 搜尋 **Google Ads**。
3. 點選 **安裝**，然後完成 Google 登入提示。

## MCP

```json
{
  "mcpServers": {
    "google-ads": {
      "type": "http",
      "url": "https://api.cursor.com/rest-mcp/google-ads/mcp"
    }
  }
}
```

驗證採用 OAuth 2.0，對 Google 使用 `https://www.googleapis.com/auth/adwords` 範圍。當外掛連線時，Grok Bot 會提示 Google 登入。

<a id="tools"></a>

## 工具

- **報告：** `list_accessible_customers`、`search`、`search_fields`、`list_billing`、`get_operation`
- **規劃：** `keyword_planner`、`reach_planner`、`audience_insights`、`benchmarks`、`suggest`、`generate_assets`
- **變更：** `validate_mutate`、`mutate`、`recommendation_action`、`experiment_action`、`upload_conversions`

<a id="docs"></a>

## 檔案

- Google Ads API：https://developers.google.com/google-ads/api/docs/start
- GAQL：https://developers.google.com/google-ads/api/docs/query/overview

標誌為官方 Google Ads 產品圖示，放置在白色底板上並加上留白，以便在介面中清楚呈現。
