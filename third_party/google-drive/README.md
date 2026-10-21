# Google Drive

Cursor 外掛，透過 Cursor 的遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Google Drive](https://drive.google.com)。

搜尋 Drive、讀取檔案元資料與內容、建立或更新檔案，以及管理分享。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Google Drive**。
3. 點選 **安裝**，然後完成 Google 登入提示。

或在聊天中執行 `/add-plugin google-drive`。

## MCP

```json
{
  "mcpServers": {
    "google-drive": {
      "type": "http",
      "url": "https://api.cursor.com/rest-mcp/google-drive/mcp"
    }
  }
}
```

驗證是針對 Google 的 OAuth 2.0。當外掛連線時，Cursor 會提示 Google 登入。

<a id="docs"></a>

## 檔案

- Google Drive API：https://developers.google.com/workspace/drive/api/reference/rest/v3
- Google Drive 概覽：https://developers.google.com/workspace/drive

標誌是官方 Google Drive 產品圖示，放置在白色圖塊上並留有邊距，以便在 Cursor 介面中清晰呈現：
https://www.gstatic.com/images/branding/productlogos/drive_2026/v1/192px.svg

<a id="license"></a>

## 授權

MIT
