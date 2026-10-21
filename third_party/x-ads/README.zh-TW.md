<a id="x-ads"></a>

# X 廣告

Cursor 外掛，透過 X 官方託管的 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器（位於 `https://ads-api.x.com/mcp`）將代理連線到 [X Ads API](https://docs.x.com/x-ads-api/introduction)。

此外掛使用 OAuth 以您的 X 帳戶登入，並可與您能存取的所有廣告帳戶配合使用。代理可以管理廣告活動、建立廣告、設定畫素與轉換追蹤，以及提取效能統計資料。

這是與 [X](../x/) 外掛不同的外掛：廣告 MCP 伺服器位於不同的子域名（`ads-api.x.com` 對 `api.x.com`），且需要不同的 OAuth 許可權範圍，因此需要獨立授權。安裝兩者意味著需登入兩次，每個外掛各一次。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **X 廣告**。
3. 點選 **安裝**，然後在提示時完成 OAuth 登入。

或在聊天中執行 `/add-plugin x-ads`。

## MCP

```json
{
  "mcpServers": {
    "x-ads": {
      "type": "http",
      "url": "https://ads-api.x.com/mcp",
      "auth": {
        "CLIENT_ID": "NGdZYmo4VVp2T1BnRG55NlExOGQ6MTpjaQ",
        "scopes": [
          "ads.read",
          "ads.write",
          "media.write",
          "offline.access"
        ]
      }
    }
  }
}
```

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 帳戶 | 讀取您的廣告帳戶、資金工具與設定 |
| 廣告活動 | 建立、更新、暫停與讀取廣告活動及廣告專案 |
| 廣告與創意素材 | 建立與管理促銷廣告與創意素材 |
| 受眾 | 讀取與管理目標設定與受眾 |
| 畫素與轉換 | 設定 X 畫素與轉換追蹤 |
| 分析 | 提取廣告活動、廣告專案與廣告的效能統計資料 |

<a id="setup"></a>

## 設定

無需貼上任何令牌，外掛內已包含 X 的 OAuth 客戶端 ID（與 X 聯結器使用的相同 ID），並請求以下許可權範圍。首次使用時，Cursor 會開啟瀏覽器視窗，讓您登入 X 並核准存取。`offline.access` 許可權範圍允許 Cursor 自動重新整理會話，因此您只需登入一次。

請求會在您的使用者環境中執行，針對您的 X 帳戶可存取的所有廣告帳戶。您可以隨時從您的 X 帳戶的連線應用設定中撤銷存取權。

<a id="scopes-requested"></a>

## 請求的許可權範圍

`ads.read`, `ads.write`, `media.write`, `offline.access`

<a id="docs"></a>

## 文件

- X Ads API：https://docs.x.com/x-ads-api/introduction
- OAuth 受保護資源元資料：https://ads-api.x.com/.well-known/oauth-protected-resource
- 驗證概述：https://docs.x.com/fundamentals/authentication/overview

標誌是 X 官方品牌工具包](https://about.x.com/en/who-we-are/brand-toolkit)中的官方標誌，放置在與 X 應用圖示一致的黑色底板上。

<a id="license"></a>

## 授權

MIT
