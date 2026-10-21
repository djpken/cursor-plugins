# Webull

Cursor 外掛，透過 Webull 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Webull](https://developer.webull.com/apis/docs/AI-friendly-Resources/mcp/)。

檢視帳戶、持倉、訂單、觀察列表和市場資料。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Webull**。
3. 點選 **安裝**，然後完成 Webull 登入提示。

或在聊天中執行 `/add-plugin webull`。

## MCP

```json
{
  "mcpServers": {
    "webull": {
      "type": "http",
      "url": "https://api.webull.com/mcp"
    }
  }
}
```

驗證方式為 OAuth。當外掛連線時，Cursor 會提示 Webull 登入，因此不需要設定客戶 ID 或個人存取令牌。

<a id="before-you-connect"></a>

## 連線前

你需要一個 Webull 帳戶。登入時，你可以選擇要暴露的帳戶和功能群組（帳戶資訊、訂單查詢、市場資料、證券主檔）。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 帳戶 | 餘額、帳戶列表和持倉 |
| 訂單 | 未完成訂單、訂單歷史和訂單詳細資訊 |
| 市場資料 | 股票、加密貨幣、期貨和事件合約：報價、柱狀圖、逐筆交易、排名 |
| 研究 | 公司簡介、分析師評級、申報檔案、財務報表和基金資料 |
| 觀察列表 | 建立和管理觀察列表 |

託管執行環境是工具名稱和結構的來源。

<a id="notes"></a>

## 注意事項

- 託管伺服器對交易僅提供讀取功能：訂單下單、修改和取消僅在 Webull 本地 MCP 伺服器中可用。
- 香港帳戶使用一個獨立的地區端點，此外掛不會設定該端點。

<a id="docs"></a>

## 文件

- Webull Cloud MCP: https://developer.webull.com/apis/docs/AI-friendly-Resources/mcp/
- 伺服器 URL: https://api.webull.com/mcp

標誌為 Webull 官方標誌。

<a id="license"></a>

## 授權

MIT
