# etoro

Cursor 外掛，透過 eToro 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [eToro](https://api-portal.etoro.com/core/vibe-code/cursor) 的 eToro 公開 API。

檢視您的 eToro 投資組合、餘額、持倉和關注列表，研究工具和交易者，並準備和下達交易。

此外掛用於從聊天中操作 eToro 帳戶。若要針對 eToro 公開 API 編寫程式碼，請安裝 eToro 自己的 [`etoro`](https://cursor.com/marketplace/etoro) 外掛，該外掛包含 API 構建規則、技能和文件搜尋伺服器。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **etoro**。
3. 點選 **安裝**，然後完成 eToro 登入提示。

或在聊天中執行 `/add-plugin etoro-trading`。

## MCP

```json
{
  "mcpServers": {
    "etoro-trading": {
      "type": "http",
      "url": "https://mcp.public-api.etoro.com"
    }
  }
}
```

認證採用 OAuth。當外掛連線時，Cursor 會提示 eToro 登入，無需設定客戶端 ID 或 API 金鑰。每個使用者以自己的 eToro 帳戶登入，並同意連線可能使用的許可權範圍；`get-my-profile-and-scopes` 工具會顯示已連線的帳戶和授予的許可權範圍。

<a id="before-you-connect"></a>

## 連線前

您需要一個 eToro 帳戶。伺服器同時操作 **Demo**（虛擬資金）和 **Real** 帳戶，相同的工具適用於兩種帳戶，因此請確認請求目標的帳戶型別，再讓代理下達交易。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 投資組合 | 完整投資組合摘要，包含總計和每種工具的盈虧、所有帳戶型別（交易、現金、加密貨幣、期權及其他）的餘額、當前持倉和待處理訂單（包括跟單交易映象）以及已結束交易歷史 |
| 交易 | `prepare-trade` 根據實時資格、報價、成本和可用餘額驗證訂單，並返回確認塊；`place-trade` 提交該訂單並等待結果。`prepare-close` / `place-close` 對關閉持倉執行相同操作 |
| 研究 | 批次檢視最多 100 種工具（價格、差價、表現、可交易性）、最多 100 位交易者的公開資料和表現（用於跟單交易研究）以及使用者關注列表 |
| API 存取 | 按標籤或關鍵字瀏覽公開 API 路由目錄，讀取任何路由的 OpenAPI 規格，並對任何路由執行讀取或寫入請求 |

託管執行環境是工具名稱和結構的來源真相。

<a id="notes"></a>

## 注意事項

- **您對代理下達的每一筆交易負有責任。** 伺服器將交易限制在 `prepare-trade` / `prepare-close` 發出的短期確認令牌之後，其工具描述指示代理向您顯示確認塊並在每次 `place-*` 或 `execute-write` 呼叫前獲得明確批准。如果您指示代理不詢問而直接行動，它仍可以下達訂單。
- 在 Real 帳戶上下達的訂單是實時交易。eToro 建議先在 Demo 帳戶上開始。
- `execute-write` 可以訪問公開 API 中任何改變狀態的路線，包括轉帳和社交帖子，不僅限於交易。

<a id="docs"></a>

## 文件

- Cursor 整合指南：https://api-portal.etoro.com/core/vibe-code/cursor
- 代理技能和伺服器概述：https://api-portal.etoro.com/core/ai-agents/etoro-skill
- API 參考：https://api-portal.etoro.com/
- 伺服器 URL：https://mcp.public-api.etoro.com

標誌是 eToro 的官方標誌。

<a id="license"></a>

## 許可

MIT
