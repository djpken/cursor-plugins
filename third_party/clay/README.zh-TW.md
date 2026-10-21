# Clay

Cursor 外掛，透過 Clay 官方託管的 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Clay](https://www.clay.com)。

在 150 多個資料提供商中尋找並豐富個人和公司資訊，執行 AI 研究代理（Claygent），並從已登入的 Clay 工作區觸發團隊核准的 Clay 工作流程。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Clay**。
3. 點選 **安裝**，然後完成 Clay 登入提示。

或在聊天中執行 `/add-plugin clay`。

## MCP

```json
{
  "mcpServers": {
    "clay": {
      "type": "http",
      "url": "https://api.clay.com/v3/mcp"
    }
  }
}
```

驗證是針對 Clay 的 OAuth 2.1，使用 Dynamic Client Registration（DCR）和 PKCE。當外掛連線時，Cursor 會自行註冊並提示 Clay 登入，不需要設定 API 金鑰或客戶 ID。

<a id="before-you-connect"></a>

## 連線前

你需要一個 Clay 帳戶，並能存取一個工作區。

你的 Clay 工作區管理員可能需要在 Clay 工作區 **設定 → MCP** 中允許 MCP 客戶端連線。如果登入成功但工具失敗，請與管理員確認。

<a id="what-agents-can-do"></a>

## 代理能做什麼

| 類別 | 功能 |
| --- | --- |
| 個人與公司 | 以自然語言條件搜尋 Clay 的資料宇宙 |
| 豐富資料 | 在 150 多個提供商中提取電子郵件、電話號碼、公司統計資料、技術統計資料及其他資料點 |
| 研究 | 向 Claygent 詢問關於帳戶和聯絡人的開放式問題 |
| 工作流程 | 觸發團隊已核准供 MCP 存取使用的 Clay 表格和工作流程 |

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Clay 使用者身份執行，且不能超過該使用者許可權。
- 工作流程觸發僅限於在 Clay 工作區中核準供 MCP 存取的工作流程。
- 連線會出現在你的 Clay 工作區的 MCP 客戶端列表中，標示為 Cursor 客戶端名稱。
- 隨時從 Clay 工作區 **設定 → MCP** 撤銷存取權。

<a id="docs"></a>

## 檔案

- 連線 Clay MCP：https://university.clay.com/docs/connect-to-clay-mcp
- 伺服器 URL：https://api.clay.com/v3/mcp

<a id="license"></a>

## 授權

MIT
