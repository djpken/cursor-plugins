<a id="outreach"></a>

# 外展

Cursor 外掛，透過 Outreach 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Outreach](https://www.outreach.ai)。

搜尋序列、潛在客戶、帳戶和 Kaia 會議，然後在已登入的 Outreach 例項中建立序列、任務和記錄。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Outreach**。
3. 點選 **安裝**，然後完成 Outreach 登入提示。

或在聊天中執行 `/add-plugin outreach`。

## MCP

```json
{
  "mcpServers": {
    "outreach": {
      "type": "http",
      "url": "https://api.outreach.io/mcp"
    }
  }
}
```

驗證是針對 Outreach 的 OAuth 2.1，使用 Dynamic Client Registration (DCR) 和 PKCE。Cursor 會自行註冊，並在外掛連線時提示 Outreach 登入。不需要設定 API 金鑰或客戶 ID。

<a id="before-you-connect"></a>

## 連線前

Outreach 管理員必須先為組織啟用 MCP Server：**Administration → Organization → Org Info → Gen AI**，並將 **MCP Server** 開啟。建立動作預設為開啟；刪除動作預設為關閉。

組織還需要 Amplify 附加元件並有有效的點數。如果該開關缺失或無法點選，組織可能沒有 Amplify。請與您的 Outreach AE 確認。

您必須是該 Outreach 例項中的有效、已授權使用者。請使用您想要使用的例項的帳戶登入；瀏覽器會話是 Outreach 用來選擇組織的依據。

<a id="what-agents-can-do"></a>

## 代理可以做的事

| 分類 | 功能 |
| --- | --- |
| 對話智慧 | 搜尋 Kaia 會議、取得逐字稿和摘要，並搜尋電子郵件 |
| 序列 | 查詢序列、建立或刪除它們、加入或移除潛在客戶，並追蹤序列狀態 |
| 帳戶、潛在客戶、交易 | 透過 CRM ID 搜尋和查詢、建立或刪除記錄，並詢問帳戶或機會問題 |
| 任務與日曆 | 建立和搜尋任務；取得日曆事件 |
| 組織與結構 | 當前使用者/組織、團隊、階段，以及搜尋和建立用的篩選/輸入結構 |

託管執行環境是工具名稱和結構的來源。連線後，將 `current_user` 作為僅讀的冒煙測試執行。

<a id="notes"></a>

## 注意事項

- 工具呼叫以授權連線的 Outreach 使用者身份執行，且不能超過該使用者的人物許可權。
- Outreach MCP 不會更新現有記錄。寫入工具僅限建立、加入和刪除；刪除工具需要確認，並可能在組織層級禁用。
- 不支援客戶憑證（非使用者）驗證。身份必須是有效的授權 Outreach 使用者。
- API 流量限制適用。保持資料精簡：使用特定篩選、日期範圍和 ID，而非廣泛搜尋。
- 每個會話連線一個 Outreach 例項。同一聊天中的多個例項可能混淆工具結果。

<a id="docs"></a>

## 文件

- Outreach MCP 伺服器概述：https://support.outreach.io/support/solutions/articles/159000425158-outreach-mcp-server-overview
- 開發者入口：https://developers.outreach.io/mcp-server
- 驗證（OAuth 2.1 + DCR）：https://developers.outreach.io/mcp-server/authentication
- 工具目錄：https://developers.outreach.io/mcp-server/tool-catalog
- 伺服器 URL：https://api.outreach.io/mcp

標誌是 Outreach 官方 apple-touch 圖示（黑色上的紫色 nucleo），來自 https://www.outreach.ai.

<a id="license"></a>

## 授權

MIT
