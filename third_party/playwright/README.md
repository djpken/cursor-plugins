# Playwright

Cursor 外掛，透過 Microsoft 的 [Playwright MCP](https://github.com/microsoft/playwright-mcp) 伺服器將代理連線到真實瀏覽器。

從聊天中瀏覽頁面、點選和填寫元素、取得無障礙快照和螢幕截圖，並執行端到端檢查。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Playwright**。
3. 點選 **安裝**。

或在聊天中執行 `/add-plugin playwright`。

<a id="requirements"></a>

## 需求

這是 **本地 stdio** MCP 伺服器。Cursor 使用 `npx -y @playwright/mcp@latest` 啟動它，因此執行 Cursor 的機器需要安裝 **Node.js** 並在 `PATH` 上。`-y` 旗標會跳過 `npx` 的安裝確認，因此首次啟動不會因等待 stdio 上的互動輸入而掛起。

首次執行會下載 Playwright 的瀏覽器二進位制檔案。這可能需要一分鐘並需要網路存取；後續執行會重用已快取的瀏覽器。

## MCP

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp@latest"]
    }
  }
}
```

不需要憑證。伺服器在本地執行並驅動同一臺機器上的瀏覽器。

<a id="docs"></a>

## 文件

- Playwright MCP: https://github.com/microsoft/playwright-mcp
- Playwright: https://playwright.dev

標誌是 Microsoft 官方 Playwright 標誌，取自 Playwright 網站，放置在白色背景上並留有邊距，以便在 Cursor UI 中清晰呈現：
https://github.com/microsoft/playwright.dev/blob/main/static/img/playwright-logo.svg

<a id="license"></a>

## 許可

MIT
