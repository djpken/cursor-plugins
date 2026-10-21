# GitHub

Cursor 外掛，透過 GitHub 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [GitHub](https://github.com)。

在您提供的個人存取令牌的許可權下，處理儲存庫、問題、Pull Requests、程式碼搜尋和 Actions。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor Settings → Plugins**。
2. 搜尋 **GitHub**。
3. 點選 **Install**，然後設定您的 GitHub 個人存取令牌（如下）。

或在聊天中執行 `/add-plugin github`。

## MCP

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/",
      "headers": {
        "Authorization": "Bearer ${GITHUB_PERSONAL_ACCESS_TOKEN}"
      }
    }
  }
}
```

<a id="setup"></a>

## 設定

GitHub 的遠端 MCP 伺服器使用 **個人存取令牌（PAT）** 進行驗證。建立一個令牌，然後將其貼到外掛設定中。

<a id="1-create-a-personal-access-token"></a>

### 1. 建立個人存取令牌

1. 開啟 https://github.com/settings/tokens.
2. 建立 **fine-grained** 令牌或 **classic** 令牌。
3. 僅授予代理所需的許可權範圍。常見選擇：
   - **Repositories / contents**：讀取或寫入程式碼和檔案
   - **Issues**：列出、建立和更新問題
   - **Pull requests**：列出、審閱和管理 PR
   - **Actions**：檢查工作流程執行（如果您需要 CI 存取）
   - **Metadata**：在 fine-grained 令牌上始終需要
4. 設定您覺得合適的到期時間，然後產生並複製令牌。

當足夠時，優先使用針對特定儲存庫的 fine-grained 令牌。具有 `repo` 的 classic 令牌範圍更廣，應謹慎處理。

<a id="2-configure-the-plugin"></a>

### 2. 設定外掛

在 **Dashboard → Plugins → Configure** 中，將 **GitHub personal access token** 設定為您剛建立的值。

工具呼叫將使用該令牌的許可權執行。如果令牌曾暴露，請從 GitHub Settings 輪換或撤銷它。

<a id="docs"></a>

## 文件

- 使用 GitHub MCP 伺服器：https://docs.github.com/en/copilot/how-tos/context/use-mcp/use-the-github-mcp-server
- 管理個人存取令牌：https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens

標誌是 GitHub 官方 Octocat 標記，放置在白色帶有留白的圖塊上，以便在 Cursor UI 中清晰呈現。

<a id="license"></a>

## 授權

MIT
