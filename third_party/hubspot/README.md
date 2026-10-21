# HubSpot

Cursor 外掛，透過 HubSpot 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [HubSpot](https://www.hubspot.com)。

在已登入的 HubSpot 帳戶中搜尋和更新 CRM 記錄、處理活動與對話，以及管理行銷電子郵件草稿。

這是 HubSpot 的遠端 CRM MCP 伺服器，而非 [開發者 MCP 伺服器](https://developers.hubspot.com/docs/developer-tooling/local-development/developer-mcp/setup)，該伺服器用於在本機建立應用程式和 CMS 資產。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **HubSpot**。
3. 點選 **安裝** 並完成 HubSpot 登入提示。若可用，將客戶 ID 和金鑰留空以使用 Grok 的 HubSpot 應用；否則從你自己的應用設定（如下）。

或在聊天中執行 `/add-plugin hubspot`。

## MCP

```json
{
  "mcpServers": {
    "hubspot": {
      "type": "http",
      "url": "https://mcp.hubspot.com",
      "auth": {
        "CLIENT_ID": "${CLIENT_ID}",
        "CLIENT_SECRET": "${CLIENT_SECRET}"
      }
    }
  }
}
```

<a id="setup"></a>

## 設定

Cursor 正在推出 Grok 的 HubSpot 應用。若可用，將 **HubSpot 客戶 ID** 和 **HubSpot 客戶金鑰** 留空並略過以下步驟。如果 Cursor 報告外掛 OAuth 客戶未設定，請設定你自己的應用。

HubSpot 的遠端 MCP 伺服器需要一個專用的 MCP 認證應用，使用 OAuth（PKCE）。管理員或開發者必須先建立該應用，才能讓任何人連線。

1. 在 HubSpot 中，前往 **開發 → MCP 認證應用**（或開啟 [app.hubspot.com/l/mcp-auth-apps](https://app.hubspot.com/l/mcp-auth-apps/)）並點選 **建立 MCP 認證應用**。
2. 在該應用上註冊兩個 redirect URL：
   - 桌面：`http://localhost:8787/callback`
   - Web 和 Cloud Agents：`https://www.cursor.com/agents/mcp/oauth/callback`
3. 在 **Dashboard → 外掛 → 設定** 中，從該應用設定 **HubSpot 客戶 ID** 和 **HubSpot 客戶金鑰**。
4. 當 Cursor 提示時完成 HubSpot OAuth 登入。選擇要連線的帳戶並授予許可權。

在團隊 marketplace 中，管理員為所有人設定一次客戶 ID 和金鑰；每個成員仍需完成自己的 HubSpot OAuth 登入，因此工具呼叫會以該成員的 HubSpot 許可權執行。

應用上不會事先宣告 scopes，它們來自 MCP 伺服器的可用工具以及安裝使用者授予的許可權。如果 HubSpot 之後新增工具，使用者可能需要重新安裝以取得新的 scopes。

<a id="notes"></a>

## 注意事項

- 如果 HubSpot 帳戶啟用了 Sensitive Data，活動物件和對話資料會透過 MCP 伺服器被阻止（這不會影響標準 CRM API）。
- 客服對話對所有使用者可見；對話收件匣的存取遵循與 HubSpot 相同的團隊/使用者限制。

<a id="docs"></a>

## 文件

- 遠端 HubSpot MCP 伺服器：https://developers.hubspot.com/docs/apps/developer-platform/build-apps/integrate-with-the-remote-hubspot-mcp-server
- 伺服器 URL：https://mcp.hubspot.com

Logo 是 HubSpot 官方反色 favicon（品牌橙色底上的白色齒輪），取自 https://www.hubspot.com/hubfs/HubSpot_Logos/HubSpot-Inversed-Favicon.png.

<a id="license"></a>

## 授權

MIT
