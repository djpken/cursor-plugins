# Coda

Cursor 外掛，透過 Coda 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Coda](https://coda.io)。

搜尋並讀取 Coda 檔案、頁面和表格，並以登入使用者相同的存取權建立或更新頁面和行。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Coda**。
3. 點選 **安裝**，然後完成 Coda 登入提示。

或在聊天中執行 `/add-plugin coda`。

## MCP

```json
{
  "mcpServers": {
    "coda": {
      "type": "http",
      "url": "https://docs.superhuman.com/apis/mcp"
    }
  }
}
```

驗證採用 OAuth 2 搭配 PKCE。Cursor 會在外掛連線時提示登入。OAuth 連線自動同時涵蓋讀取與寫入。

<a id="before-you-connect"></a>

## 連線之前

Coda 現在是 **Superhuman Docs**，MCP 伺服器也隨之移至 `docs.superhuman.com`。您的現有 Coda 帳戶、檔案和方案均不變。

MCP 伺服器目前處於 beta 階段。付費方案的 Doc Maker 享有完整體驗；免費方案的 Doc Maker 和 Editors 獲得有限的「免費試用」（每週 30 次請求，每月 60 次），且 Editors 僅限讀取工具。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 搜尋 | 搜尋檔案與頁面 |
| 檔案與頁面 | 讀取、建立和更新檔案與頁面 |
| 表格與行 | 讀取和寫入表格行 |
| 輔助工具 | `url_decode` 將貼上的 Coda URL 轉換為其他工具所需的 ID |

託管執行環境是工具名稱與結構的來源基準。

<a id="notes"></a>

## 注意事項

- 工具呼叫以授權連線的使用者身份執行。
- 較舊的 `https://coda.io/apis/mcp` 地址仍服務現有連線，但 Superhuman 的變更記錄指出新設定應使用 `docs.superhuman.com/apis/mcp`，因此此外掛採用該地址。
- Superhuman 也接受以 `Authorization: Bearer <token>` 傳送的個人存取令牌，讓您選擇僅讀取、僅寫入或讀取+寫入，而非 OAuth 的讀取+寫入。令牌必須以限制型別 **MCP** 建立，否則伺服器會返回 401。Superhuman 目前建議 Cursor 使用令牌方式，因為處理重新整理令牌較佳。
- `coda-mcp` npm 套件是第三方維護者提供的社群本地伺服器，與此託管端點無關。

<a id="docs"></a>

## 檔案

- 連線至 Coda MCP：https://help.superhuman.com/hc/en-us/articles/46210076980365-Connect-to-the-Coda-MCP
- 安全建議：https://help.superhuman.com/hc/en-us/articles/46210118248205-Security-recommendations-for-the-Coda-MCP
- 變更內容：Coda 成為 Superhuman Docs：https://help.superhuman.com/hc/en-us/articles/46210093285773-What-s-changing-Coda-becomes-Superhuman-Docs
- 伺服器 URL：https://docs.superhuman.com/apis/mcp

標誌是 Coda 官方標記，來自 `coda` GitHub 組織。

<a id="license"></a>

## 授權

MIT
