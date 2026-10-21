# Google Docs

Cursor 外掛，透過 Cursor 的遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Google Docs](https://docs.google.com)。

讀取檔案結構與文字、建立檔案、插入與取代文字、刪除內容範圍，並套用段落樣式。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Google Docs**。
3. 點選 **安裝**，然後完成 Google 登入提示。

或在聊天中執行 `/add-plugin google-docs`。

## MCP

```json
{
  "mcpServers": {
    "google-docs": {
      "type": "http",
      "url": "https://api.cursor.com/rest-mcp/google-docs/mcp"
    }
  }
}
```

驗證採用 OAuth 2.0 對 Google 進行。Cursor 會在外掛連線時提示 Google 登入。

<a id="skill"></a>

## 技能

隨附的 `google-docs` 技能會教導代理檔案撰寫慣例：安全編輯的索引契約、字元與段落樣式、列表、表格與圖片。

<a id="docs"></a>

## 檔案

- Google Docs API：https://developers.google.com/workspace/docs/api/reference/rest
- Google Workspace 概覽：https://developers.google.com/workspace

標誌為官方 Google Docs 產品圖示，放置在白色底板上並留有邊距，以便在 Cursor 介面中清晰呈現。

<a id="license"></a>

## 授權

MIT
