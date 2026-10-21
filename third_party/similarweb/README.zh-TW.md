# Similarweb

Cursor 外掛，透過 Similarweb 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Similarweb](https://www.similarweb.com)。

在不離開編輯器的情況下，取得 Similarweb 的數位情報資料：網站流量與互動、競爭對手比較、目標受眾人口統計與關鍵字指標，以供研究之用。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Similarweb**。
3. 點選 **安裝**，然後設定您的 Similarweb API 金鑰（如下）。

或在聊天中執行 `/add-plugin similarweb`。

## MCP

```json
{
  "mcpServers": {
    "similarweb": {
      "type": "http",
      "url": "https://mcp.similarweb.com",
      "headers": {
        "api-key": "${SIMILARWEB_API_KEY}"
      }
    }
  }
}
```

驗證方式是 Similarweb **API 金鑰**，透過 `api-key` 標頭傳送（小寫、以連字號分隔，不是 `Authorization`）。從 **帳戶設定 → 資料工具 → REST API** 複製，然後在 **Dashboard → 外掛 → 設定** 中設定。

<a id="before-you-connect"></a>

## 在連線之前

您需要具有 API 存取權的 Similarweb 訂閱方案：API-only、Business 或 Enterprise。呼叫會扣減標準 API 資料點數。如果帳戶設定中看不到 REST API 區段，帳戶管理員必須產生金鑰。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 網站指標 | 流量與互動、流量來源、網站排名、相似網站、推薦連結、受眾重疊、人口統計、技術、區段與 PPC 支出 |
| 搜尋指標 | SERP 玩家與 SERP 點選 |
| 應用指標 | 應用安裝滲透率、應用商店排名與應用詳細資料 |

託管執行環境是工具名稱與結構的來源真相。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以 API 金鑰所附的許可權與點數餘額執行。
- Similarweb 自家的 Cursor 指南會透過 `npx mcp-remote` 導引。這並非必要：Cursor 直接連線遠端端點並傳送 `api-key` 標頭，這正是 Similarweb 為 Microsoft Copilot Studio 所記載的相同形式。
- Similarweb 執行兩個文件入口，`docs.similarweb.com` 與 `developers.similarweb.com`，其 MCP 路徑有所差異。兩者都指向相同的端點。

<a id="docs"></a>

## 文件

- Similarweb MCP：https://docs.similarweb.com/api-v5/similarweb-mcp
- Similarweb MCP（開發者入口）：https://developers.similarweb.com/docs/similarweb-mcp
- Cursor 整合指南：https://developers.similarweb.com/docs/cursor-mcp-integration
- 伺服器 URL：https://mcp.similarweb.com

標誌是 Similarweb 的官方標記，來自 `similarweb` GitHub 組織。

<a id="license"></a>

## 授權

MIT
