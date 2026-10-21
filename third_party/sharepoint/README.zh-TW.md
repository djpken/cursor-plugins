# SharePoint

Cursor 外掛，透過 Cursor 的遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Microsoft SharePoint](https://www.microsoft.com/microsoft-365/sharepoint/collaboration)。

在已登入的 Microsoft 帳戶中搜尋網站、瀏覽網站的檔案庫與資料夾、搜尋與讀取檔案，以及讀取 SharePoint 清單與清單專案。

<a id="sharepoint-vs-onedrive"></a>

## SharePoint 與 OneDrive 的比較

[OneDrive 外掛](../onedrive/) 可存取已登入使用者**個人磁碟機**。此外掛可存取使用者可訪問的**網站**：小組與通訊網站、其檔案庫及其清單。若您希望同時涵蓋個人檔案與小組網站，請安裝兩者。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **SharePoint**。
3. 點選 **安裝**，然後完成 Microsoft 登入提示。

或在聊天中執行 `/add-plugin sharepoint`。

## MCP

```json
{
  "mcpServers": {
    "sharepoint": {
      "type": "http",
      "url": "https://api.cursor.com/rest-mcp/sharepoint/mcp"
    }
  }
}
```

驗證方式為對 Microsoft（Entra ID）進行 OAuth 2.0。當外掛連線時，Cursor 會提示 Microsoft 登入。

此外掛僅具讀取功能。它請求委派的 `Sites.Read.All` 範圍，Microsoft 將其分類為高影響範圍，因此大多數工作或學校租戶需要管理員先同意一次，成員才能連線。

<a id="tools"></a>

## 工具

| 區域 | 工具 |
|:-----|:------|
| 網站 | `search_sites`, `get_site` |
| 檔案庫 | `list_site_drives`, `list_site_drive_items`, `search_site_drive_items`, `get_site_drive_item` |
| 清單 | `list_site_lists`, `get_list`, `list_list_items`, `get_list_item` |

<a id="docs"></a>

## 檔案

- SharePoint 網站 API（Microsoft Graph）：https://learn.microsoft.com/en-us/graph/api/resources/sharepoint
- Microsoft Graph 概覽：https://learn.microsoft.com/en-us/graph/overview

標誌為 Microsoft 官方 SharePoint 產品圖示，取自 Microsoft Fluent 品牌圖示 CDN，放置於白色底板上並留有邊距，以便在 Cursor 介面中清晰呈現：
https://res-1.cdn.office.net/files/fabric-cdn-prod_20240411.001/assets/brand-icons/product/svg/sharepoint_48x1.svg

<a id="license"></a>

## 授權

MIT
