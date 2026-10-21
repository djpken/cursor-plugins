# Circleback

Cursor 外掛，透過 Circleback 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Circleback](https://circleback.ai)。

搜尋會議、逐字稿、行動專案、行事曆事件和電子郵件，並在已登入的 Circleback 帳戶中查詢人員和公司。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Circleback**。
3. 點選 **安裝**，然後完成 Circleback 登入提示。

或在聊天中執行 `/add-plugin circleback`。

## MCP

```json
{
  "mcpServers": {
    "circleback": {
      "type": "http",
      "url": "https://circleback.ai/api/mcp"
    }
  }
}
```

驗證是針對 Circleback 的 OAuth 2.0，使用 Dynamic Client Registration (DCR) 和 PKCE。Cursor 會自行註冊，並在外掛連線時提示 Circleback 登入。不需要設定 API 金鑰或客戶 ID。

<a id="notes"></a>

## 備註

- 工具呼叫以授權連線的 Circleback 使用者身份執行，且不能超過該使用者許可權。
- 代理可以搜尋會議和逐字稿、提取備註和行動專案、搜尋行事曆和電子郵件，以及查詢與您的 Circleback 歷史相關的人員或公司。

<a id="docs"></a>

## 文件

- Circleback MCP: https://support.circleback.ai/en/articles/13249081-circleback-mcp
- 伺服器 URL: https://circleback.ai/api/mcp

標誌是 Circleback 官方 apple-touch 圖示，來自 https://circleback.ai/apple-touch-icon.png.

<a id="license"></a>

## 授權

MIT
