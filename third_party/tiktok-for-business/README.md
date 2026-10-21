# TikTok for Business

Cursor 外掛，透過 TikTok 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [TikTok for Business](https://ads.tiktok.com)。

管理 TikTok 廣告活動、廣告群組、廣告、受眾、素材和報告。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **TikTok for Business**。
3. 點選 **安裝**，然後完成 TikTok for Business 登入提示。

或在聊天中執行 `/add-plugin tiktok-for-business`。

## MCP

```json
{
  "mcpServers": {
    "tiktok-for-business": {
      "type": "http",
      "url": "https://business-api.tiktok.com/open_mcp/tt-ads-mcp-layer"
    }
  }
}
```

這是 TikTok 的漸進披露伺服器。它在連線時載入約 40 個核心工具，並在代理需要時載入其約 400 個工具中的其餘部分，這使工具列表保持簡短。TikTok 也在 `https://business-api.tiktok.com/open_mcp/tt-ads-mcp-flat` 執行一個完整披露伺服器，該伺服器一次載入所有工具。

認證是 OAuth 搭配 Dynamic Client Registration 和 PKCE。Cursor 在外掛連線時自行註冊並開啟 TikTok 的登入頁面：使用您的 TikTok for Business 帳戶（Ads Manager）登入，檢視許可權，然後點選 **授權**。不需要設定 API 金鑰、開發者應用或客戶 ID。授權有效期為 30 天，之後您需要再次登入。

<a id="what-agents-can-do"></a>

## 代理可以做的事

工具包圍 TikTok Business API v1.3 端點：

| 區域 | 示例 |
| --- | --- |
| 帳戶 | 授權廣告商、廣告商資訊、Business Center |
| 廣告活動和廣告 | 廣告活動、廣告群組和廣告，包括 Upgraded Smart+ |
| 報告 | 按維度和指標的效能報告 |
| 受眾 | 自訂和儲存的受眾、畫素、自訂轉換 |
| 素材 | 圖片、影片、音樂、身份、素材組合 |
| 商品庫 | 商品庫、產品集、feed 和產品 |

託管 runtime 是工具名稱和 schema 的來源真相。

<a id="notes"></a>

## 注意事項

- 某些工具會建立、更新和更改廣告活動、廣告群組和廣告的狀態，這可能消耗廣告預算。在批准之前，請檢視每個工具呼叫。
- 每個工具每個使用者每秒最多 6 次請求。
- 若要撤銷存取權，請移除外掛。

<a id="docs"></a>

## 文件

- TikTok for Business MCP Server: https://business-api.tiktok.com/portal/docs/tiktok-ads-mcp-server/v1.3
- 連線指南: https://business-api.tiktok.com/portal/docs/how-to-connect-to-tiktok-for-business-mcp-server/v1.3
- 可用工具: https://business-api.tiktok.com/portal/docs/available-tools-in-tiktok-for-business-mcp-server/v1.3
- 伺服器 URL: https://business-api.tiktok.com/open_mcp/tt-ads-mcp-layer

Logo 是 TikTok 官方應用圖示。

<a id="license"></a>

## 授權

MIT
