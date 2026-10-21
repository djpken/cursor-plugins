# Statsig

Cursor 外掛，透過 Statsig 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Statsig](https://www.statsig.com)。

在已登入的 Statsig 專案中列出並檢視功能閘門、實驗、動態設定和指標，提取閘門和實驗結果，並（若具有寫入許可權）從聊天中建立或更新閘門、實驗和設定。

官方 Cursor 設定：https://docs.statsig.com/integrations/mcp/cursor

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Statsig**。
3. 點選 **安裝**，然後完成 Statsig 登入提示。

或在聊天中執行 `/add-plugin statsig`。

## MCP

```json
{
  "mcpServers": {
    "statsig": {
      "type": "http",
      "url": "https://api.statsig.com/v3/mcp"
    }
  }
}
```

驗證是透過 Statsig 的 OAuth。當外掛連線時，Cursor 會提示 Statsig 使用者登入，不需要設定 API 金鑰或客戶 ID。

<a id="upgrading-from-1x"></a>

## 從 1.x 升級

版本 2.0.0 連線至 MCP v3，這會改變工具名稱和輸入結構。更新外掛後，重新連線 Statsig 伺服器並開始新的聊天，讓代理載入當前工具目錄。如果 Cursor 再次請求授權，請完成 Statsig 登入提示。

此外掛更新不會更改現有手動設定的 MCP 連線。如果您也手動設定了 Statsig，請分別更新該連線，並避免啟用多個 Statsig 連線。硬編碼舊工具名稱的指令或自動化必須更新為 v3 所宣傳的工具。

<a id="before-you-connect"></a>

## 連線前

您需要一個 Statsig 帳戶並能存取專案。Statsig 的 MCP OAuth 會發放一個**個人 Console API 金鑰**，其範圍限定於您的角色，因此您的 Statsig 組織所有者必須已在組織設定中為您的角色啟用個人 Console API 金鑰的建立。如果登入失敗，請要求組織所有者啟用此功能。

<a id="what-agents-can-do"></a>

## 代理可以執行什麼

| 類別 | 功能 |
| --- | --- |
| 閘門（功能旗標） | 列出閘門並使用型別篩選（例如過時或永久），取得完整閘門設定，提取閘門結果，檢視版本歷史，並建立或取代閘門 |
| 實驗 | 依狀態、建立者或標籤列出實驗；取得實驗詳細資訊；提取整體和維度結果，並提供 CUPED 和信心選項；檢視版本歷史；開始程式碼清理；建立或取代實驗 |
| 動態設定 | 列出設定，取得目標規則和回傳值，並建立或取代設定 |
| 指標 | 列出指標和指標來源，並讀取指標定義 |

託管執行環境是工具名稱和結構的來源。MCP v3 將相關動作整合為型別工具。使用連線伺服器所宣傳的工具；如果某功能未顯示，請使用 `discover_tools` 尋找操作及其當前輸入結構。

<a id="notes"></a>

## 注意

- 工具呼叫以授權連線的 Statsig 使用者身份執行，且不能超出該使用者角色的許可權。僅讀取角色可以使用所有讀取工具；寫入工具需要具有寫入許可權的角色。
- 對於取代實體的操作，請先讀取當前實體並保留應保持不變的欄位。確認預期的變更後再寫入，並遵循伺服器回傳的任何審核或確認要求。
- Statsig 也記錄了 API 金鑰設定，該設定會在本地執行 `npx mcp-remote` 並使用 `statsig-api-key` 標頭。此外掛僅打包 OAuth Streamable HTTP 端點，並不包裝本地 stdio 伺服器。
- 隨時透過從 Statsig 專案的 API 金鑰頁面刪除個人 Console API 金鑰來撤銷存取。

<a id="docs"></a>

## 文件

- Statsig MCP 在 Cursor：https://docs.statsig.com/integrations/mcp/cursor
- Statsig MCP 概述和工具列表：https://docs.statsig.com/integrations/mcp/overview
- 個人 Console API 金鑰：https://docs.statsig.com/access-management/api-keys
- 伺服器 URL：https://api.statsig.com/v3/mcp

標誌是 Statsig 的官方標誌，來自 `statsig-io` GitHub 組織。

<a id="license"></a>

## 授權

MIT
