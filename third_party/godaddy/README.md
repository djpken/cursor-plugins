# GoDaddy

Cursor 外掛，透過 GoDaddy 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [GoDaddy](https://www.godaddy.com)。

從公開域名資料中，腦力激盪域名名稱、批次檢查可用性與價格，並取得註冊連結。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **GoDaddy**。
3. 點選 **安裝**。不需要登入步驟。

或在聊天中執行 `/add-plugin godaddy`。

## MCP

```json
{
  "mcpServers": {
    "godaddy": {
      "type": "http",
      "url": "https://api.godaddy.com/v1/domains/mcp"
    }
  }
}
```

無需驗證。GoDaddy 的域名 MCP 伺服器僅使用公開域名資料，因此不需要帳號、API 金鑰或登入步驟。每個客戶 IP 有速率限制。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 可用性 | 檢查一個域名或一次最多 1,000 個域名，包含價格與註冊連結 |
| 建議 | 從關鍵字或商業描述產生替代域名名稱 |

託管執行環境是工具名稱與結構的來源。連線後，將 `domains_check_availability` 作為僅讀的冒煙測試呼叫。

<a id="notes"></a>

## 注意事項

- 伺服器嚴格僅讀公開資料。無法註冊或轉移域名、更改 DNS 或進行購買。
- 因為沒有帳號上下文，無法檢視或管理您已擁有的域名。
- 要求 `GODADDY_API_KEY` 的社群 `godaddy-mcp` 套件是包覆 REST API 的第三方專案，與此外掛無關。此外掛使用 GoDaddy 自家的託管端點。

<a id="docs"></a>

## 檔案

- GoDaddy MCP 伺服器：https://developer.godaddy.com/en/docs/api-users/mcp
- 伺服器 URL：https://api.godaddy.com/v1/domains/mcp

標誌是 GoDaddy 官方標誌，來自 `godaddy` GitHub 組織。

<a id="license"></a>

## 授權

MIT
