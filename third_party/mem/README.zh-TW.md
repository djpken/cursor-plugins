# Mem

Cursor 外掛，透過 Mem 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Mem](https://mem.ai)。

直接在編輯器中使用語意搜尋，擷取、搜尋並整理 Mem 中的筆記與收藏。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Mem**。
3. 點選 **安裝**，然後完成 Mem 登入提示。

或在聊天中執行 `/add-plugin mem`。

## MCP

```json
{
  "mcpServers": {
    "mem": {
      "type": "http",
      "url": "https://mcp.mem.ai/mcp"
    }
  }
}
```

驗證方式為 OAuth。Cursor 會在外掛連線時提示 Mem 登入。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 筆記 | 取得、列出、搜尋、建立、更新、丟棄、恢復及刪除筆記 |
| 收藏 | 建立與搜尋收藏，並新增或移除筆記成員 |
| 附件 | 處理附件與錄音 |

託管執行環境是工具名稱與結構的來源。

<a id="notes"></a>

## 筆記

- 工具呼叫會以授權連線的 Mem 使用者身份執行。
- Mem 會執行用量限制。達到限制時會返回 HTTP 429，並帶有 `UsageQuotaExceeded` 或 `CommercialUsageLimitRejection` 資料。
- 未驗證的請求對端點失敗是預期的行為，Mem 將此記錄為正常行為，並非連線故障。

<a id="docs"></a>

## 文件

- Mem MCP 概覽：https://docs.mem.ai/mcp/overview
- 設定：https://docs.mem.ai/mcp/setup
- 支援的工具：https://docs.mem.ai/mcp/supported-tools
- 伺服器 URL：https://mcp.mem.ai/mcp

Logo 是 Mem 官方標誌，來自 `mem-labs` GitHub 組織。

<a id="license"></a>

## 授權

MIT
