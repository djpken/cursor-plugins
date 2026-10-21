# Intercom

Cursor 外掛，透過 Intercom 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Intercom](https://www.intercom.com)。

搜尋對話和聯絡人、查詢公司，以及在已登入的 Intercom 工作區中列出、搜尋、建立或更新 Help Center 文章。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor Settings → Plugins**。
2. 搜尋 **Intercom**。
3. 點選 **Install**，然後完成 Intercom 登入提示。

或在聊天中執行 `/add-plugin intercom`。

## MCP

```json
{
  "mcpServers": {
    "intercom": {
      "type": "http",
      "url": "https://mcp.intercom.com/mcp"
    }
  }
}
```

驗證方式為針對 Intercom 的 OAuth 2.0，使用 Dynamic Client Registration (DCR) 和 PKCE。Cursor 會自行註冊，並在外掛連線時提示 Intercom 登入，不需要設定 API 金鑰或客戶 ID。

<a id="regions"></a>

## 區域

此外掛指向美國端點。Intercom MCP 適用於美國和歐洲託管的工作區；澳洲目前尚未支援。

| 區域 | 工作區 URL | MCP URL |
| --- | --- | --- |
| 美國 | `app.intercom.com` | `https://mcp.intercom.com/mcp` |
| 歐洲 | `app.eu.intercom.com` | `https://mcp.eu.intercom.com/mcp` |

如果你的工作區為歐洲託管，請在安裝後將 `mcp.json` 中的 `url` 更改為 `https://mcp.eu.intercom.com/mcp`。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Intercom 使用者身份執行，且不能超出該使用者許可權。
- 所需的 Intercom 許可權包括讀取使用者/公司和對話，以及 Help Center 工具的讀取/寫入文章許可權。
- Intercom 伺服器也支援 Bearer-token 驗證，但此外掛使用建議的 OAuth 流程。

<a id="docs"></a>

## 檔案

- Intercom MCP 指南：https://developers.intercom.com/docs/guides/mcp
- 伺服器 URL（美國）：https://mcp.intercom.com/mcp

標誌為 Intercom 的品牌標記（Simple Icons）位於白色圖塊上，使用 Intercom 藍色 `#1F8FFF`。

<a id="license"></a>

## 授權

MIT
