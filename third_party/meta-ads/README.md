<a id="meta-ads"></a>

# Meta 廣告

Cursor 外掛，透過 Meta 官方託管的 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器（位於 `https://mcp.facebook.com/ads`），將代理連線到 [Meta 廣告平臺](https://www.facebook.com/business/ads)。

此外掛使用 Facebook 商業登入進行登入，並與您選擇分享的商業資產組合和廣告帳號配合運作。代理可以管理廣告活動、廣告組合、廣告、受眾和產品目錄，並從 Facebook 和 Instagram 提取效能洞察。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Meta 廣告**。
3. 點選 **安裝**，然後在提示時完成 Facebook 登入。

或在聊天中執行 `/add-plugin meta-ads`。

## MCP

```json
{
  "mcpServers": {
    "meta-ads": {
      "type": "http",
      "url": "https://mcp.facebook.com/ads",
      "auth": {
        "CLIENT_ID": "1854843612533787"
      }
    }
  }
}
```

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 帳號 | 讀取您分享的廣告帳號和商業資產組合 |
| 洞察 | 提取效能洞察 |
| 廣告活動 | 建立、更新和讀取廣告活動 |
| 廣告組合 | 建立、更新和讀取廣告組合 |
| 廣告與創意 | 建立和管理廣告與創意 |
| 受眾 | 讀取和管理受眾 |
| 資料集與畫素 | 讀取和管理資料集與畫素 |
| 產品目錄 | 讀取和管理產品目錄 |

託管伺服器是工具名稱和結構的來源真相。

<a id="setup"></a>

## 設定

無需貼上任何令牌。外掛內含 Cursor 的 Meta 應用 ID，因為 Meta 不接受來自 Cursor 的動態客戶註冊。Meta 應用 ID 是公開資訊，外掛不需要客戶秘密。

首次使用時，Cursor 會開啟瀏覽器視窗進行 Facebook 商業登入。登入後，選擇要分享的商業資產組合和廣告帳號，並核准存取。請求以您的身份執行，僅針對您分享的資產。

您隨時可以在 Facebook 的 **設定 → 商業整合** 中撤銷存取權。

<a id="notes"></a>

## 注意事項

- Meta 將伺服器稱為 beta 版本，並逐步為每個廣告帳號和工具推出。如果一個工具報告 `is_ads_mcp_enabled = false`，該廣告帳號尚未獲得存取權。
- 伺服器可以進行寫入操作，包括建立廣告活動和更改預算。請在核準每個工具呼叫之前審視其內容。
- 呼叫會共用 Marketing API 對每個廣告帳號的速率限制。

<a id="docs"></a>

## 文件

- Ads MCP 伺服器，開始使用：https://developers.facebook.com/documentation/ads-commerce/ads-ai-connectors/ads-mcp-server/ads-mcp-server-get-started
- Meta 商業幫助中心：https://www.facebook.com/business/help/1456422242197840

標誌是 Meta 的標記，來自 [Simple Icons](https://simpleicons.org/)，以 Meta 藍色（`#0081FB`）放在白色底板上。

<a id="license"></a>

## 授權

MIT
