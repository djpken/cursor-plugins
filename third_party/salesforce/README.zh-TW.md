# Salesforce

Cursor 外掛，透過 [Salesforce Hosted MCP](https://developer.salesforce.com/docs/platform/hosted-mcp-servers/) 將代理連線到 [Salesforce](https://www.salesforce.com)，這是 Salesforce 的第一方 [Model Context Protocol](https://modelcontextprotocol.io/) 服務。

執行 SOQL 和 SOSL、檢查物件結構、瀏覽關係，以及建立、更新或刪除記錄，全部都在登入使用者自身的許可權和欄位層級安全性之下。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor Settings → Plugins**。
2. 搜尋 **Salesforce**。
3. 點選 **Install**，然後設定伺服器 URL 和消費者金鑰（如下），並完成 Salesforce 登入提示。

或在聊天中執行 `/add-plugin salesforce`。

## MCP

```json
{
  "mcpServers": {
    "salesforce": {
      "type": "http",
      "url": "${SALESFORCE_MCP_URL}",
      "auth": {
        "CLIENT_ID": "${CLIENT_ID}",
        "scopes": ["mcp_api", "refresh_token"]
      }
    }
  }
}
```

<a id="setup"></a>

## 設定

Salesforce Hosted MCP 需要在您的組織中建立一個 **External Client App**。Connected Apps 不受支援。

<a id="1-create-the-external-client-app"></a>

### 1. 建立 External Client App

從 Setup 前往 **External Client App Manager → New External Client App**，填寫基本資訊，然後展開 **API (Enable OAuth Settings)** 並勾選 **Enable OAuth**。

加入所有需要的 callback URL，Cursor 在不同介面使用不同的 URL：

| 介面 | Callback URL |
|:--------|:-------------|
| Desktop | `http://localhost:8787/callback` |
| Web 和 Cloud Agents | `https://www.cursor.com/agents/mcp/oauth/callback` |
| 舊版桌面版本 | `cursor://anysphere.cursor-mcp/oauth/callback` |

在 **OAuth Scopes** 下，僅選取這兩個，不要選更廣泛的範圍：

- **Access Salesforce hosted MCP servers** (`mcp_api`)
- **Perform requests at any time** (`refresh_token`, `offline_access`)

第二個很容易漏掉，因為選擇器以描述而非值來標示範圍。沒有它，外掛無法重新整理，每個使用者都必須在存取令牌到期時重新驗證。不要加入 **Full access** (`full`)，Hosted MCP 不需要它。

在 **Security** 下，選取 **Issue JSON Web Token (JWT)-based access tokens for named users**。這是必要的：沒有它，Salesforce 會發出不透明的令牌，每個工具呼叫都會失敗並出現 `JWT Token is required`。將 **Require Secret for Web Server Flow** 保持關閉，Cursor 以公開客戶端使用 PKCE 驗證，因此不涉及客戶端密碼。不要啟用 **JWT Bearer Flow**，這是不同的功能且需要憑證。

最後，從 **Settings → Consumer Key and Secret** 複製 **Consumer Key**。

新的 External Client App 可能需要最多 30 分鐘才能傳播。在此之前，驗證會失敗並出現 `invalid_client_id`；請等待，而不是重新建立應用程式。

<a id="2-activate-a-server-and-copy-its-url"></a>

### 2. 啟用伺服器並複製其 URL

在 Setup 中，開啟 **MCP Servers**，啟用您想要的伺服器，並複製其 **Server URL**。URL 同時包含組織型別與伺服器：

| 組織型別 | 標準伺服器 | 自訂伺服器 |
|:---------|:----------------|:--------------|
| Production、Developer、Enterprise | `https://api.salesforce.com/platform/mcp/v1/platform/sobject-all` | `https://api.salesforce.com/platform/mcp/v1/custom/myserver` |
| Sandbox 或 scratch | `https://api.salesforce.com/platform/mcp/v1/sandbox/platform/sobject-all` | `https://api.salesforce.com/platform/mcp/v1/sandbox/custom/myserver` |

Salesforce 提供幾個標準伺服器，影響範圍不同：`sobject-reads` 僅限讀取，`sobject-mutations` 支援讀取、建立和更新，`sobject-deletes`，以及 `sobject-all` 支援所有操作。請將外掛指向能完成任務的最小範圍伺服器。

<a id="3-configure-the-plugin"></a>

### 3. 設定外掛

在 **Dashboard → Plugins → Configure** 中，設定 **Salesforce MCP server URL** 和 **Salesforce Consumer Key**，然後在 Cursor 提示時完成 Salesforce 登入。

在團隊 marketplace 中，管理者設定兩個值一次。每個成員仍會各自驗證，因此工具會以該成員自身的物件和欄位許可權執行。

<a id="troubleshooting"></a>

## 疑難排解

| 症狀 | 原因 |
|:--------|:------|
| `invalid_client_id` | External Client App 尚未完成傳播。請等待最多 30 分鐘。 |
| `invalid_scope` | 應用程式缺少 **Access Salesforce hosted MCP servers** 或 **Perform requests at any time**。 |
| 登入成功後出現 `JWT Token is required` 或 `Invalid token` | **Issue JSON Web Token (JWT)-based access tokens for named users** 未啟用。 |
| 驗證成功但伺服器回應 404 | MCP 伺服器未在 Setup 中啟用，或 URL 的組織型別與您登入的組織不符。 |

<a id="docs"></a>

## 檔案

- 設定 Cursor：https://developer.salesforce.com/docs/platform/hosted-mcp-servers/guide/cursor.html
- 建立 External Client App：https://developer.salesforce.com/docs/platform/hosted-mcp-servers/guide/create-external-client-app.html
- 可用伺服器：https://developer.salesforce.com/docs/platform/hosted-mcp-servers/references/reference/sobject-all.html

<a id="license"></a>

## 授權

MIT
