# Trello

Cursor 外掛，透過 Atlassian 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Trello](https://trello.com)。

瀏覽和建立看板、檢視和移動列表、建立、更新、移動、封存和完成卡片並設定標籤和截止日期、管理檢查列表、將卡片捕捉到您的收件匣、安排 Planner 專注時間，並在整個工作區中搜尋。

這是 Trello 自己的 MCP 伺服器，僅涵蓋 Trello 資料。它與 Cursor 內建的 Atlassian 伺服器（涵蓋 Jira 和 Confluence）是分開的；如果您同時使用 Trello 與這些產品，請安裝兩者。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Trello**。
3. 點選 **安裝**，然後完成 Atlassian 登入提示並選擇要授權的 Trello 工作區。

或在聊天中執行 `/add-plugin trello`。

## MCP

```json
{
  "mcpServers": {
    "trello": {
      "type": "http",
      "url": "https://mcp.trello.com/v1"
    }
  }
}
```

驗證方式是 OAuth。Cursor 會在外掛連線時提示 Atlassian 登入，因此沒有需要設定的客戶 ID 或 API 金鑰。每個使用者以自己的 Atlassian 帳戶登入，在同意畫面選擇一個 Trello 工作區，並批准連線可能使用的讀取、寫入和搜尋許可權。

<a id="before-you-connect"></a>

## 連線前

您需要任何方案的 Trello 帳戶。每個連線在啟動時僅限於一個 Trello 工作區；斷開並重新連線以切換工作區或授予您略過的許可權。建立 Planner 專注時間事件需要 Trello Premium 或 Enterprise，而收件匣和 Planner 許可權可能無法適用於您自己組織以外的工作區。Enterprise 管理員可以在 Atlassian Administration 中允許或阻止伺服器及其讀取/寫入許可權。

<a id="what-agents-can-do"></a>

## 代理可以做的事

| 類別 | 能力 |
| --- | --- |
| 成員 | `trelloReadMember` 返回登入使用者のプロフィールと預設，包括用來解析相對截止日期的時區 |
| 看板 | `trelloReadBoard` 透過 URL 或 id 取得看板，列出看板，並透過工作區列出看板並帶有可見性濾器和遊標分頁，包括看板的標籤；`trelloWriteBoard` 建立看板 |
| 列表 | `trelloWriteList` 在看板上建立列表並移動它們，`pos` 用於有序建立 |
| 卡片 | `trelloReadCard` 透過 URL 或 id 取得卡片；`trelloWriteCard` 建立、更新、移動、封存和標記卡片完成，設定截止日期，並附加或移除標籤 |
| 檢查列表 | `trelloWriteChecklist` 在卡片上建立和更新檢查列表並新增或更新檢查列表專案 |
| 收件匣 | `trelloReadInbox` 和 `trelloWriteInbox` 檢視、建立、更新和封存您個人收件匣中的卡片，這與看板是分開的 |
| Planner | `trelloReadPlanner` 從連線的 Google 或 Outlook 日曆讀取日曆事件；`trelloWritePlanner` 建立專注時間事件並將卡片連結或解除連結到它們 |
| 搜尋 | `trelloSearch` 透過關鍵字尋找卡片和看板，並可使用限定詞如 `due:` |

工具是動作分派的：每個工具接受一個 `action` 引數來選擇操作。每個 id 是 Atlassian Resource Identifier（`ari:cloud:trello::card/workspace/<workspaceId>/<cardId>`），由讀取工具返回，不是原始 Trello id 或短連結。託管執行環境是工具名稱和結構的來源真相。

<a id="notes"></a>

## 注意事項

- 寫入會落在真實看板上，您的團隊成員可以看到。請在批次移動、封存或收件匣清理之前要求代理確認。
- 伺服器沒有永久刪除；卡片和列表只能封存。
- 評論、附件、自定義欄位、卡片成員、標籤管理和看板編輯尚未公開。Atlassian 在伺服器的 README 中列出這些為計劃專案。
- 截止日期和 Planner 時間是 UTC ISO 8601；當您提供相對時間如「明天下午 1 點」時，代理應先讀取成員的時區。

<a id="docs"></a>

## 文件

- Trello MCP 伺服器 README：https://github.com/atlassian/trello-mcp-server
- Trello 支援文章：https://support.atlassian.com/trello/docs/connect-trello-to-ai-assistants-with-trello-mcp/
- 正確呼叫工具的代理技能：https://github.com/atlassian/trello-mcp-server/blob/main/skills/trello-use/SKILL.md
- 伺服器 URL：https://mcp.trello.com/v1

標誌是 Trello 的官方標記。

<a id="license"></a>

## 授權

MIT
