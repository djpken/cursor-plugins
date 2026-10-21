# Hunter

Cursor 外掛，透過 Hunter 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Hunter](https://hunter.io)。

尋找並驗證專業電子郵件地址，列出任何公司域名背後的聯絡人，發現符合特定背景的公司，豐富人員與公司資料，並將聯絡人儲存為潛在客戶。

官方 Cursor 設定：https://hunter.io/agents.md

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Hunter**。
3. 點選 **安裝**，然後設定您的 Hunter API 金鑰（如下）。

或在聊天中執行 `/add-plugin hunter`。

## MCP

```json
{
  "mcpServers": {
    "hunter": {
      "type": "http",
      "url": "https://mcp.hunter.io/mcp",
      "headers": {
        "X-API-Key": "${HUNTER_API_KEY}"
      }
    }
  }
}
```

驗證是 Hunter **API 金鑰**，透過 `X-API-Key` 標頭傳送。請在 https://hunter.io/api-keys 建立一個金鑰（免費方案即可），然後在 **Dashboard → 外掛 → 設定** 中設定。請勿提交金鑰。

<a id="before-you-connect"></a>

## 在連線之前

您需要一個 Hunter 帳戶和 API 金鑰。MCP 伺服器適用於所有 Hunter 方案，包括免費方案，並使用該金鑰所附的方案與點數。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 探索 | 依產業、規模、地點、技術等條件尋找公司，或以自然語言描述目標（免費，不需點數） |
| 域名搜尋 | 列出公司域名背後的人員與電子郵件地址，並提供部門與職級篩選 |
| 電子郵件尋找 | 從姓名與公司或域名，找出專業人士最可能的電子郵件地址 |
| 電子郵件驗證 | 檢查電子郵件地址是否可投遞，並檢視信心分數與來源 |
| 資料豐富化 | 從電子郵件地址或域名提取人員與公司資料 |
| 潛在客戶 | 建立、更新與組織潛在客戶及潛在客戶列表；將潛在客戶推送到連線的 CRM |
| 行銷活動 | 將收件人加入行銷活動，並啟動外聯序列 |

託管執行環境是工具名稱與結構的來源基準。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以 API 金鑰所附的許可權與點數執行。域名搜尋、電子郵件尋找、電子郵件驗證與資料豐富化呼叫會消耗點數，如同在 Hunter 應用中操作；探索是免費的。
- 此外掛遵循 Hunter 官方 Cursor 打包方式：Streamable HTTP 位於 `https://mcp.hunter.io/mcp`，使用 `X-API-Key`。Hunter 也為某些客戶端提供 OAuth 說明；請勿使用 OAuth 於此外掛。
- Hunter 的 `test-api-key` 僅適用於 REST API；MCP 伺服器會以 401 拒絕它。
- `hunter-io/hunter-mcp` GitHub 儲存庫是 Hunter 舊的本地（stdio）伺服器，已封存。Hunter 將所有客戶端導向上述遠端伺服器。

<a id="docs"></a>

## 檔案

- 代理設定說明：https://hunter.io/agents.md
- Hunter MCP：https://hunter.io/mcp
- API 金鑰：https://hunter.io/api-keys
- API 參考（MCP 區段）：https://hunter.io/api-documentation/v2#mcp
- 伺服器 URL：https://mcp.hunter.io/mcp

標誌是 Hunter 官方狐狸標誌，來自 `hunter-io` GitHub 組織。

<a id="license"></a>

## 授權

MIT
