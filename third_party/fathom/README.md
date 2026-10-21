# Fathom

Cursor 外掛，透過 Fathom 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Fathom](https://fathom.video)。

將 Fathom 會議錄製、轉錄稿和 AI 摘要帶入編輯器，以便起草後續事項並提取決策。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor Settings → Plugins**。
2. 搜尋 **Fathom**。
3. 點選 **Install**，然後完成 Fathom 登入提示。

或在聊天中執行 `/add-plugin fathom`。

## MCP

```json
{
  "mcpServers": {
    "fathom": {
      "type": "http",
      "url": "https://api.fathom.ai/mcp"
    }
  }
}
```

驗證方式為 OAuth。當外掛連線時，Cursor 會提示 Fathom 登入。

<a id="before-you-connect"></a>

## 連線前

API 存取在所有 Fathom 方案層級均可用，包括免費方案。CRM 匹配的聯絡人和交易資料需要 Business 方案。在團隊或組織環境中，所有者可能需要先啟用聯結器，成員才能使用。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 會議 | 列出和搜尋會議，並依會議型別篩選 |
| 錄製 | 查詢錄製及其 metadata |
| 轉錄稿與摘要 | 提取完整轉錄稿和 AI 生成的摘要 |
| 團隊 | 團隊和會議型別設定 |

託管 runtime 是工具名稱和 schema 的來源。

<a id="notes"></a>

## 注意事項

- 工具呼叫以授權連線的 Fathom 使用者身份執行。
- Fathom 的幫助中心描述每個工具的開關，因此管理者可以縮小聯結器暴露的範圍。
- 會議記錄工具 Fathom 以 fathom.video 作為品牌，但其 API 和 MCP 服務來自 `fathom.ai`。GitHub 組織 `Fathom-AI` 和 analytics 產品 `usefathom` 是不同的公司。
- npm 上的社群 `fathom-mcp` 套件採用 `FATHOM_API_KEY`，且非官方。此外掛使用 Fathom 自己的託管伺服器。

<a id="docs"></a>

## 文件

- Fathom MCP 文件：https://developers.fathom.ai/mcp-docs
- 伺服器 URL：https://api.fathom.ai/mcp

Logo 是 Fathom 的官方標誌，來自 `fathom-video` GitHub 組織。

<a id="license"></a>

## 授權

MIT
