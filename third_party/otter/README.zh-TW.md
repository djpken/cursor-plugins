# Otter.ai

Cursor 外掛，透過 Otter.ai 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Otter.ai](https://otter.ai)。

搜尋您的 Otter 會議歷史，並將完整逐字稿拉入編輯器，以總結決策並提取行動專案。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Otter.ai**。
3. 點選 **安裝**，然後完成 Otter.ai 登入提示。

或在聊天中執行 `/add-plugin otter`。

## MCP

```json
{
  "mcpServers": {
    "otter": {
      "type": "http",
      "url": "https://mcp.otter.ai/mcp"
    }
  }
}
```

驗證方式為 OAuth。Cursor 會在外掛連線時提示 Otter 登入。Otter 不提供公開 API 金鑰，因此 OAuth 是唯一途徑。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 能力 |
| --- | --- |
| 身份 | 確認已登入的 Otter 使用者 |
| 搜尋 | 搜尋您的會議歷史 |
| 逐字稿 | 依 ID 取得完整逐字稿 |

託管執行環境是工具名稱與結構的來源基準。連線後，請以 `get_user_info` 作為僅讀的冒煙測試。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Otter 使用者身份執行。
- 三個工具皆為僅讀，伺服器無法編輯或刪除 Otter 內容。
- 儘管以「Otter for Enterprise」標誌推出，MCP 伺服器被列為 Basic 層級功能。

<a id="docs"></a>

## 檔案

- Otter MCP 伺服器：https://help.otter.ai/hc/en-us/articles/35287607569687-Otter-MCP-Server
- 伺服器 URL：https://mcp.otter.ai/mcp

標誌為 Otter.ai 官方標記，來自 `otter-ai` GitHub 組織。

<a id="license"></a>

## 授權

MIT
