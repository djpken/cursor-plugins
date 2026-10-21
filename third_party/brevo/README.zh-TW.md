# Brevo

Cursor 外掛，透過 Brevo 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Brevo](https://www.brevo.com)。

管理聯絡人和名單，建立電子郵件、SMS 和 WhatsApp 行銷活動，並在您的 Brevo 帳戶中使用內建 CRM 的交易、公司和任務。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Brevo**。
3. 點選 **安裝**，然後設定您的 Brevo MCP 令牌（如下）。

或在聊天中執行 `/add-plugin brevo`。

## MCP

```json
{
  "mcpServers": {
    "brevo": {
      "type": "http",
      "url": "https://mcp.brevo.com/v1/brevo/mcp",
      "headers": {
        "Authorization": "Bearer ${BREVO_MCP_TOKEN}"
      }
    }
  }
}
```

驗證是 Brevo **MCP 令牌**，以 bearer 令牌傳送。Brevo 的一般 API 金鑰無法使用，金鑰必須在啟用 **Create MCP server API key** 時建立。

<a id="before-you-connect"></a>

## 在連線之前

在 Brevo 的 **Account → SMTP & API → API Keys** 中建立令牌，開啟 **Create MCP server API key**，然後在 **Dashboard → Plugins → Configure** 中設定。

令牌授予帳戶的完整讀取和寫入存取權。沒有僅讀模式，因此請將其視為密碼，若洩漏則從同一畫面輪換。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 聯絡人 | 聯絡人、名單、區段、屬性、資料夾及匯入/匯出 |
| 電子郵件行銷活動 | 建立和管理行銷活動、模板及交易模板，並讀取分析資料 |
| SMS 與 WhatsApp | SMS 行銷活動，加上 WhatsApp 行銷活動和訊息管理 |
| CRM | 交易、公司、任務、流程和註記 |
| 帳戶與寄送 | 寄件者、域名、專用 IP、使用者、Webhook 和外部資料來源 |

託管執行環境是工具名稱和結構的來源。

<a id="notes"></a>

## 註記

- 工具呼叫以 MCP 令牌的許可權執行，而非以個別使用者身份。
- `https://mcp.brevo.com/v1/brevo/mcp` 同時暴露全部 27 個模組。Brevo 也為每個模組提供一個端點，例如 `https://mcp.brevo.com/v1/brevo_contacts/mcp`，這是減少工具數量的好方法，如果完整伺服器範圍過大。
- Brevo 將 MCP 伺服器描述為早期存取，因此工具目錄可能變動。
- Brevo 在 `https://developers.brevo.com/_mcp/server` 執行另一個獨立 MCP 伺服器，用於文件搜尋。此外掛指向產品伺服器。

<a id="docs"></a>

## 文件

- Brevo MCP 協議：https://developers.brevo.com/docs/mcp-protocol
- 整合指南（Cursor 設定）：https://developers.brevo.com/docs/integration-guide
- 伺服器 URL：https://mcp.brevo.com/v1/brevo/mcp

標誌是 Brevo 的官方標記，來自 `getbrevo` GitHub 組織。

<a id="license"></a>

## 授權

MIT
