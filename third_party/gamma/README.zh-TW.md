# Gamma

Cursor 外掛，透過 Gamma 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Gamma](https://developers.gamma.app/mcp/gamma-mcp-server.md)。

生成簡報、檔案和網頁。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Gamma**。
3. 點選 **安裝**，然後完成 Gamma 登入提示。

或在聊天中執行 `/add-plugin gamma`。

## MCP

```json
{
  "mcpServers": {
    "gamma": {
      "type": "http",
      "url": "https://mcp.gamma.app/mcp"
    }
  }
}
```

驗證採用 OAuth。當外掛連線時，Cursor 會提示 Gamma 登入，不需要設定客戶端 ID 或個人存取令牌。

<a id="before-you-connect"></a>

## 連線前

你需要一個 Gamma 帳戶。生成會消耗該帳戶的點數。

<a id="what-agents-can-do"></a>

## 代理可以執行什麼

| 分類 | 功能 |
| --- | --- |
| 生成 | 從提示、大綱或模板生成簡報、檔案、網頁和社交帖子 |
| 匯出 | PDF、PPTX 和 PNG 匯出 |
| 瀏覽 | 列出和讀取現有的 Gamma、主題和資料夾 |
| 分析 | 檢視現有 Gamma 的瀏覽量和評論 |

託管執行環境是工具名稱和結構的來源。

<a id="notes"></a>

## 注意

- 生成是非同步的：工具返回一個生成 ID，代理會持續查詢直到完成。
- 驗證採用 OAuth 2.0 並支援動態客戶端註冊。

<a id="docs"></a>

## 檔案

- Gamma MCP 伺服器：https://developers.gamma.app/mcp/gamma-mcp-server.md
- 工具參考：https://developers.gamma.app/mcp/mcp-tools-reference.md
- 伺服器 URL：https://mcp.gamma.app/mcp

標誌是 Gamma 官方標記，來自 `gamma-app` GitHub 組織。

<a id="license"></a>

## 授權

MIT
