# X

Cursor 外掛，透過 X 官方託管的 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器（位於 `https://api.x.com/mcp`）將代理連線到 [X API](https://docs.x.com)。

此外掛透過 OAuth 以您的 X 帳號登入。它不再僅限讀取：除了搜尋和讀取公開的 X 資料之外，代理還可以管理您的列表、書籤、封鎖和靜音，呼叫 X Chat 端點，以及安排 X 電話。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **X**。
3. 點選 **安裝**，然後在提示時完成 OAuth 登入。

或在聊天中執行 `/add-plugin x`。

## MCP

```json
{
  "mcpServers": {
    "x": {
      "type": "http",
      "url": "https://api.x.com/mcp",
      "auth": {
        "CLIENT_ID": "NGdZYmo4VVp2T1BnRG55NlExOGQ6MTpjaQ",
        "scopes": [
          "tweet.read",
          "users.read",
          "follows.read",
          "space.read",
          "mute.read",
          "like.read",
          "list.read",
          "list.write",
          "block.read",
          "block.write",
          "bookmark.read",
          "bookmark.write",
          "dm.read",
          "dm.write",
          "calls.read",
          "calls.write",
          "media.write",
          "developer.billing.write",
          "developer.write",
          "offline.access"
        ]
      }
    }
  }
}
```

<a id="what-agents-can-do"></a>

## 代理可以做的事

| 分類 | 功能 |
| --- | --- |
| 帖子 | 取得帖子，檢視喜歡者 / 轉貼者 / 引用者，近期數量 |
| 搜尋 | 完整存檔帖子搜尋、使用者搜尋、新聞搜尋 |
| 使用者 | 透過 ID 或帳號查詢使用者；讀取使用者帖子、時間軸和提及 |
| 新聞與趨勢 | 取得新聞報導，取得某個地點（WOEID）的趨勢 |
| 追蹤與喜歡 | 讀取您的追蹤和喜歡 |
| Spaces | 搜尋 Spaces（直播或已排程），透過 ID 或主持人查詢，並讀取在 Space 中分享的帖子 |
| Communities | 搜尋社群並讀取社群詳細資訊 |
| 列表 | 讀取列表、其帖子、成員和追蹤者，以及使用者擁有、追蹤、釘選和成員列表；建立、編輯、追蹤、釘選和管理您的列表成員 |
| 書籤 | 讀取和管理您的書籤 |
| 封鎖與靜音 | 讀取您的封鎖和靜音；新增或移除封鎖 |
| Chat | 列出對話、取得事件、傳送訊息、打字和讀取回執；列出、開啟和接受訊息請求 |
| 電話 | 列出和開啟已排程的 X 電話；排程、更新或取消；回應邀請 |
| 開發者帳號 | 讀取您的 X 開發者帳號設定和信用餘額 |

發帖不包含：外掛不請求 `tweet.write` 範圍，因此代理無法以您的身份發布帖子。

<a id="setup"></a>

## 設定

不需要貼上任何 token。外掛隨附 X 的 OAuth 客戶端 ID，並請求上述範圍。首次使用時，Cursor 會開啟瀏覽器視窗，讓您登入 X 並核准存取。`offline.access` 範圍讓 Cursor 自動重新整理會話，因此您只需登入一次。

X 工具已包含在 Grok Bot，無需額費用。

請求在您的使用者環境中執行，因此會計入您的帳號速率限制。您可以隨時從您的 X 帳號的連線應用設定中撤銷存取。

<a id="scopes-requested"></a>

## 請求的範圍

`tweet.read`, `users.read`, `follows.read`, `space.read`, `mute.read`, `like.read`, `list.read`, `list.write`, `block.read`, `block.write`, `bookmark.read`, `bookmark.write`, `dm.read`, `dm.write`, `calls.read`, `calls.write`, `media.write`, `developer.billing.write`, `developer.write`, `offline.access`

<a id="x-documentation-search"></a>

## X 文件搜尋

X 也託管一個未驗證的 MCP 伺服器，供其開發者文件使用。如果您希望代理在工作時查詢端點詳細資訊，可以將它與此外掛一起加入：

```json
{
  "mcpServers": {
    "x-docs": {
      "url": "https://docs.x.com/mcp"
    }
  }
}
```

<a id="docs"></a>

## 文件

- X API 的 MCP 伺服器：https://docs.x.com/tools/mcp
- 驗證概述：https://docs.x.com/fundamentals/authentication/overview
- X API v2 OpenAPI 規格：https://api.x.com/2/openapi.json

Logo 是 X 官方標誌，取自 [X 品牌工具包](https://about.x.com/en/who-we-are/brand-toolkit)，放置在黑色底板上，與 X 自己的應用圖示相符。

<a id="license"></a>

## 授權

MIT
