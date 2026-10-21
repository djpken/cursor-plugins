# Klaviyo

Cursor 外掛，透過 Klaviyo 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Klaviyo](https://www.klaviyo.com)。

建立區段、草擬活動與流程、查詢個人資料與事件，並從已登入的 Klaviyo 帳戶提取報告。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Klaviyo**。
3. 點選 **安裝**，然後完成 Klaviyo 登入提示。

或在聊天中執行 `/add-plugin klaviyo`。

## MCP

```json
{
  "mcpServers": {
    "klaviyo": {
      "type": "http",
      "url": "https://mcp.klaviyo.com/mcp"
    }
  }
}
```

驗證採用 OAuth 與 Dynamic Client Registration。Cursor 會自行註冊，並在外掛連線時提示 Klaviyo 登入，無需設定 API 金鑰或客戶 ID。

<a id="before-you-connect"></a>

## 連線前

您需要 Klaviyo 帳戶的 **Owner**、**Admin** 或 **Manager** 角色。工具呼叫會以該使用者許可權執行。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 個人資料與事件 | 查詢與更新個人資料、推送事件，並管理同意與資料隱私請求 |
| 列表與區段 | 建立與查詢列表和區段，並管理成員資格 |
| 活動與流程 | 草擬、排程與檢視電子郵件、SMS 和推送活動與流程 |
| 模板與商品目錄 | 管理模板、圖片、優惠券、商品目錄與評論 |
| 報告 | 活動、流程、表單與區段效能報告 |
| 帳戶設定 | 帳戶、品牌、標籤、寄送域名、Webhook 與應用程式 |

託管執行環境是工具名稱與結構的來源基準。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Klaviyo 使用者身份執行，且不能超過該使用者許可權。
- 完整目錄很大。Klaviyo 支援 URL 查詢引數來縮減範圍，`?core-tools-only=true` 為核心集合，`?read-only=true` 移除所有寫入工具，`?toolsets=profiles:read,campaigns:read` 選擇特定工具集。若您想要更窄的範圍，可編輯外掛伺服器 URL。
- `?disable-tools-with-user-generated-content=true` 會隱藏返回使用者生成內容的工具，在共享或敏感帳戶上建議設定此項。
- 當您屬於多個帳戶時，新增 `?company=your-company-slug` 可固定特定帳戶。
- URL 不得有尾隨斜線。

<a id="docs"></a>

## 文件

- Klaviyo MCP 伺服器：https://developers.klaviyo.com/en/docs/klaviyo_mcp_server
- MCP 伺服器指南（幫助中心）：https://help.klaviyo.com/hc/en-us/articles/52833598880923
- 伺服器 URL：https://mcp.klaviyo.com/mcp

標誌為 Klaviyo 官方標記，來自 `klaviyo` GitHub 組織。

<a id="license"></a>

## 授權

MIT
