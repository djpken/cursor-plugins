# Shopify

Grok Bot 外掛，將代理連線到 Grok **Shopify** 聯結器，該聯結器透過 Shopify 的遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器與您的商店溝通。

連線您的 Shopify 商店，讓 Grok 可以回答關於您的產品、訂單、客戶、庫存和銷售的問題。您透過 Shopify 登入並批准 Grok 請求的商店存取權；Grok 保留該授權以便聯結器可以運作，而 Grok Bot 的隱私模式仍控制您的對話是否被儲存或用於訓練。xAI 不會看到或儲存您的 Shopify 密碼，您可以隨時從 grok.com 斷開商店。

這是針對現有商店的商家聯結器，列在 slug `shopify-store` 下，因為市場已經有 Shopify 自己的 [`shopify-plugin`](https://cursor.com/marketplace/shopify)（Shopify AI 工具套件：用於 Shopify 開發的技能，無商店存取權）。這兩者旨在整合為一個單一的 "Shopify" 外掛，同時包含 MCP 伺服器和工具套件技能；在此之前，`rules/shopify.mdc` 告訴代理在兩者都安裝時，何時使用商店的 MCP 工具，何時使用工具套件技能。

<a id="who-can-use-it"></a>

## 誰可以使用

- Grok Bot **0.49** 或更新版本。
- 不適用於 Cursor。Cursor 不得列出或安裝此外掛。

## MCP

```json
{
  "mcpServers": {
    "shopify": {
      "type": "http",
      "url": "https://setup.shopify.com/mcp"
    }
  }
}
```

伺服器是 Shopify 為連線商店提供的託管 MCP。登入透過 Grok 執行：Grok 聯結器完成 Shopify 的 OAuth（PKCE 公共客戶，由 xAI 註冊）並代表呼叫者執行工具，因此在 Grok Bot 中顯示為由 Grok 服務。Shopify 的授權伺服器不提供動態客戶註冊，這就是 Cursor 無法自行連線此 URL 的原因，外掛僅限 Grok Bot。

<a id="routing-rule"></a>

## 路由規則

`rules/shopify.mdc`（始終應用）路由請求：使用者自己的商店資料和變更會送往 `shopify` MCP 工具，在任何寫入工具之前需確認；開發工作（GraphQL、Liquid、Functions、Hydrogen、Polaris、App Store 審核、文件）在安裝時送往 Shopify AI 工具套件技能；工具套件透過 Shopify CLI 對商店執行任何操作時，在 Grok Bot 內改用 MCP 工具。

<a id="about-this-connector"></a>

## 關於此聯結器

- **在聊天中詢問您的商店。** 針對連線商店的產品、價格、訂單、客戶、庫存水平和銷售問題。
- **您分享的內容。** 登入授予 Grok 您在 Shopify 同意頁面上批准該商店的存取權。
- **授權。** Grok 儲存產生的授權以便聯結器可以執行請求；Grok Bot 的隱私模式仍控制對話是否被儲存或用於訓練。
- **您的憑證保持私密。** xAI 不會看到或儲存您的 Shopify 密碼。
- **隨時斷開。** 從 grok.com 的 Shopify 聯結器移除商店。

第三方聯結器並非由 xAI 構建或維護。授予外部服務存取權時請謹慎。連線前請審視許可權。使用受 [xAI 隱私政策](https://x.ai/legal/privacy-policy) 約束。

<a id="license"></a>

## 許可

MIT
