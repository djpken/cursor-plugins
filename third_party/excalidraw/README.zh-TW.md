# Excalidraw

Cursor 外掛，透過 Excalidraw 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Excalidraw](https://github.com/excalidraw/excalidraw-mcp)。

從聊天中繪製並匯出手繪風格圖表。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Excalidraw**。
3. 點選 **安裝**。

或在聊天中執行 `/add-plugin excalidraw`。

## MCP

```json
{
  "mcpServers": {
    "excalidraw": {
      "type": "http",
      "url": "https://mcp.excalidraw.com/mcp"
    }
  }
}
```

不需要驗證；伺服器僅涵蓋公開儲存庫。

<a id="before-you-connect"></a>

## 在連線之前

不需要帳號。託管伺服器會在支援的客戶端中將圖表渲染為互動式 MCP 應用檢視，並可將 `.excalidraw` 檔案匯出到任何位置。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 繪製 | 根據代理描述的元素建立圖表檢視 |
| 匯出 | 將當前繪製內容匯出為 `.excalidraw` 檔案 |
| 檢查點 | 在會話中儲存和恢復繪製檢查點 |

託管執行環境是工具名稱和結構的來源依據。

<a id="notes"></a>

## 注意事項

- 這是開源的 Excalidraw MCP 伺服器。Excalidraw+ 工作區使用一個獨立且需要驗證的端點 (`https://api.excalidraw.com/api/v1/mcp`)，此外掛不會設定該端點。
- 裸來源 `https://mcp.excalidraw.com` 會重新導向至 `/mcp`；外掛使用最終 URL。

<a id="docs"></a>

## 文件

- excalidraw-mcp 在 GitHub：https://github.com/excalidraw/excalidraw-mcp
- 伺服器 URL：https://mcp.excalidraw.com/mcp

標誌是 Excalidraw 官方標記，來自 `excalidraw` GitHub 組織。

<a id="license"></a>

## 授權

MIT
