# Wrike

Cursor 外掛，透過 Wrike 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Wrike](https://www.wrike.com)。

搜尋 Wrike 工作區、瀏覽資料夾與專案階層，並建立或更新任務、專案與留言。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Wrike**。
3. 點選 **安裝**，然後設定您的 Wrike 永久存取令牌（如下）。

或在聊天中執行 `/add-plugin wrike`。

## MCP

```json
{
  "mcpServers": {
    "wrike": {
      "type": "http",
      "url": "https://mcp.wrike.com/v2",
      "headers": {
        "Authorization": "Bearer ${WRIKE_ACCESS_TOKEN}"
      }
    }
  }
}
```

驗證方式是 Wrike **永久存取令牌**，以 bearer token 形式傳送。請在 **應用與整合 → API** 下建立一個，然後在 **Dashboard → 外掛 → 設定** 中設定。

Wrike 的 MCP 伺服器不提供 Dynamic Client Registration，因此令牌是支援 Cursor 等客戶端的方式，而非瀏覽器 OAuth 流程。

<a id="before-you-connect"></a>

## 在連線之前

Wrike MCP 適用於所有方案（Free、Team、Business、Pinnacle 與 Apex）。建立令牌需要 Wrike API 許可權，帳戶所有者預設擁有這些許可權。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 搜尋 | 跨工作區的統一搜尋 |
| 瀏覽 | 瀏覽資料夾、專案與空間 |
| 任務 | 建立與更新任務，包括批次操作 |
| 留言與收件匣 | 發表留言並讀取 Wrike 收件匣 |
| 元資料 | 時間記錄、聯絡人與自訂欄位 |

託管執行環境是工具名稱與結構的來源真相。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以令牌持有者的許可權執行。
- `https://mcp.wrike.com/v2` 僅透過 streamable HTTP 提供服務，沒有 SSE 備援。
- 較舊的 `https://mcp.wrike.com/app/mcp/stream` 與 `https://mcp.wrike.com/app/mcp/sse` 網址屬於 MCP v1，並提供較舊的工具組合。部分 Wrike 頁面仍會引用這些網址。
- Wrike 也支援 connector 型 OAuth，使用管理員建立的 OAuth 應用中的 client ID 與 secret。此方式主要針對託管 connector，而非本地客戶端。

<a id="docs"></a>

## 檔案

- Wrike MCP 伺服器概覽：https://developers.wrike.com/docs/wrike-mcp-server-overview
- 設定其他 MCP 客戶端：https://developers.wrike.com/docs/setup-other-mcp-clients-with-wrike-mcp
- Wrike MCP（幫助中心）：https://help.wrike.com/hc/en-us/articles/34605829225746-Wrike-Model-Context-Protocol-MCP
- 伺服器網址：https://mcp.wrike.com/v2

標誌是 Wrike 的官方標記，來自 `wrike` GitHub 組織。

<a id="license"></a>

## 授權

MIT
