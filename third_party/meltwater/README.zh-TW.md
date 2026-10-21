<a id="meltwater"></a>

# 融雪水

Cursor 外掛，透過 Meltwater 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Meltwater](https://developer.meltwater.com/guides/meltwater-mcp/overview)。

搜尋媒體與社群提及，並提取分析資料。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Meltwater**。
3. 點選 **安裝**，然後完成 Meltwater 登入提示。

或在聊天中執行 `/add-plugin meltwater`。

## MCP

```json
{
  "mcpServers": {
    "meltwater": {
      "type": "http",
      "url": "https://api.meltwater.com/v2/mcp"
    }
  }
}
```

驗證採用 OAuth。當外掛連線時，Cursor 會提示 Meltwater 登入，無需設定客戶端 ID 或 API 令牌。

<a id="before-you-connect"></a>

## 連線前

您需要包含 Meltwater MCP 套件的 Meltwater 訂閱。可用工具視您的訂閱產品而定。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 您的資產 | 尋找在 Meltwater 中設定的已儲存搜尋與標籤 |
| 提及 | 為已儲存搜尋取得新聞與社群檔案 |
| 分析 | 數量、情緒、主要來源與主題 |
| 查詢 | 當沒有已儲存搜尋時產生查詢 |

託管執行環境是工具名稱與結構的來源真相。

<a id="notes"></a>

## 注意事項

- Meltwater 的伺服器宣傳標準 MCP OAuth 與動態客戶端註冊；其公開檔案仍描述 API 金鑰驗證，但 OAuth 流程已生效。
- 這是 Meltwater MCP (`/v2/mcp`)，而非位於 `/mcp` 的更高層級 Mira API 端點。

<a id="docs"></a>

## 檔案

- Meltwater MCP 概覽：https://developer.meltwater.com/guides/meltwater-mcp/overview
- 連線：https://developer.meltwater.com/guides/meltwater-mcp/connecting
- 伺服器 URL：https://api.meltwater.com/v2/mcp

標誌為 Meltwater 官方標記。

<a id="license"></a>

## 授權

MIT
