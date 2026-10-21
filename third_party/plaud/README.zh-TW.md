# Plaud

Cursor 外掛，透過 Plaud 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Plaud](https://www.plaud.ai)。

列出並搜尋您的 Plaud 裝置或應用程式所錄製的會議與語音記事，讀取包含時間戳和說話者標籤的完整逐字稿，並將任何錄音的人工智慧生成摘要、行動專案和關鍵主題匯入您的工作中。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Plaud**。
3. 點選 **安裝**，然後完成 Plaud 登入提示。

或在聊天中執行 `/add-plugin plaud`。

## MCP

```json
{
  "mcpServers": {
    "plaud": {
      "type": "http",
      "url": "https://mcp.plaud.ai/mcp"
    }
  }
}
```

驗證方式為 OAuth。當外掛連線時，Cursor 會提示 Plaud 登入，不需要設定客戶端 ID 或 API 金鑰。每個使用者以自己的 Plaud 帳戶登入。

<a id="before-you-connect"></a>

## 在連線之前

您需要一個 Plaud 帳戶，並將錄音同步到 Plaud 雲端（從 Plaud 裝置或 Plaud 應用程式）。尚未在 Plaud 中轉錄或摘要的錄音，將不會有可返回的逐字稿或記事。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 能力 |
| --- | --- |
| 錄音 | `list_files` 列出您的錄音，並提供選項性篩選（`query` 錄音名稱關鍵字匹配、`date_from` / `date_to`、分頁）；`get_file` 返回單一錄音的完整詳細資訊，包括暫時的音訊下載 URL、逐字稿片段和 AI 記事 |
| 逐字稿 | `get_transcript` 返回錄音的完整逐字稿，包含時間戳和說話者標籤 |
| 摘要 | `get_note` 返回錄音的 AI 生成摘要、行動專案和關鍵主題 |
| 帳戶 | `get_current_user` 顯示已連線的 Plaud 帳戶 |

託管執行環境是工具名稱和結構的來源真相。

<a id="notes"></a>

## 注意事項

- 錄音通常包含其他人的說話內容。代理讀取的逐字稿和摘要可能包含同事、客戶或其他人的話語，因此請將其視為機密，並在分享輸出前檢查當地錄音同意規則。
- 請求會透過 Plaud 託管的 MCP 伺服器（位於美國）。Plaud 表示僅在傳輸過程中處理錄音資料，請求完成後不會儲存；請參閱 [Plaud 的隱私政策](https://plaud.ai/privacy)。
- `get_file` 返回一個預簽的音訊 URL，有效期為 24 小時。

<a id="docs"></a>

## 文件

- Plaud MCP 指南：https://docs.plaud.ai/plaud-mcp-cli/mcp
- Plaud MCP 與 CLI 變更記錄：https://docs.plaud.ai/plaud-mcp-cli/changelog
- 開發者平臺概覽：https://docs.plaud.ai/overview
- 伺服器 URL：https://mcp.plaud.ai/mcp

標誌是 Plaud 的官方標記。

<a id="license"></a>

## 授權

MIT
