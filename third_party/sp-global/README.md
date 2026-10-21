# S&P Global

Cursor 外掛，透過 S&P Global 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [S&P Global](https://docs.kensho.com/llmreadyapi/overview)。

查詢 S&P Capital IQ 的財務資料、價格與會議紀錄。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **S&P Global**。
3. 點選 **安裝**，然後完成 S&P Global 登入提示。

或在聊天中執行 `/add-plugin sp-global`。

## MCP

```json
{
  "mcpServers": {
    "sp-global": {
      "type": "http",
      "url": "https://kfinance.kensho.com/integrations/mcp"
    }
  }
}
```

驗證方式為 OAuth。Cursor 會在外掛連線時提示 S&P Global 登入，無需設定客戶端 ID 或個人存取令牌。

<a id="before-you-connect"></a>

## 連線前

您需要有效的 S&P Global Market Intelligence 訂閱或試用，才能使用 Kensho LLM-ready API。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 公司 | 透過股票程式碼、ISIN 或 CUSIP 解析公司並讀取公司簡介 |
| 財務資料 | 來自 S&P Capital IQ 的財務報表與超過 200 個專案 |
| 價格 | 歷史價格、成交量、市值與企業價值 |
| 研究 | 電話會議紀錄、商業關係、併購與預估值 |

託管執行環境是工具名稱與結構的唯一來源。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的使用者的 S&P Global 許可權執行。
- Kensho 也提供開源的 `kensho-kfinance` 套件，可執行本地伺服器；此外掛僅使用託管伺服器。

<a id="docs"></a>

## 檔案

- Kensho LLM-ready API：https://docs.kensho.com/llmreadyapi/overview
- GitHub 上的 kfinance：https://github.com/kensho-technologies/kfinance
- 伺服器 URL：https://kfinance.kensho.com/integrations/mcp

標誌為 S&P Global 官方標誌（黑色條帶覆於紅色方塊），以 192×192 尺寸重新繪製，來源為 marketplace.spglobal.com 發布的圖示。

<a id="license"></a>

## 授權

MIT
