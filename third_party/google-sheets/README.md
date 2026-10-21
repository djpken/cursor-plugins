# Google Sheets

Cursor 外掛，透過 Cursor 的遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Google Sheets](https://sheets.google.com)。

讀取試算表元資料與單元格範圍、建立試算表、寫入與附加值、清除範圍，以及管理工作表分頁。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Google Sheets**。
3. 點選 **安裝**，然後完成 Google 登入提示。

或在聊天中執行 `/add-plugin google-sheets`。

## MCP

```json
{
  "mcpServers": {
    "google-sheets": {
      "type": "http",
      "url": "https://api.cursor.com/rest-mcp/google-sheets/mcp"
    }
  }
}
```

驗證是針對 Google 的 OAuth 2.0。當外掛連線時，Cursor 會提示 Google 登入。

<a id="skill"></a>

## 技能

隨附的 `google-sheets` 技能會教導代理試算表慣例：何時使用 A1 與網格座標、儀錶板格式、圖表與驗證。

<a id="docs"></a>

## 文件

- Google Sheets API：https://developers.google.com/workspace/sheets/api/reference/rest
- Google Workspace 概覽：https://developers.google.com/workspace

標誌是官方 Google Sheets 產品圖示，放置在帶有留白的白色圖塊上，以便在 Cursor 介面中清晰呈現。

<a id="license"></a>

## 授權

MIT
