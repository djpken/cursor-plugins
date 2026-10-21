# Finary

Cursor 外掛，透過 Finary 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Finary](https://finary.com)。

讀取您的淨資產、帳戶、持股、支出與預算。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Finary**。
3. 點選 **安裝**，然後完成 Finary 登入提示。

或在聊天中執行 `/add-plugin finary`。

## MCP

```json
{
  "mcpServers": {
    "finary": {
      "type": "http",
      "url": "https://public-api.finary.com/mcp"
    }
  }
}
```

驗證採用 OAuth，並使用 Dynamic Client Registration 與 PKCE。Cursor 會自行註冊，並在外掛連線時開啟 Finary 的登入頁面：使用您的 Finary 帳戶登入，檢視助理可讀取的資料列表，並授權唯讀存取。不需要設定 API 金鑰、客戶 ID 或銀行憑證。

<a id="before-you-connect"></a>

## 連線前

您需要一個 Finary 帳戶。聯結器會讀取 Finary 中已有的資料，因此請先在 Finary 中連結您的銀行、券商及其他帳戶。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

聯結器會讀取登入使用者自身的 Finary 資料：

| 區域 | 資料 |
| --- | --- |
| 淨資產 | 總財富及其設定，包括每位家庭成員的分項 |
| 帳戶與資產 | 帳戶、持股與部位、不動產、貸款及其他資產 |
| 投資 | 每項基金的費用與複利模擬 |
| 現金流 | 收入、支出與預算，最多兩年歷史 |
| 規劃 | 目標與計畫 |
| 個人資料 | 在 Finary 中新增的家庭成員與公司、姓名、電子郵件及連結帳戶狀態 |

託管執行環境是工具名稱與結構的來源基準。

<a id="notes"></a>

## 注意事項

- 聯結器僅具唯讀功能。它無法下單、進行轉帳，或更改、刪除 Finary 中的任何內容。
- 它只讀取登入使用者自身的資料。
- 回答描述您的財富狀況，並非投資建議。
- 若要斷開連線，請移除外掛或從您的 Finary 帳戶中撤銷存取權。撤銷會立即生效，重新連線則需再次授權。

<a id="docs"></a>

## 文件

- Finary MCP 頁面：https://finary.com/en/mcp
- 設定指南：https://help.finary.com/en/articles/16335735-using-the-finary-mcp-with-my-ai-assistant-claude-chatgpt-perplexity-mistral
- 伺服器 URL：https://public-api.finary.com/mcp

Logo 為 Finary 官方標誌。

<a id="license"></a>

## 授權

MIT
