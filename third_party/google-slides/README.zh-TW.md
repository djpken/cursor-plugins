# Google Slides

Cursor 外掛，透過 Cursor 的遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Google Slides](https://slides.google.com)。

讀取簡報結構和文字、建立簡報、新增和重新排序投影片、插入文字框、圖形、圖片和表格、設定文字和背景樣式，以及將任何投影片渲染為 PNG 縮圖。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Google Slides**。
3. 點選 **安裝**，然後完成 Google 登入提示。

或在聊天中執行 `/add-plugin google-slides`。

## MCP

```json
{
  "mcpServers": {
    "google-slides": {
      "type": "http",
      "url": "https://api.cursor.com/rest-mcp/google-slides/mcp"
    }
  }
}
```

驗證是針對 Google 的 OAuth 2.0。當外掛連線時，Cursor 會提示 Google 登入。

<a id="skill"></a>

## 技能

隨附的 `google-slides` 技能會教導代理簡報製作慣例：物件手柄和點座標、避免裁剪圖片的版面、色板和文字樣式，以及每個投影片的 PNG 驗證。

<a id="docs"></a>

## 文件

- Google Slides API：https://developers.google.com/workspace/slides/api/reference/rest
- Google Workspace 概覽：https://developers.google.com/workspace

標誌是官方 Google Slides 產品圖示，放置在白色底板上並留有邊距，以便在 Cursor 介面中清晰呈現。

<a id="license"></a>

## 授權

MIT
