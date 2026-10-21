# TinyFish

Cursor 外掛，透過 TinyFish 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [TinyFish](https://www.tinyfish.ai)。

從 URL 和自然語言目標執行多步驟網頁自動化、搜尋網路、從頁面提取乾淨內容，並開啟遠端隱身瀏器會話，以便直接控制 Playwright、Puppeteer 或 Selenium。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **TinyFish**。
3. 點選 **安裝**，然後完成 TinyFish 登入提示。

或在聊天中執行 `/add-plugin tinyfish`。

## MCP

```json
{
  "mcpServers": {
    "tinyfish": {
      "type": "http",
      "url": "https://agent.tinyfish.ai/mcp"
    }
  }
}
```

驗證方式為 OAuth。當外掛連線時，Cursor 會提示 TinyFish 登入，不需要設定客戶端 ID 或 API 金鑰。每位使用者以自己的 TinyFish 帳戶登入。

<a id="before-you-connect"></a>

## 連線前

你需要一個 TinyFish 帳戶（[註冊](https://agent.tinyfish.ai/api-keys)）。搜尋和內容提取免費；網頁自動化和瀏器會話會從你的 TinyFish 錢包扣費（舊帳戶使用方案點數）。請參閱 [費率](https://docs.tinyfish.ai/mcp-integration#rates) 檢視當前價格。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 網頁自動化 | `run_web_automation` 從 URL 和目標在網站上執行多步驟任務（點選、瀏覽、表單、登入）並串流進度；`run_web_automation_async` 對長時間任務返回 `run_id`，並提供 `get_run`、`list_runs` 和 `cancel_run` 進行後續操作。儲存的瀏器上下文設定（`use_profile`、`profile_id`）可重用登入狀態，適用於定期驗證的工作流程 |
| 批次自動化 | `batch_status` 和 `batch_cancel` 可同時檢查或取消最多 8 個執行 |
| 網路搜尋 | `search` 返回結構化結果，並提供時間、日期範圍、網站型別（網頁、新聞、研究論文）、地點、語言和網站允許/拒絕列表的篩選；`get_search_usage` 列出過去搜尋 |
| 內容提取 | `fetch_content` 從最多 10 個 URL 渲染並提取乾淨的 Markdown、HTML 或 JSON，支援 CSS 選擇器範圍和條件請求；`list_fetch_usage` 列出過去提取 |
| 瀏器會話 | `create_browser_session` 開啟遠端隱身 Chrome 會話並返回 CDP 連線資訊；`list_browser_sessions` 和 `close_browser_session` 管理它們 |
| 錢包 | `get_wallet` 讀取餘額、自動充值狀態和每個產品費率（僅讀） |

託管執行環境是工具名稱和 schema 的來源真相。

<a id="notes"></a>

## 注意事項

- 網頁自動化會在真實網站上執行，並使用你的儲存設定和憑證（當你啟用時）。請先檢視目標，再讓代理執行登入、提交表單或購買等任務。
- 有機器人保護的網站可能需要 `stealth` 瀏器設定；複雜自動化可能需要 30–60 秒。
- 執行和瀏器分鐘會從你的 TinyFish 錢包扣費；`get_wallet` 顯示當前餘額和費率。

<a id="docs"></a>

## 文件

- MCP 整合指南：https://docs.tinyfish.ai/mcp-integration
- 外掛概述：https://docs.tinyfish.ai/plugins
- API 參考：https://docs.tinyfish.ai/api-reference
- 伺服器 URL：https://agent.tinyfish.ai/mcp

Logo 是 TinyFish 的官方標誌。

<a id="license"></a>

## 授權

MIT
