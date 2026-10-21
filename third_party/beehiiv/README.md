# beehiiv

Cursor 外掛，透過 beehiiv 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [beehiiv](https://www.beehiiv.com)。

以你自己的語氣撰寫和編輯電子報文章，用標籤、自訂欄位和區段來組織訂閱者，建立自動化流程，並將出版、文章、播客和網站表現資料帶入聊天。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **beehiiv**。
3. 點選 **安裝**，然後完成 beehiiv 登入提示。

或在聊天中執行 `/add-plugin beehiiv`。

## MCP

```json
{
  "mcpServers": {
    "beehiiv": {
      "type": "http",
      "url": "https://mcp.beehiiv.com/mcp"
    }
  }
}
```

驗證方式為 OAuth。當外掛連線時，Cursor 會提示 beehiiv 登入。不需要設定客戶端 ID 或 API 金鑰。每個使用者以自己的 beehiiv 帳戶登入，連線會沿用該使用者的工作區角色和許可權。

<a id="before-you-connect"></a>

## 連線前

你需要一個 beehiiv 帳戶（[註冊](https://www.beehiiv.com)）。MCP 適用於所有方案：免費方案僅提供讀取權。寫入動作（建立和編輯文章、管理區段、建立自動化）需要[付費 beehiiv 方案](https://www.beehiiv.com/pricing)。每個連線繫結一個工作區。若要跨多個工作區作業，請以不同的查詢字串（例如 `https://mcp.beehiiv.com/mcp?account=2`）再次新增伺服器，並將每個連線登入不同的帳戶。

<a id="what-agents-can-do"></a>

## 代理可做的事

| 分類 | 能力 |
| --- | --- |
| 內容與文章 | 建立和編輯文章草稿、管理文章模板和內容標籤、讀取過去文章及其內容以匹配語氣和結構 |
| 受眾與訂閱者 | 查詢訂閱者及其互動資料、建立和管理訂閱者標籤與自訂欄位、建立區段和條件集合、設定調查和投票 |
| 成長與獲取 | 管理註冊流程、訂閱表單和表單主題、檢視、探索和管理推薦 |
| 營收 | 檢視廣告網路機會和提案以及直接贊助協議（僅讀取）、管理付費產品和等級、檢視付費牆 |
| 自動化 | 建立和設定自動化流程及其內部電子郵件 |
| 播客 | 檢視節目和單集資料、單集統計和逐字稿 |
| 網站 | 編輯頁面 metadata（如 SEO 標題和描述）、管理重定向、檢視網站分析 |
| 分析 | 出版、文章和網站表現：訂閱者成長、開啟率、點選率、送達率和流量分佈 |
| 其他 | 上傳和管理圖片資產、管理客座作者、管理外部 RSS 來源 |

託管執行環境是工具名稱和 schema 的來源真相。

<a id="notes"></a>

## 注意事項

- 寫入動作會改變你的實際工作區：編輯草稿、標記訂閱者、儲存區段或更改產品等級會立即影響你的真實受眾。請確認代理即將更改的內容後才執行。
- 發布、排程和傳送文章無法透過 MCP 執行，必須在 beehiiv 應用中完成；代理可以準備草稿但不能傳送。透過 MCP 建立的自動化同樣會保持未啟動狀態，直到你在應用中啟用它們。
- beehiiv 持續更新 MCP 工具且不提供版本。如果新工具未出現，請斷開並重新連線外掛。

<a id="docs"></a>

## 文件

- beehiiv MCP 入門指南：https://www.beehiiv.com/support/article/39255979546263-getting-started-with-the-beehiiv-mcp
- beehiiv MCP 可做的事：https://www.beehiiv.com/support/article/41262491804439-what-you-can-do-with-the-beehiiv-mcp
- MCP 概覽：https://www.beehiiv.com/features/mcp
- API 參考：https://developers.beehiiv.com
- 伺服器 URL：https://mcp.beehiiv.com/mcp

Logo 是 beehiiv 的官方標誌。

<a id="license"></a>

## 授權

MIT
