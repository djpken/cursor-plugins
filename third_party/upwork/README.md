# Upwork

Cursor 外掛，透過 Upwork 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Upwork](https://www.upwork.com)。

在已登入的 Upwork 帳戶中，搜尋和篩選人才、發布和編輯工作、處理邀請和提案，以及管理有效契約。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Upwork**。
3. 點選 **安裝**，然後完成 Upwork 登入提示。

或在聊天中執行 `/add-plugin upwork`。

## MCP

```json
{
  "mcpServers": {
    "upwork": {
      "type": "http",
      "url": "https://mcp.upwork.com/mcp"
    }
  }
}
```

驗證採用 OAuth 2.1 與 Dynamic Client Registration。Cursor 會自行註冊，並在外掛連線時提示 Upwork 登入。不需要設定 API 金鑰或客戶 ID。

<a id="before-you-connect"></a>

## 連線前

只需要一個 Upwork 帳戶。沒有方案等級、夥伴計畫或等候名單。工具介面會根據您以客戶或自由工作者身份登入而有所不同。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 尋找人才（客戶） | 搜尋和篩選自由工作者，並管理人才列表 |
| 工作（客戶） | 發布、編輯和管理工作列表 |
| 邀請與提案 | 傳送邀請，並審閱或回應提案 |
| 尋找工作（自由工作者） | 搜尋工作，並起草和提交提案 |
| 契約 | 管理有效契約和工作交付 |

託管執行環境是工具名稱和結構的來源。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Upwork 使用者身份執行。
- 財務操作刻意不會透過 MCP 完成。資金託管、接受報價和支付里程碑都會在 upwork.com 上完成。
- GitHub 和 npm 上有幾個社群 `upwork-mcp` 專案；都不是由 Upwork 發布。此外掛使用 Upwork 自家的託管伺服器。

<a id="docs"></a>

## 檔案

- Upwork MCP Server: https://www.upwork.com/ai/mcp
- 伺服器 URL: https://mcp.upwork.com/mcp

標誌是 Upwork 的官方標誌，來自 `upwork` GitHub 組織。

<a id="license"></a>

## 授權

MIT
