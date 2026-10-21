# Ashby

Cursor 外掛，透過 Ashby 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Ashby](https://www.ashbyhq.com)。

搜尋候選人和職位、準備面試、檢視招募流程狀態和待處理任務，以及在已登入的 Ashby 工作區中執行招募動作。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Ashby**。
3. 點選 **安裝**，然後完成 Ashby 登入提示。

或在聊天中執行 `/add-plugin ashby`。

## MCP

```json
{
  "mcpServers": {
    "ashby": {
      "type": "http",
      "url": "https://mcp.ashbyhq.com/mcp/v1"
    }
  }
}
```

認證是針對 Ashby 的 OAuth 2.0。當外掛連線時，Cursor 會提示 Ashby 登入，不需要設定 API 金鑰。Ashby 支援動態客戶註冊。

<a id="before-you-connect"></a>

## 連線前

組織管理員必須先在 **Admin → Organization Setup → Opt-In Features** 下啟用 MCP 伺服器切換，組織中的任何人才能連線。

啟用後，Elevated Access 使用者可以連線自己的 Ashby 帳戶。MCP 伺服器適用於所有 Ashby 方案，但不適用於僅 Analytics 的組織。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Ashby 使用者身份執行，且不能超過該使用者許可權。
- Ashby MCP 會限制每分鐘的請求與工具預算單位；若遇到限制，請參閱 Ashby 的說明檔案。
- MCP 工具輸入與輸出可能隨時變動。如需穩定契約，請使用 Ashby 的 [公開 API](https://developers.ashbyhq.com/reference/introduction)。

<a id="docs"></a>

## 說明檔案

- Ashby MCP 伺服器（beta）：https://docs.ashbyhq.com/ashby-mcp-server-beta
- 伺服器 URL：https://mcp.ashbyhq.com/mcp/v1

標誌是 Ashby 官方應用程式圖示（品牌藍色底上的白色襯線 A），取自 https://www.ashbyhq.com/favicon.png.

<a id="license"></a>

## 授權

MIT
