# Xero

Cursor 外掛，透過 Xero 官方 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，在 Cursor 本地執行，將代理連線到 [Xero](https://www.xero.com)。

讀取和寫入 Xero 組織的會計和薪資資料：發票、聯絡人、科目表、付款、報價、日記帳、報告和時間表。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Xero**。
3. 點選 **安裝**，然後設定 Xero 客戶 ID 和客戶金鑰（如下）。

或在聊天中執行 `/add-plugin xero`。

## MCP

```json
{
  "mcpServers": {
    "xero": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "-y",
        "@xeroapi/xero-mcp-server@latest"
      ],
      "env": {
        "XERO_CLIENT_ID": "${XERO_CLIENT_ID}",
        "XERO_CLIENT_SECRET": "${XERO_CLIENT_SECRET}"
      }
    }
  }
}
```

Xero 未發布託管的 MCP 端點。其官方伺服器透過 stdio 在本地執行，並使用 **Custom Connection** 進行驗證，這是 Xero 的機器對機器 OAuth 2.0 流程，因此外掛接受客戶 ID 和金鑰，而非提示瀏覽器登入。

<a id="before-you-connect"></a>

## 連線前

1. 在 [developer.xero.com](https://developer.xero.com) 登入並建立一個應用，選擇 **Custom Connection** 選項。
2. 事先選擇許可權範圍。2026-04-29 之前建立的連線使用預設的許可權範圍列表；較新的連線使用細粒度列表。伺服器先嘗試預設集合，然後回退，因此通常不需要設定 `XERO_SCOPES`。
3. 從 Xero 傳送的電子郵件中授權連線，並選擇要連線的組織。
4. 複製 **Client ID**，生成 **Client Secret**，並在 **Dashboard → Plugins → Configure** 中設定這兩項。

Custom Connection 繫結到單個 Xero 組織，且每個組織是付費附加功能。薪資工具需要 NZ 或 UK 組織。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 帳戶與聯絡人 | 科目表、聯絡人和聯絡人群組 |
| 銷售與採購 | 發票、報價、信用通知和付款 |
| 銀行 | 銀行交易和手動日記帳 |
| 專案與追蹤 | 專案和追蹤類別 |
| 報告 | 損益表、資產負債表、試算表和逾期應收/應付帳款 |
| 薪資 | 員工、休假、休假型別和時間表（NZ 和 UK 組織） |

伺服器是工具名稱和結構的來源真相。

<a id="notes"></a>

## 注意事項

- 這是本地 stdio 伺服器，因此 `npx` 必須在執行 Cursor 的機器上可用。首次執行時會下載 `@xeroapi/xero-mcp-server`。
- Xero 官方 FAQ 表示伺服器適用於任何支援本地 stdio 伺服器的客戶，且其測試是在 Claude Desktop 和 Cursor 上進行。
- 工具呼叫使用授予 Custom Connection 的許可權範圍，針對它所繫結的單個組織執行。若要處理多個組織，請為每個組織建立一個連線。
- 若要進一步縮小範圍，將空格分隔的 `XERO_SCOPES` 值新增到伺服器的 `env`，例如 `accounting.invoices accounting.contacts accounting.settings`。
- john-zhang-dev 的 `xero-mcp` 是社群包，JAX 是 Xero 的產品內助手。這兩者都不是此伺服器。

<a id="docs"></a>

## 文件

- Xero MCP 伺服器：https://github.com/XeroAPI/xero-mcp-server
- Xero AI 和 MCP：https://developer.xero.com/ai
- Custom Connections：https://developer.xero.com/documentation/guides/oauth2/custom-connections/
- npm 包：https://www.npmjs.com/package/@xeroapi/xero-mcp-server

Logo 是 Xero 官方標誌，來自 `XeroAPI` GitHub 組織。

<a id="license"></a>

## 許可

MIT
