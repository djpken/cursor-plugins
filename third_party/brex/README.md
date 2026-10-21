# Brex

Cursor 外掛，透過 Brex 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Brex](https://www.brex.com)。

查詢公司支出，包括費用、收據、帳單、供應商、卡片、餘額和旅遊，並標註費用。全部都在您的 Brex 許可權範圍內執行。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Brex**。
3. 點選 **安裝**，然後完成 Brex 登入提示。

或在聊天中執行 `/add-plugin brex`。

## MCP

```json
{
  "mcpServers": {
    "brex": {
      "type": "http",
      "url": "https://api.brex.com/mcp"
    }
  }
}
```

認證採用 OAuth 與 Dynamic Client Registration。Cursor 會自行註冊，並在外掛連線時提示 Brex 登入。不需要設定 API 金鑰或客戶 ID。

<a id="before-you-connect"></a>

## 連線之前

兩個一次性管理步驟會限制整個帳戶。帳戶或卡片管理員必須在 **設定 → 開發者** 下接受 Developer API 協議，然後在 **設定 → Beta 功能** 下啟用 **Brex in AI assistants**。之後，任何員工都可以使用自己的憑證連線。

<a id="what-agents-can-do"></a>

## 代理可以做的事

| 分類 | 功能 |
| --- | --- |
| 費用 | 列出和檢視費用、執行費用分析、瀏覽費用和商家分類 |
| 帳單與供應商 | 列出和檢視帳單、供應商和應付帳款 |
| 卡片與限制 | 卡片詳細資訊和消費限制 |
| 銀行 | 帳戶餘額和現金詳細資訊 |
| 旅遊 | 行程和訂房 |
| 組織 | 使用者、部門和地點 |

託管執行環境是工具名稱和結構的來源。連線後，呼叫 `list_expense_categories` 作為僅讀的冒煙測試。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Brex 使用者身份執行，並受限於該使用者現有的 Brex 功能。
- 寫入操作範圍有限：費用元資料，例如備忘錄、收據和出席者。伺服器無法轉移資金、發行卡片或支付帳單。
- 核准和卡片管理尚未透過 MCP 公開。
- Brex 將 MCP 伺服器標為 beta，因此工具目錄可能會變動。

<a id="docs"></a>

## 檔案

- Brex MCP 伺服器：https://developer.brex.com/docs/mcp
- 伺服器 URL：https://api.brex.com/mcp

Logo 是 Brex 的官方標誌，來自 `brexhq` GitHub 組織。

<a id="license"></a>

## 授權

MIT
