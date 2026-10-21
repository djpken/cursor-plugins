# Buffer

Cursor 外掛，透過 Buffer 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Buffer](https://buffer.com)。

從聊天中即可讀取已連線的社交頻道、瀏覽排程與草稿、撰寫、排程、編輯與發布貼文、捕捉想法、重用貼文模板，並取得貼文分析。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Buffer**。
3. 點選 **安裝**，然後完成 Buffer 登入提示。

或在聊天中執行 `/add-plugin buffer`。

## MCP

```json
{
  "mcpServers": {
    "buffer": {
      "type": "http",
      "url": "https://mcp.buffer.com/mcp"
    }
  }
}
```

驗證方式為 OAuth。當外掛連線時，Cursor 會提示 Buffer 登入，無需設定客戶端 ID 或 API 金鑰。每位使用者以自己的 Buffer 帳戶登入。

<a id="before-you-connect"></a>

## 連線前

你需要一個 Buffer 帳戶（[註冊](https://buffer.com)），並至少連線一個社交頻道。每個 Buffer 方案都包含 MCP 存取，包括免費方案。請求會計入與其他 API 客戶相同的[速率限制](https://developers.buffer.com/guides/api-limits.html)，配額依方案而定；每個工具回應都會報告剩餘配額。連線涵蓋整個帳戶，包括所有組織與頻道，因此若你屬於多個組織，代理會詢問你指的是哪一個。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 帳戶 | `get_account` 返回已登入的帳戶、其組織（含方案限制與成員數量）、帳戶時區及其當前本地時間，代理會用它將「明天下午 5 點」轉為實際時間戳 |
| 頻道 | `list_channels` 列出組織連線的社交帳戶（服務、型別、頭像、連線狀態）；`get_channel` 新增頻道的發布排程、發布目標、排程狀態、時區、連結縮短，以及服務特定資料，例如 Pinterest 板 |
| 貼文 | `list_posts` 依頻道、狀態（`draft`、`needs_approval`、`scheduled`、`sending`、`sent`、`error`）、標籤與日期範圍進行篩選，並可包含每篇貼文的指標；`get_post` 完整讀取一篇貼文，包括任何發布錯誤；`create_post` 在單一頻道上排程或發布，支援排程、立即分享、下一個分享或自訂時間模式、媒體、串文、連結預覽、服務 metadata，以及儲存為草稿的選項；`edit_post` 修改文字、媒體、標籤或排程；`delete_post` 永久移除一篇貼文 |
| 想法 | `list_ideas` 與 `list_idea_groups` 瀏覽想法板；`create_idea` 儲存一個概念，包含標題、文字、媒體、標籤、目標服務與目標日期，但不排程任何內容 |
| 貼文模板 | `list_post_templates`、`get_post_template`、`create_post_template`、`update_post_template` 與 `delete_post_template` 管理可重用模板，並以 `{{placeholders}}` 設定為私人或內部可見性 |
| 分析 | `get_aggregated_post_metrics` 在最多 365 天的日期範圍內，統計貼文的反應、留言，以及（若每個頻道支援）觸達、曝光與互動率 |
| GraphQL | `introspect_schema` 返回完整的 Buffer API schema；`execute_query` 執行唯讀查詢，`execute_mutation` 執行 mutation，用以處理上述工具未涵蓋的任何操作 |

伺服器也將 schema 暴露為 `buffer://schema` 資源，以及 `review_weekly_posts` 提示，將本週已排程的貼文依頻道分組。

託管 runtime 是工具名稱與 schema 的來源真相。

<a id="notes"></a>

## 注意事項

- **發布會直接影響真實社交帳戶。** `create_post` 搭配 `shareNow` 會立即發布，`automatic` 排程發布不會再次檢查；`delete_post` 無法取消。在讓代理發布或排程之前，請確認頻道、文字與時間，並保留 Cursor 的寫入工具核准提示。
- `saveToDraft` 搭配 `create_post` 會將貼文保留在排程中供審核，而非排程發布；`schedulingType: notification` 會傳送提醒讓你手動發布，而非替你發布。
- `execute_mutation` 可觸達 Buffer GraphQL API 中任何改變狀態的操作，不僅限於貼文。
- 貼文指標每天更新一次，因此分析可能比社交網路遲最多一天。

<a id="docs"></a>

## 文件

- MCP 整合指南：https://developers.buffer.com/guides/integrations/mcp.html
- MCP 概覽：https://buffer.com/mcp
- API 參考：https://developers.buffer.com/
- 伺服器 URL：https://mcp.buffer.com/mcp

Logo 是 Buffer 的官方標誌。

<a id="license"></a>

## 授權

MIT
