<a id="salesforce-headless-360-beta"></a>

# Salesforce（Headless 360，Beta）

Cursor 外掛，將代理連線到 Salesforce 的 [Headless 360 MCP 伺服器](https://developer.salesforce.com/docs/platform/hosted-mcp-servers/references/reference/headless-360-mcp.html)（Beta），屬於 [Salesforce Hosted MCP](https://developer.salesforce.com/docs/platform/hosted-mcp-servers/) 的一部分。

Headless 360 透過四個工具讓代理在 Salesforce 中取得存取權，這些工具由日益擴充的 Salesforce 操作庫支援。代理可以查詢、建立和更新記錄；管理使用者、許可權集和許可權集授權；處理 Apex 觸發器、平臺事件和變更資料擷取；設定具名憑證；以及管理 Commerce Cloud 訂單。每次呼叫都以登入使用者執行，並遵循該使用者物件許可權、欄位層級安全性及共享規則。

此外掛與 [`salesforce`](../salesforce/) 外掛不同，該外掛連線至 SObject 和自訂 Hosted MCP 伺服器。兩者可以同時安裝。

<a id="who-does-what"></a>

## 各角色的職責

| 角色 | 職責 |
|:-----|:-------------|
| **Salesforce 管理員** | 建立外部客戶應用程式、啟用 Headless 360，並在團隊的外掛設定中輸入一次 **伺服器 URL**、**消費者金鑰** 和 **消費者金鑰**。 |
| **團隊中的其他成員** | 安裝外掛並登入 Salesforce。成員不需要消費者金鑰或金鑰，且無法從自己的 Salesforce 帳戶取得這些資訊。 |

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Salesforce（Headless 360，Beta）**。
3. 點選 **安裝**。如果您的管理員已為團隊設定此外掛，請完成 Salesforce 登入提示。如果系統要求您輸入伺服器 URL、消費者金鑰和消費者金鑰，請先請您的 Salesforce 管理員為團隊設定外掛。

<a id="tools"></a>

## 工具

| 工具 | 功能 |
|:-----|:-------------|
| `discover` | 尋找符合請求的 Salesforce 操作。 |
| `describe` | 返回操作的引數、相依專案和步驟。 |
| `dispatch` | 執行操作。它可以更改資料和組織設定。 |
| `dispatch_readonly` | 執行僅讀操作。它絕不會更改資料或設定。 |

## MCP

```json
{
  "mcpServers": {
    "salesforce-headless-360": {
      "type": "http",
      "url": "${SALESFORCE_MCP_URL}",
      "auth": {
        "CLIENT_ID": "${CLIENT_ID}",
        "CLIENT_SECRET": "${CLIENT_SECRET}",
        "scopes": ["mcp_api", "refresh_token"]
      }
    }
  }
}
```

<a id="admin-setup"></a>

## 管理員設定

<a id="1-activate-headless-360"></a>

### 1. 啟用 Headless 360

Headless 360 需要 API 版本 67.0 或更新版本。從 Setup 中，在 Quick Find 輸入 **MCP Servers**，選取 **MCP Servers**，找到 **headless-360**，並點選 **Activate**。

<a id="2-create-the-external-client-app"></a>

### 2. 建立外部客戶應用程式

從 Setup 中，前往 **External Client App Manager → New External Client App**，填寫基本資訊，然後展開 **API（Enable OAuth Settings）** 並勾選 **Enable OAuth**。不支援 Connected Apps。

新增 Cursor 和 Grok Bot 使用的每個回呼 URL：

| 介面 | 回呼 URL |
|:--------|:-------------|
| Cursor 桌面版 | `http://localhost:8787/callback` |
| Cursor 桌面版（IPv4 迴路） | `http://127.0.0.1:8787/callback` |
| Web 和 Cloud Agents | `https://www.cursor.com/agents/mcp/oauth/callback` |
| 舊版 Cursor 桌面版 | `cursor://anysphere.cursor-mcp/oauth/callback` |
| Grok Bot | `grokbot://mcp/oauth/callback` |
| Grok Bot（瀏覽器交接） | `https://www.cursor.com/bot/mcp/oauth/callback` |

在 **OAuth Scopes** 下，僅選取以下兩個，不要選取更廣泛的範圍：

- **Access Salesforce hosted MCP servers**（`mcp_api`）
- **Perform requests at any time**（`refresh_token`、`offline_access`）

第二個很容易忽略，因為選取器以描述而非值標示範圍。若未選取，成員每次存取令牌到期時都必須重新登入。不要新增 **Full access**（`full`）。

在 **Security** 下：

- 選取 **Issue JSON Web Token (JWT)-based access tokens for named users**。若不選取，Salesforce 會發出具名令牌，且每個工具呼叫都會失敗並顯示 `JWT Token is required`。
- 保持 **Require Proof Key for Code Exchange (PKCE)** 和 **Require Secret for Web Server Flow** 開啟。
- 不要啟用 **JWT Bearer Flow**，這是不同的功能且需要憑證。

從 **Settings → Consumer Key and Secret** 複製 **Consumer Key** 和 **Consumer Secret**。

新的外部客戶應用程式可能需要最多 30 分鐘才能傳播。在此之前，登入會失敗並顯示 `invalid_client_id`；請等待而非重新建立應用程式。

<a id="3-configure-the-plugin-for-your-team"></a>

### 3. 為您的團隊設定外掛

在 **Dashboard → Plugins → Configure** 中，在您的團隊 marketplace 設定以下專案：

| 設定 | 值 |
|:--------|:------|
| **Salesforce MCP server URL** | `https://api.salesforce.com/platform/mcp/v1/platform/headless-360` 適用於 production 和 Developer org，或 `https://api.salesforce.com/platform/mcp/v1/sandbox/platform/headless-360` 適用於 sandbox 和 scratch org。 |
| **Salesforce Consumer Key** | 步驟 2 中的 Consumer Key。 |
| **Salesforce Consumer Secret** | 步驟 2 中的 Consumer Secret。 |

成員隨後可略過設定並直接進行 Salesforce 登入。金鑰在設定 UI 中會被遮蔽。Cursor 仍會將它傳送到每個成員的客戶端，因為桌面客戶端自行執行登入。

<a id="approvals-for-changes"></a>

## 變更的核準

`dispatch` 可以更改組織設定或資料，例如建立或停用使用者、指派許可權集或部署 Apex。請設定您的客戶端，在執行 `dispatch` 之前要求核准，並讓 `dispatch_readonly` 無需核准即可執行。在 production 環境之前，先在 sandbox 或 Developer org 中嘗試設定變更。

<a id="troubleshooting"></a>

## 疑難排解

| 症狀 | 原因 |
|:--------|:------|
| 成員被要求輸入伺服器 URL、消費者金鑰和消費者金鑰 | 您的 Salesforce 管理員尚未為團隊設定外掛。 |
| `invalid_client_id` | 外部客戶應用程式尚未完成傳播。請等待最多 30 分鐘。 |
| `invalid_client` 或 `invalid client credentials` | Consumer Secret 與 Consumer Key 不符。請從同一應用程式再次複製兩者。 |
| `redirect_uri_mismatch` | 應用程式缺少您登入介面的回呼 URL。請新增上述所有回呼 URL。 |
| `invalid_scope` | 應用程式缺少 **Access Salesforce hosted MCP servers** 或 **Perform requests at any time**。 |
| 登入成功後出現 `JWT Token is required` 或 `Invalid token` | **Issue JSON Web Token (JWT)-based access tokens for named users** 未啟用。 |
| 登入成功但伺服器返回 404 | Headless 360 未在 Setup 中啟用，或 URL 的 org 型別（production 或 sandbox）與您登入的 org 不符。 |

<a id="docs"></a>

## 檔案

- Headless 360 MCP 伺服器：https://developer.salesforce.com/docs/platform/hosted-mcp-servers/references/reference/headless-360-mcp.html
- 建立外部客戶應用程式：https://developer.salesforce.com/docs/platform/hosted-mcp-servers/guide/create-external-client-app.html
- 設定 Cursor：https://developer.salesforce.com/docs/platform/hosted-mcp-servers/guide/cursor.html

<a id="license"></a>

## 授權

MIT
