# Calendly

Cursor 外掛，透過 Calendly 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Calendly](https://calendly.com)。

檢查與更新可用時間、產生排程連結，以及在已登入的 Calendly 帳戶中預約、取消或重新安排會議。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Calendly**。
3. 點選 **安裝**，然後完成 Calendly 登入提示。

或在聊天中執行 `/add-plugin calendly`。

## MCP

```json
{
  "mcpServers": {
    "calendly": {
      "type": "http",
      "url": "https://mcp.calendly.com/"
    }
  }
}
```

驗證採用 OAuth 2.1，搭配 PKCE 與 Dynamic Client Registration。Cursor 會自行註冊，並在外掛連線時提示 Calendly 登入，不需要設定客戶端 ID 或個人存取令牌。Calendly 不接受個人存取令牌於 MCP 伺服器上。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 能力 |
| --- | --- |
| 活動型別 | 列出與檢視活動型別 |
| 已排程活動 | 查詢已排程活動與受邀者，並取消或重新安排 |
| 可用時間 | 可用時間排程與忙碌時間 |
| 排程連結 | 產生單次使用與共用排程連結 |
| 路由與組織 | 路由表與組織管理 |

託管執行環境是工具名稱與結構的來源基準。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Calendly 使用者身份執行，並使用 `mcp:scheduling:read` 與 `mcp:scheduling:write` 許可權範圍。
- 伺服器可在免費方案上運作，但可使用的動作會依 Calendly 方案而有所不同。
- Calendly 自行託管伺服器，不支援自行託管或本地部署。
- 端點為不含尾端斜線的純網域，即 `https://mcp.calendly.com/`，而非 `/mcp`。

<a id="docs"></a>

## 檔案

- Calendly MCP 伺服器：https://developer.calendly.com/calendly-mcp-server
- 支援的工具：https://developer.calendly.com/supported-tools
- 將 Calendly 連線到你的 AI 工具：https://calendly.com/help/connect-calendly-to-your-ai-tools
- 伺服器 URL：https://mcp.calendly.com/

標誌為 Calendly 官方標記，取自 `calendly` GitHub 組織。

<a id="license"></a>

## 授權

MIT
