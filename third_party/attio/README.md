# Attio

Cursor 外掛，透過 Attio 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Attio](https://attio.com)。

在已登入的 Attio 工作區中，搜尋、建立和更新 CRM 記錄（人員、公司、交易和自訂物件）、處理列表、備註和任務，以及搜尋電子郵件、會議和評論。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Attio**。
3. 點選 **安裝**，然後完成 Attio 登入提示。

或在聊天中執行 `/add-plugin attio`。

## MCP

```json
{
  "mcpServers": {
    "attio": {
      "type": "http",
      "url": "https://mcp.attio.com/mcp"
    }
  }
}
```

驗證是透過 Attio 的 OAuth。當外掛連線時，Cursor 會提示 Attio 使用者登入。不需要設定 API 金鑰或客戶 ID。

<a id="before-you-connect"></a>

## 連線之前

您需要一個 Attio 帳戶，並能存取一個工作區。Attio MCP 可供所有工作區成員使用。

<a id="what-agents-can-do"></a>

## 代理可以執行的工作

| 分類 | 功能 |
| --- | --- |
| 記錄與物件 | 搜尋、建立、更新、插入或更新，以及合併人員、公司、交易和自訂物件 |
| 列表 | 檢視列表和屬性、新增記錄，以及更新列表專案和階段 |
| 備註 | 搜尋、建立和更新備註；對備註內容進行語意搜尋 |
| 任務 | 列出、建立和更新任務 |
| 會議與電話 | 搜尋電話錄音和逐字稿 |
| 電子郵件 | 搜尋和閱讀電子郵件 |
| 評論 | 列出和新增記錄與列表專案上的評論 |
| 工作區 | 列出成員和團隊；檢視已登入的使用者 |
| 報告 | 執行基本報告；部分方案提供選用的唯讀 SQL |

託管執行環境是工具名稱和結構的來源。

<a id="notes"></a>

## 備註

- 工具呼叫會以授權連線的 Attio 使用者身份執行，且不能超出該使用者許可權。
- 讀取操作會自動核准。寫入操作會在變更工作區資料之前請求確認。
- `query-particle-sql`（唯讀 SQL）並非在所有 Attio 計費方案中皆可用。
- Attio 自行託管伺服器；此外掛不會包覆本地 stdio 伺服器。
- 您可以隨時從您的 Attio 帳戶設定中撤銷存取權。

<a id="docs"></a>

## 檔案

- Attio MCP 概覽：https://docs.attio.com/mcp/overview
- Attio MCP 幫助中心：https://attio.com/help/reference/attio-ai/attio-mcp
- 伺服器 URL：https://mcp.attio.com/mcp

標誌是 Attio 的官方標記，來自 `attio` GitHub 組織。

<a id="license"></a>

## 授權

MIT
