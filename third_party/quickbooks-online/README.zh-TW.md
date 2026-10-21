# QuickBooks Online

Cursor 外掛，透過 Intuit 的遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [QuickBooks Online](https://quickbooks.intuit.com)，Intuit 的雲端會計產品，Cursor 會自動處理您的憑證。

讀取公司檔案中的會計資料：發票、帳單、費用、付款、客戶與供應商、科目表、日記帳分錄，以及標準財務報表。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **QuickBooks Online**。
3. 點選 **安裝**，然後在 Cursor 提示時登入 Intuit。

或在聊天中執行 `/add-plugin quickbooks-online`。

## MCP

```json
{
  "mcpServers": {
    "quickbooks-online": {
      "type": "http",
      "url": "https://mcp-e2e.quickbooks.intuit.com/mcp"
    }
  }
}
```

Intuit 託管伺服器。驗證是針對 Intuit 的 OAuth，Cursor 在外掛連線時執行，沒有需要執行指令，也沒有需要貼上的 API 金鑰或客戶金鑰。

<a id="before-you-connect"></a>

## 連線前

不需要準備任何事項。Cursor 擁有 OAuth 客戶端，因此您不需要 Intuit 開發者帳戶或自己的應用程式。

您會被要求登入 Intuit 並選擇一個公司。授權是針對每個公司檔案，每個您希望代理能訪問的公司都需要連線一次，並僅授予您打算公開的公司。

<a id="what-agents-can-do"></a>

## 代理能做的事

| 類別 | 功能 |
| --- | --- |
| 帳戶 | 科目表與公司資訊 |
| 聯絡人 | 客戶、供應商與員工 |
| 銷售 | 發票、信用憑單與已收付款 |
| 採購 | 帳單、費用、採購訂單與已付付款 |
| 銀行 | 銀行交易與日記帳分錄 |
| 專案 | 產品、服務與追蹤類別 |
| 報表 | 損益表、資產負債表與現金流量表 |

伺服器是工具名稱與 schema 的來源真相。

<a id="notes"></a>

## 注意事項

- 訪問範圍遵循登入的 Intuit 使用者所能看到的內容。聯結器無法訪問該使用者沒有許可權的公司檔案。
- QuickBooks Online 與 QuickBooks Desktop 是不同的產品。此聯結器適用於 QuickBooks Online。
- 斷開連線是兩個獨立步驟：解除安裝外掛會停止 Cursor 提供伺服器，而在 Intuit 帳戶設定中撤銷連線會結束 Cursor 對您的資料的訪問。

<a id="docs"></a>

## 文件

- QuickBooks Online: https://quickbooks.intuit.com
- Intuit Developer: https://developer.intuit.com
- Model Context Protocol: https://modelcontextprotocol.io
- 伺服器 URL: https://mcp-e2e.quickbooks.intuit.com/mcp

標誌是 QuickBooks 的官方標記，來自 `quickbooks.intuit.com`。

<a id="license"></a>

## 授權

MIT
