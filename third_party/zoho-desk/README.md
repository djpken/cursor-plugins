# Zoho Desk

Cursor 外掛，透過 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Zoho Desk](https://www.zoho.com/desk/)，該伺服器由 Cursor 託管並負責憑證處理。

與您的客服系統協作：工單、聯絡人、任務和知識庫文章。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Zoho Desk**。
3. 點選 **安裝**，選擇您的 Zoho 資料中心，然後在 Cursor 提示時登入 Zoho。

或在聊天中執行 `/add-plugin zoho-desk`。

## MCP

此外掛不包含 `mcp.json`，這是刻意設計。

此儲存庫中的其他每個聯結器，不是指向供應商的託管端點，也不是透過 stdio 在本機執行套件，因此它帶有自己的伺服器定義。Zoho Desk 則不然：Cursor 執行伺服器，外掛僅作為選入機制，為您的帳戶啟用它。安裝外掛就是讓伺服器可用；沒有需要執行指令、沒有需要連線的端點，也沒有需要貼上的 API 金鑰或客戶端金鑰。

一個值得知道的後果：因為伺服器未在此宣告，您不會在本地 `mcp.json` 中找到它，也不能將其他 MCP 客戶端指向它。它僅從 Cursor 可達。

<a id="before-you-connect"></a>

## 連線前

無需準備。Cursor 擁有 OAuth 客戶端，因此您不需要 Zoho 開發者帳戶或自己的應用。

在登入之前，選擇您的 Zoho 帳戶所在的資料中心。它與您使用的 Zoho 域名相符，例如 `zoho.com` 或 `zoho.eu`。Zoho Desk 在美國、歐洲、印度、澳洲、日本、加拿大、沙烏地阿拉伯、英國、阿拉伯聯合大公國和新加坡可用。

Cursor 在您登入時會找到您的 Desk 組織。如果您屬於多個組織，它會使用 Zoho 標記為您的預設的那一個。

<a id="what-agents-can-do"></a>

## 代理可做的事

| 類別 | 能力 |
| --- | --- |
| 工單 | 讀取、建立、更新和刪除工單 |
| 聯絡人 | 讀取、建立、更新和刪除聯絡人 |
| 任務 | 讀取、建立、更新和刪除任務 |
| 知識庫 | 讀取、建立、更新和刪除文章 |
| 搜尋 | 搜尋 Desk 記錄 |
| 組織 | 讀取基本組織資訊和設定 |

伺服器是工具名稱和結構的來源真相。

<a id="notes"></a>

## 注意事項

- 存取權遵循登入的 Zoho 使用者所能看到的內容。聯結器無法存取該使用者無法存取部門或工單。
- 某些工具會建立、更新和刪除工單、聯絡人、任務和文章。在批准每個工具呼叫之前，請檢視它。
- 斷開連線是兩個獨立步驟：解除安裝外掛會停止 Cursor 提供伺服器，而在您的 Zoho 帳戶設定中撤銷 Cursor 的存取權會結束 Cursor 對您的資料的存取。

<a id="docs"></a>

## 文件

- Zoho Desk: https://www.zoho.com/desk/
- Zoho Desk API: https://desk.zoho.com/DeskAPIDocument
- Model Context Protocol: https://modelcontextprotocol.io

標誌是 Zoho Desk 的官方標記，來自 `zoho.com`。

<a id="license"></a>

## 授權

MIT
