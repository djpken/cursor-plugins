<a id="craft"></a>

# 製作

Cursor 外掛，透過 Craft 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Craft](https://www.craft.do)。

在已連線的 Craft 空間中搜尋、讀取、建立和更新檔案、每日筆記、任務和集合。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Craft**。
3. 點選 **安裝**，然後完成 Craft 登入提示。

或在聊天中執行 `/add-plugin craft`。

## MCP

```json
{
  "mcpServers": {
    "craft": {
      "type": "http",
      "url": "https://mcp.craft.do/my/mcp"
    }
  }
}
```

驗證採用 OAuth。當外掛連線時，Cursor 會提示 Craft 登入，並在授權畫面中選擇要授予存取權的空間。

<a id="before-you-connect"></a>

## 連線前

請先在 Craft 應用中建立 MCP 連線。開啟側邊欄中的 **連線** 區域。在該連線存在之前，端點不會授權。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 搜尋 | 跨檔案搜尋，支援標籤、日期和正規表示式篩選 |
| 檔案 | 建立、讀取、更新和刪除檔案 |
| 每日筆記與任務 | 處理每日筆記和任務專案 |
| 集合 | 管理集合，包括結構編輯 |

託管執行環境是工具名稱和結構的來源。

<a id="notes"></a>

## 注意事項

- 工具呼叫範圍限定於授權期間選定的 Craft 空間。
- 端點 URL 對所有人相同：`https://mcp.craft.do/my/mcp`。每個空間的範圍設定是在 OAuth 批准期間進行，而非在 URL 中。
- 因為一個連線繫結一個空間，跨多個空間工作意味著每個空間需新增一個伺服器專案。
- Packagist 上的 `stimmt/craft-mcp` 是一個不相關的 Craft **CMS** 外掛。

<a id="docs"></a>

## 檔案

- Craft MCP 指南：https://www.craft.do/imagine/guide/mcp
- 連線 Craft 到 Cursor：https://www.craft.do/imagine/guide/mcp/cursor
- MCP 支援文章：https://support.craft.do/en/integrate/mcp
- 伺服器 URL：https://mcp.craft.do/my/mcp

標誌是 Craft 官方標記，來自 `craftdocs` GitHub 組織。

<a id="license"></a>

## 授權

MIT
