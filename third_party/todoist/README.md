# Todoist

Cursor 外掛，透過 Todoist 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Todoist](https://www.todoist.com)。

在已登入的 Todoist 帳戶中，建立、尋找、更新和完成任務，並管理專案、區段、標籤和截止日期。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Todoist**。
3. 點選 **安裝**，然後完成 Todoist 登入提示。

或在聊天中執行 `/add-plugin todoist`。

## MCP

```json
{
  "mcpServers": {
    "todoist": {
      "type": "http",
      "url": "https://ai.todoist.net/mcp"
    }
  }
}
```

驗證方式為 OAuth。Cursor 會在第一次工具呼叫時提示 Todoist 登入，不需要設定 API 金鑰。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 任務 | 建立、更新、完成和重新開啟任務，包括截止日期和提醒 |
| 專案 | 管理專案、區段和標籤 |
| 搜尋 | `search` 和 `fetch` 工具，用於尋找任務並取得其詳細資訊 |
| 留言 | 在任務和專案上讀取和新增留言 |

託管執行環境是工具名稱和結構的來源真相。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Todoist 使用者身份執行。
- Doist 也發布了官方本地伺服器 `@doist/todoist-mcp`，使用 `TODOIST_API_KEY` 進行驗證。如果您在託管端點遇到連線斷開的情況，建議改用此伺服器。
- 不需要任何 Todoist 方案等級。Todoist 說明文章中的付費方案要求適用於 AI 客戶端，而非 Todoist。

<a id="docs"></a>

## 檔案

- Todoist MCP 伺服器：https://github.com/Doist/todoist-mcp
- 使用 Todoist MCP：https://www.todoist.com/help/articles/use-chatgpt-with-todoist-mcp-WEeLx9d8h
- 伺服器 URL：https://ai.todoist.net/mcp

標誌是 Todoist 的官方標記，來自 https://todoist.com.

<a id="license"></a>

## 授權

MIT
