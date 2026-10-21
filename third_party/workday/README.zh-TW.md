# Workday

Cursor 外掛，透過 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器（由 Cursor 託管）將代理連線到 [Workday](https://www.workday.com)，Workday 是雲端人力資源、薪資與財務平臺。

讀取租戶的人員資料：員工及其個人資料、監督組織、休假與請假、薪資群組與薪資單、職位發布與候選人，以及透過 Workday Query Language (WQL) 的自訂報告。

<a id="install"></a>

## 安裝

1. 團隊管理員開啟 **Cursor Settings → Plugins**，搜尋 **Workday**，並為團隊安裝。
2. 管理員輸入團隊的 Workday API 客戶端（請參閱 [Team setup](#team-setup)）。
3. 每位成員在 Cursor 提示時登入 Workday。

或在聊天中執行 `/add-plugin workday`。

## MCP

此外掛不提供 `mcp.json`，這是刻意設計。

此儲存庫中的每個其他聯結器不是指向供應商託管的端點，也不是透過 stdio 在本地執行套件，因此它自帶伺服器定義。Workday 則不同：Cursor 執行伺服器，外掛僅作為選擇性啟用，為你的團隊開啟此功能。沒有需要執行的指令，也沒有需要設定的 MCP 端點。

一個值得知道的後果：因為伺服器未在此宣告，你不會在本地 `mcp.json` 中找到它，也不能將其他 MCP 客戶端指向它。僅能從 Cursor 存取。

<a id="team-setup"></a>

## 團隊設定

Workday API 客戶端是在每個客戶的租戶內註冊，因此 Cursor 無法提供。團隊管理員提供團隊自己的客戶端一次，每位成員透過它登入。

<a id="1-register-an-api-client-in-workday"></a>

### 1. 在 Workday 註冊 API 客戶端

Workday 管理員在租戶中註冊 API 客戶端（**Register API Client**），並設定：

- **Client grant type:** authorization code grant。
- **Redirection URI:** `https://www.cursor.com/agents/mcp/oauth/callback`。
- **Scopes:** 您希望代理讀取的功能區域，例如人力設定、組織、休假、薪資與招募。

儲存後，**View API Clients** 會顯示客戶端 ID、客戶端密碼與租戶的端點 URL。

<a id="2-enter-the-client-in-cursor"></a>

### 2. 在 Cursor 輸入客戶端

只有團隊管理員可以儲存這些資訊。所有六個欄位皆為必填：

| 欄位 | 值 |
| --- | --- |
| `CLIENT_ID` | API 客戶端 ID。 |
| `CLIENT_SECRET` | API 客戶端密碼。加密儲存，不會在外掛列表中顯示。 |
| `TENANT` | 租戶名稱，例如 `acme_dpt1`。僅限字母、數字、`_` 與 `-`。 |
| `AUTHORIZATION_BASE_URL` | 從 View API Clients 的 **Authorization Endpoint**（以 `/authorize` 結尾，位於租戶的網頁主機），或該網頁主機的來源。若提供來源，Cursor 使用 `<origin>/<TENANT>/authorize`。 |
| `TOKEN_ENDPOINT` | 從 View API Clients 的 **Token Endpoint**，例如 `https://wd2-impl-services1.workday.com/ccx/oauth2/acme_dpt1/token`。它位於服務主機而非網頁主機，因此 Cursor 不會自動推導。 |
| `REST_API_BASE_URL` | 從 View API Clients 的 **Workday REST API Endpoint**，例如 `https://wd2-impl-services1.workday.com/ccx/api/v1/acme_dpt1`。 |

每個 URL 必須使用 `https` 並位於 Workday 域名（`workday.com`、`myworkday.com`、`workdaygov.com` 或 `myworkdaygov.com`，包括子域名）。Cursor 拒絕其他主機，因為客戶端密碼與成員的授權會傳送至這些主機。

<a id="3-members-sign-in"></a>

### 3. 成員登入

每位成員使用自己的帳戶登入 Workday。連線會保持與發出客戶端之團隊的繫結，因此在 Cursor 中切換團隊不會將成員的 Workday 會話移至其他團隊的租戶。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 員工 | 員工與僱員個人資料、薪酬與福利 |
| 組織 | 組織、監督組織及其成員 |
| 休假 | 缺勤餘額、休假記錄與請假 |
| 時間追蹤 | 打卡事件與員工時間總計 |
| 薪資 | 薪資群組、薪資組成、薪資單與薪資輸入 |
| 招募 | 職位發布、潛在候選人與面試 |
| 報告 | WQL 資料來源與查詢，以及自訂物件定義 |

伺服器是工具名稱與結構的來源。

<a id="notes"></a>

## 注意事項

- 存取範圍遵循登入的 Workday 使用者所能看到的資料，並與 API 客戶端 scopes 交集。Workday 的安全群組仍適用，因此聯結器無法存取使用者無權檢視的員工、薪酬或薪資資料。
- Workday 資料通常敏感。僅授予 API 客戶端您希望代理讀取的 scopes。
- 若登入失敗並顯示「credentials are not configured for this team」，表示欄位缺失、URL 不是 `https` Workday URL，或租戶名稱包含不支援的字元。
- 斷開連線在兩端獨立：解除安裝外掛會停止 Cursor 提供伺服器，而停用 API 客戶端或在 Workday 中撤銷其令牌會結束 Cursor 對您的資料的存取。

<a id="docs"></a>

## 文件

- Workday: https://www.workday.com
- Workday Query Language: https://doc.workday.com
- Model Context Protocol: https://modelcontextprotocol.io

Logo 是 Workday 的官方標誌，來自 `Workday` GitHub 組織。

<a id="license"></a>

## 授權

MIT
