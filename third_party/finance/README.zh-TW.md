<a id="finance"></a>

# 財務

Grok Bot 外掛，透過 Grok 聯結器閘道上的遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 Grok **財務**聯結器。

透過 Plaid 連結您的銀行、卡片和投資帳戶，讓 Grok 能回答關於餘額、消費、訂閱和投資的疑問。您將分享聯絡資訊、帳戶與餘額資訊、交易、信用與貸款，以及投資。Grok 會同步並儲存您連結的帳戶資料，以便聯結器運作，而 Grok Bot 的隱私模式仍會控制您的對話是否被儲存或用於訓練。存取為唯讀，xAI 不會看到或儲存您的銀行登入資訊，您可以隨時從 cursor.com 解除帳戶連結。

<a id="who-can-use-it"></a>

## 適用物件

- Grok Bot **0.49** 或更新版本。
- 不適用於 Cursor。Cursor 不得列出或安裝此外掛。

## MCP

```json
{
  "mcpServers": {
    "finance": {
      "type": "http",
      "url": "https://connectors-gateway.grok.com/gateway/v1/finance/mcp"
    }
  }
}
```

伺服器由 xAI 託管，並透過與呼叫者連結的 Grok 帳戶進行驗證。客戶端沒有登入提示：Cursor 後端在撥打此 URL 時會附加連結帳戶的憑證。在 grok.com（Plaid）上連結帳戶發生於 grok.com；直到帳戶連結後，聯結器才會報告需要授權。

<a id="about-this-connector"></a>

## 關於此聯結器

- **在聊天中檢視您的財務資訊。** 詢問關於連結帳戶中的餘額、近期消費、訂閱和投資。
- **您分享的內容。** 透過 Plaid 連結會將聯絡資訊、帳戶與餘額資訊、交易、信用與貸款，以及投資分享給 Grok，以便它能追蹤和管理您的財務。
- **資料同步。** Grok 會同步並儲存連結帳戶資料（餘額、交易、持有資產），以便聯結器能回答問題；此同步為聯結器運作所需。Grok Bot 的隱私模式仍會控制您的對話是否被儲存或用於訓練。
- **您的憑證保持私密。** xAI 不會儲存或檢視任何銀行帳戶或密碼憑證。
- **唯讀存取。** 財務連線為唯讀，意味著無法用來將資金移入或移出銀行帳戶。
- **隨時解除連結。** 從 cursor.com 的財務頁面管理或移除連結帳戶。

第三方聯結器並非由 xAI 構建或維護。授予外部服務存取權時請謹慎。在連線前請檢視許可權。使用受 [xAI 隱私政策](https://x.ai/legal/privacy-policy) 規範。

<a id="license"></a>

## 授權

MIT
