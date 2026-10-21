# HighLevel

Cursor 外掛，透過 HighLevel 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [HighLevel](https://www.gohighlevel.com)（亦以 LeadConnector 品牌稱之）。

在 HighLevel 子帳戶中查詢和更新 CRM 資料：聯絡人、對話與訊息、機會與管道、行事曆與預約、付款、部落格、電子郵件模板及社交貼文。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **HighLevel**。
3. 點選 **安裝**，然後完成 LeadConnector 登入提示。

或在聊天中執行 `/add-plugin highlevel`。

## MCP

```json
{
  "mcpServers": {
    "highlevel": {
      "type": "http",
      "url": "https://services.leadconnectorhq.com/mcp/cursor/v2"
    }
  }
}
```

驗證採用 OAuth，搭配 Dynamic Client Registration 與 PKCE。Cursor 會自行註冊，並在外掛連線時開啟 LeadConnector 登入：登入、選擇要暴露的子帳戶（位置）、然後核准。不需要設定 Private Integration Token 或 `locationId` 標頭。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

HighLevel 的客戶特定 `/v2` 端點暴露了一組小型統一工具，可涵蓋完整的操作目錄：

| 工具 | 用途 |
| --- | --- |
| `list_locations` | 列出此連線可使用的子帳戶，或返回唯一繫結的子帳戶 |
| `search_operations` | 依意圖探索操作，包括記錄搜尋及建立、更新或刪除動作 |
| `describe_operation` | 檢視操作的輸入、示例 payload、所需許可權範圍及安全資訊 |
| `execute_operation` | 執行操作，受 OAuth 許可權範圍、許可權及內建安全檢查之限制 |

這些工具背後有超過 550 個操作，涵蓋 38 個領域，包括聯絡人、對話與訊息、機會與管道、行事曆與預約、付款、產品與商店、發票與估價、社交規劃器、部落格、電子郵件、表單與調查、漏斗、工作流程、Voice AI、自訂物件與欄位，以及使用者。可使用的操作取決於授予的許可權範圍。

託管 runtime 是工具名稱與 schema 的來源真相。

<a id="notes"></a>

## 注意事項

- 伺服器既可讀也可寫，包括髮送訊息、更新聯絡人與機會、建立貼文。僅核准代理所需的最小存取權，並先在非生產子帳戶上測試。
- 每個請求都會針對登入時選擇的一個子帳戶執行。使用多子帳戶選項安裝的代理商，可在同一聊天中切換所選的子帳戶。
- 敏感及不可逆的操作會經過額外的確認與安全檢查。
- 此外掛使用 HighLevel 的 Cursor 端點（`/mcp/cursor/v2`）。通用端點（`/mcp/`）暴露較小的命名工具集合。

<a id="docs"></a>

## 文件

- MCP 伺服器說明文章：https://help.leadconnectorhq.com/support/solutions/articles/155000008185
- 開發者文件：https://marketplace.gohighlevel.com/docs/other/mcp/
- 伺服器 URL：https://services.leadconnectorhq.com/mcp/cursor/v2

標誌為 HighLevel 官方箭頭標記，取自 gohighlevel.com 上的文字標誌。

<a id="license"></a>

## 授權

MIT
