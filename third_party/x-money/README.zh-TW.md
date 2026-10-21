# X Money

Grok Bot 外掛，透過 X Money 託管的 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器（位於 `https://mcp.money.x.com/mcp`）將代理連線到 [X Money](https://x.com/i/money)。

使用您的 X Money 卡片、向 X 上的使用者傳送資金、管理財務、檢視餘額以及瀏覽交易歷史。

<a id="who-can-use-it"></a>

## 適用物件

- Grok Bot **0.52** 或更新版本。
- 有效的 X Money 帳戶。X Money 僅適用於美國的合格客戶。
- 不適用於 Cursor。Cursor 不得列出或安裝此外掛。

<a id="install"></a>

## 安裝

1. 開啟 **Grok Bot → 外掛**。
2. 搜尋 **X Money**。
3. 點選 **安裝**，然後在提示時完成 X Money 連線。

或請代理協助連線您的 X Money 帳戶。

## MCP

```json
{
  "mcpServers": {
    "x-money": {
      "type": "http",
      "url": "https://mcp.money.x.com/mcp",
      "placement": "server"
    }
  }
}
```

<a id="connecting"></a>

## 連線

驗證方式為 OAuth。沒有需要貼上的客戶 ID、API 金鑰或令牌。Cursor 會代表您完成登入。

1. Grok Bot 會在瀏覽器中開啟 **將 Grok Bot 連線至 X Money** 頁面。頁面會顯示連線碼和 QR 碼。
2. 在 X Money 應用中輸入該碼，或掃描 QR 碼，並使用您的通行金鑰進行核准。
3. 頁面會確認連線，Grok Bot 會自行接收該連線。

該碼的有效期限約為 5 分鐘。您可以隨時從 **X → 錢包 → 設定 → 連線** 撤銷連線。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 餘額 | 您的 X Money 帳戶的可用餘額 |
| 交易 | 您的交易歷史，可加上篩選條件 |
| 付款 | 向另一位 X 使用者傳送資金，或請求另一位 X 使用者付款給您 |
| 購買 | 使用 X Money 虛擬卡片向線上商家付款 |

X Money 會決定每個帳戶可使用的動作，因此不同使用者之間的可用動作可能有所不同。

<a id="approval"></a>

## 核准

每個涉及資金移動的動作都會先與您確認。在傳送資金、請求資金或向商家付款之前，代理會顯示核准 / 取消提示，明確說明物件、金額和用途。直到您核准後才會執行，且每個動作都需要獨立核准。X Money 也會在其端強制執行此規則，並拒絕任何未核准的此類呼叫。

<a id="skill"></a>

## 技能

`skills/x-money-guide/SKILL.md` 提供代理上述核准規則、連線流程、如何指導您進行連線、重新連線和撤銷，以及每個 X Money 失敗訊息的含義。

<a id="notes"></a>

## 注意事項

- 透過此外掛進行的付款和購買是實際操作，會移動真實資金。
- 每個動作都會經過與應用相同的 X Money 檢查，並加上代理的獨立限制。當 X Money 拒絕動作時，代理會轉達原因且不會重試。
- 較高風險的轉帳會保留在 X 應用中，不提供給代理。
- 購買時使用的卡片資訊會直接傳送至商家結帳頁面，絕不會在聊天中顯示。
- 代理絕不會看到您的 X Money 密碼、通行金鑰或銀行憑證。

<a id="docs"></a>

## 文件

- X Money：https://x.com/i/money
- 管理已連線的代理：https://x.com/i/money/settings/connections
- 伺服器 URL：https://mcp.money.x.com/mcp

標誌為 X Money 的官方標誌。

<a id="license"></a>

## 授權

MIT
