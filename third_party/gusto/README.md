# Gusto

Cursor 外掛，透過 Gusto 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Gusto](https://gusto.com)。

處理薪資、員工、承包商、休假和報告。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Gusto**。
3. 點選 **安裝**，然後完成 Gusto 登入提示。

或在聊天中執行 `/add-plugin gusto`。

## MCP

```json
{
  "mcpServers": {
    "gusto": {
      "type": "http",
      "url": "https://mcp.api.gusto.com/"
    }
  }
}
```

驗證採用 OAuth，包含 Dynamic Client Registration 和 PKCE。Cursor 會自行註冊，並在外掛連線時開啟 Gusto 登入頁：登入、如果您有多家公司則選擇公司、選擇要分享的資料類別，然後授權。不需要設定 API 金鑰或客戶 ID。

<a id="before-you-connect"></a>

## 連線前

您需要具有全域或完整管理員許可權的 Gusto 帳戶。

<a id="what-agents-can-do"></a>

## 代理可以做的事

存取僅限於您在登入時授予的資料類別。伺服器涵蓋：

| 區域 | 範例 |
| --- | --- |
| 公司 | 公司詳細資料、地點、部門、入職狀態、銀行帳戶 |
| 薪資 | 薪資、薪資排程、時數和收入、薪資報告、執行薪資 |
| 人員 | 員工、職位、薪酬、福利、自訂欄位 |
| 承包商 | 承包商和承包商付款 |
| 時間 | 休假政策和請求、時間表 |
| 報告 | 公司報告 |

呼叫 `get_token_info` 檢視當前連線具有哪些範圍。託管 runtime 是工具名稱和 schema 的來源。

<a id="notes"></a>

## 注意事項

- 某些工具會更改 Gusto 中的資料，包括執行薪資。在批准之前檢視每個工具呼叫，並對此伺服器保持自動執行關閉。
- Gusto 建議將其伺服器獨立使用，不要在同一聊天中搭配其他 MCP 伺服器，以免薪資和員工資料傳給其他服務。
- 在您的客戶設定中選擇退出模型訓練。Gusto 承諾不對您的資料進行訓練，但這不涵蓋您連線的客戶。
- 若要更改或撤銷存取權，請斷開伺服器並重新登入，或移除外掛。

<a id="docs"></a>

## 文件

- Gusto MCP 文件：https://docs.gusto.com/app-integrations/docs/mcp
- 產品頁面：https://gusto.com/product/integrations/gusto-mcp
- 伺服器 URL：https://mcp.api.gusto.com/
- 支援：mcp@gusto.com

標誌是 Gusto 的官方標誌。

<a id="license"></a>

## 授權

MIT
