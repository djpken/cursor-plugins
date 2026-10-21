# Readwise

Cursor 外掛，透過 Readwise 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Readwise](https://readwise.io)。

搜尋你已讀過的所有內容。混合語意與全文搜尋，涵蓋 Readwise 重點與 Reader 檔案。並儲存、標記及分類新素材。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Readwise**。
3. 點選 **安裝**，然後完成 Readwise 登入提示。

或在聊天中執行 `/add-plugin readwise`。

## MCP

```json
{
  "mcpServers": {
    "readwise": {
      "type": "http",
      "url": "https://mcp2.readwise.io/mcp"
    }
  }
}
```

驗證採用 OAuth。外掛連線時，Cursor 會提示 Readwise 登入。無需設定 API 金鑰。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 重點 | 搜尋與列出重點、建立新重點，以及更新或刪除現有重點 |
| 每日回顧 | 擷取當前每日回顧 |
| Reader 檔案 | 搜尋、列出與閱讀檔案，並取得其詳細資訊與重點 |
| 分類 | 儲存新檔案、移動位置，以及新增或移除標記 |

託管執行環境是工具名稱與結構的來源基準。連線後，執行 `readwise_get_daily_review` 作為僅讀的冒煙測試。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Readwise 使用者身份執行。
- 主機是 `mcp2.readwise.io`，而非 `mcp.readwise.io`。
- `@readwise/readwise-mcp` npm 套件是 Readwise 自家的舊版本地伺服器。它已棄用、不支援 Reader，且不應用於新設定。

<a id="docs"></a>

## 檔案

- Readwise MCP: https://docs.readwise.io/tools/mcp
- Readwise MCP 登陸頁: https://readwise.io/mcp
- 伺服器 URL: https://mcp2.readwise.io/mcp

標誌是 Readwise 官方標記，取自 `readwiseio` GitHub 組織。

<a id="license"></a>

## 授權

MIT
