# Zoho Books

Cursor 外掛，透過 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Zoho Books](https://www.zoho.com/books/)，該伺服器由 Cursor 託管並負責憑證處理。

處理您的會計資料：聯絡人、發票、估價單和專案。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Zoho Books**。
3. 點選 **安裝**，選擇您的 Zoho 資料中心，然後在 Cursor 提示時登入 Zoho。

或在聊天中執行 `/add-plugin zoho-books`。

## MCP

此外掛不包含任何 `mcp.json`，這是刻意設計。

此儲存庫中的每個其他聯結器，不是指向供應商的託管端點，也不是透過 stdio 在本地執行套件，因此它自帶伺服器定義。Zoho Books 則不同：Cursor 執行伺服器，外掛僅作為選項，為您的帳戶啟用該伺服器。安裝外掛即可讓伺服器可用；沒有需要執行的命令、沒有需要連線的端點，也沒有需要貼上的 API 金鑰或客戶金鑰。

一個值得知道的後果：因為伺服器未在此處宣告，您不會在本地 `mcp.json` 中找到它，也無法將其他 MCP 客戶端指向它。它僅可從 Cursor 存取。

<a id="before-you-connect"></a>

## 連線前

無需準備任何事項。Cursor 擁有 OAuth 客戶端，因此您不需要 Zoho 開發者帳戶或自己的應用程式。

在登入之前，選擇您的 Zoho 帳戶所在的資料中心。它與您使用的 Zoho 域名相符，例如 `zoho.com` 或 `zoho.eu`。Zoho Books 在美國、歐洲、印度、澳洲、日本、沙烏地阿拉伯和阿拉伯聯合大公國均可用。

每個 Zoho Books 呼叫都針對一個組織執行。Cursor 在您登入時會找到它。如果您屬於多個組織，它會使用 Zoho 標記為您的預設組織。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 聯絡人 | 讀取、建立、更新和刪除客戶與供應商 |
| 發票 | 讀取、建立、更新和刪除發票 |
| 估價單 | 讀取、建立、更新和刪除估價單 |
| 專案 | 讀取、建立、更新和刪除專案 |
| 組織 | 讀取組織詳細資訊 |

伺服器是工具名稱和結構的來源真相。

<a id="notes"></a>

## 注意事項

- 存取範圍遵循登入的 Zoho 使用者在組織中可見的資料。聯結器無法存取使用者無權檢視的資料。
- 某些工具會建立、更新和刪除會計記錄。在批准每個工具呼叫之前，請檢視它。
- 斷開連線是兩個獨立步驟：解除安裝外掛會停止 Cursor 提供伺服器，而在您的 Zoho 帳戶設定中撤銷 Cursor 的存取權，會結束 Cursor 對您的資料的存取。

<a id="docs"></a>

## 文件

- Zoho Books: https://www.zoho.com/books/
- Zoho Books API: https://www.zoho.com/books/api/v3/
- Model Context Protocol: https://modelcontextprotocol.io

標誌是 Zoho Books 的官方標誌，來自 `zoho.com`。

<a id="license"></a>

## 授權

MIT
