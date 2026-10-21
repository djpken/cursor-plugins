# MailerLite

Cursor 外掛，透過 MailerLite 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [MailerLite](https://www.mailerlite.com)。

在已登入的 MailerLite 帳戶中，新增和更新訂閱者、組織群組與區段、建立和檢查活動，以及管理表單、自動化和 Webhook。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **MailerLite**。
3. 點選 **安裝**，然後完成 MailerLite 登入提示。

或在聊天中執行 `/add-plugin mailerlite`。

## MCP

```json
{
  "mcpServers": {
    "mailerlite": {
      "type": "http",
      "url": "https://mcp.mailerlite.com/mcp"
    }
  }
}
```

驗證方式為 OAuth。當外掛連線時，Cursor 會提示 MailerLite 登入，不需要貼上 API 金鑰或令牌。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 訂閱者 | 新增和更新訂閱者、讀取活動，並計算受眾數量 |
| 群組與區段 | 建立群組、管理成員資格，並查詢區段 |
| 活動 | 建立、更新、排程和檢查活動及其收件者 |
| 表單與自動化 | 管理表單、建立自動化，並讀取自動化活動 |
| Webhook 與匯入 | 管理 Webhook 並執行訂閱者匯入 |

託管執行環境是工具名稱和結構的來源。連線後，將 `get_subscriber_count` 作為僅讀的冒煙測試執行。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 MailerLite 使用者身份執行。
- MCP 伺服器處於 beta 階段，因此工具目錄可能變動。
- 伺服器提供約 40 個工具。MailerLite 指出 Cursor 免費方案將單一聯結器限制為 40 個有效工具，因此您可能會看到「超出總工具限制」的警告。

<a id="docs"></a>

## 文件

- MailerLite MCP 伺服器：https://developers.mailerlite.com/mcp
- 如何連線 MailerLite 的 MCP：https://www.mailerlite.com/help/how-to-connect-mailerlites-mcp
- 伺服器 URL：https://mcp.mailerlite.com/mcp

Logo 是 MailerLite 官方標誌，來自 `mailerlite` GitHub 組織。

<a id="license"></a>

## 授權

MIT
