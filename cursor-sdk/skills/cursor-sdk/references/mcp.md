<a id="mcp-servers"></a>

# MCP 伺服器

MCP (Model Context Protocol) 伺服器為 agent 提供額外工具。SDK 支援本機與雲端 runtime，但傳輸語義與憑證處理規則有所不同，若設定錯誤通常不會發出警告（「為什麼 agent 沒有我的 Linear 工具？」）。

<a id="two-transports-three-deployment-shapes"></a>

## 兩種傳輸方式，三種部署形態

```typescript
type McpServerConfig =
  | { type?: "stdio"; command: string; args?: string[]; env?: Record<string,string>; cwd?: string }
  | { type?: "http" | "sse"; url: string; headers?: Record<string,string>; auth?: { CLIENT_ID: string; CLIENT_SECRET?: string; scopes?: string[] } };
```

三種實際會用到的模式：

1. **本機 stdio**：在呼叫端機器上啟動子程式。大多數 `@modelcontextprotocol/server-*` 套件預設使用此方式。
2. **本機 HTTP / 遠端 HTTP**：SDK（本機）或後端（雲端）代表 agent 呼叫的 URL。
3. **雲端 stdio**：在**雲端 VM 內部**啟動的子程式，而非在呼叫端機器上。

兩種 runtime 的形態看起來相似，改變的是執行位置。一個值得注意的具體差異：雲端 stdio 伺服器會拒絕 `cwd`，因為 VM 控制工作目錄，若嘗試設定 `cloud-mcp-utils.ts` 會丟擲 `ConfigurationError("Cloud MCP server cannot include cwd.")`。雲端 stdio 請省略 `cwd`。

<a id="local-runtime--mcp-config"></a>

## 本機 Runtime：MCP 設定

```typescript
const agent = Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2" },
  local: { cwd: process.cwd() },
  mcpServers: {
    filesystem: {
      type: "stdio",
      command: "npx",
      args: ["-y", "@modelcontextprotocol/server-filesystem", process.cwd()],
      cwd: process.cwd(),
      env: { NODE_OPTIONS: "--max-old-space-size=4096" },
    },
    docs: {
      type: "http",
      url: "https://example.com/mcp",
      headers: { Authorization: `Bearer ${process.env.DOCS_TOKEN!}` },
    },
  },
});
```

- Stdio 伺服器作為 Node 程式的子程式執行。請確保 `command` 在 PATH 中，或使用絕對路徑。妥善釋放 agent 以回收這些程式。
- HTTP 伺服器由本機 SDK 直接呼叫。`headers` 會附加在每個請求上。
- `auth: { CLIENT_ID, CLIENT_SECRET?, scopes? }` 會觸發 OAuth 流程，適用於發行正式 OAuth token 的第一方整合。大多數自管 MCP 伺服器只需要 `headers`。

<a id="cloud-runtime--mcp-config"></a>

## 雲端 Runtime：MCP 設定

```typescript
const agent = Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2" },
  cloud: {
    repos: [{ url: "https://github.com/your-org/your-repo", startingRef: "main" }],
  },
  mcpServers: {
    linear: {
      type: "http",
      url: "https://mcp.linear.app/sse",
      headers: { Authorization: `Bearer ${process.env.LINEAR_API_KEY!}` },
    },
    github: {
      type: "stdio",
      command: "npx",
      args: ["-y", "@modelcontextprotocol/server-github"],
      env: { GITHUB_TOKEN: process.env.GITHUB_TOKEN! },
    },
  },
});
```

與本機環境的關鍵差異：

- **HTTP `headers` 與 `auth` 由 Cursor 後端代理。** 敏感的 header 值會在伺服器端被遮蔽，不會傳送到雲端 VM。這是存放 OAuth token、API 金鑰等資訊的安全位置。
- **Stdio `env` 值會注入雲端 VM。** 請將其視為任何生產環境機密，因為 VM 內執行的程式會看到這些值。不要以此方式傳送終端使用者憑證。
- **`command`/`args` 必須能在雲端 VM 內解析**，該 VM 使用標準 Linux 映像。`npx`、`node` 及常見二進位檔可正常運作；其他工具可能需要 `command` 才能在 VM 內解析，或使用容器提供的二進位檔。

在 Dashboard 設定的 MCP 伺服器在雲端也會被採用。使用者只需在 [https://cursor.com/agents](https://cursor.com/agents) 設定一次；`Agent.create` 上的內聯設定會疊加在其上。

<a id="persistence-across-agentresume"></a>

## 跨 `Agent.resume(...)` 的永續性

**內聯 `mcpServers` 不會持久化。** 若你恢復 agent 並期望擁有相同的 MCP 工具存取許可權，請再次傳入設定：

```typescript
const baseMcp = { /* ... */ };
const agent = Agent.resume(agentId, {
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2" },
  local: { cwd: process.cwd() },
  mcpServers: baseMcp,
});
```

Dashboard 設定的伺服器會持久化（它們繫結到使用者而非 agent），因此常見錯誤是將便利性讓給內聯設定，然後疑惑為什麼恢復後會遺失。

<a id="settings-sourced-mcp-servers"></a>

## 來自設定的 MCP 伺服器

預設情況下，本機 SDK agent **不會**載入環境中的使用者、團隊或專案 MCP 設定。透過 `settingSources` 選擇加入：

```typescript
const agent = Agent.create({
  apiKey,
  model: { id: "composer-2" },
  local: {
    cwd: process.cwd(),
    settingSources: ["project", "user"], // or "all"
  },
});
```

`settingSources` 位於 **`local` 內部**，放在頂層會導致 TypeScript 失敗，並在 JavaScript 中靜默無效。它對雲端 agent 無效；雲端永遠遵循 `project` / `team` / `plugins`。

有效來源：`"project"`、`"user"`、`"team"`、`"mdm"`、`"plugins"`，或使用 `"all"` 包含所有專案。請使用能滿足需求的最小集合；`"all"` 可能會引入團隊策略與 plugin，進而影響生產程式碼。

內聯 `mcpServers` 永遠優先：它們是明確輸入，而非環境設定。你可以組合使用。

<a id="choosing-between-http-and-stdio"></a>

## HTTP 與 stdio 的選擇

對於遠端服務（Linear、GitHub、Jira、Figma、你自己的內部工具）：

- **優先使用 HTTP**，當服務有穩定的 MCP endpoint 時。Cursor 後端在雲端處理認證代理；你不需要將機密傳送到 VM。
- **使用 stdio**，當你的整合是本機優先的輔助工具（檔案系統、CLI、開發工具），或需要程式層級隔離時。在雲端，stdio 伺服器在 Cursor VM 內執行，適用於無狀態輔助工具，但對持有長期憑證的任何東西來說都有危險。

對於支援 OAuth 的第一方 MCP 伺服器，請使用 `auth: { CLIENT_ID, CLIENT_SECRET?, scopes? }`，而不是硬編碼 `Authorization` headers。

<a id="reloading-mcp-after-file-changes"></a>

## 檔案變更後重新載入 MCP

若你在 agent 執行期間變更本機磁碟上的 `.cursor/mcp.json` 或其他設定來源，並希望 agent 在不重新建立的情況下採用變更：

```typescript
await agent.reload();
```

這隻影響未來的 `send()` 呼叫；進行中的執行會使用其啟動時的設定。

<a id="debugging-mcp-failures"></a>

## 偵錯 MCP 失敗

MCP 設定問題通常表現為「agent 沒有該工具」，而非丟擲錯誤。檢查清單：

1. **伺服器是否真的註冊了？** 觀察流開始時的 `system` 事件，有時它包含工具目錄。若沒有，表示伺服器未正確連線。
2. **雲端上的 stdio**：`command` 在雲端 VM 映像中可用嗎？`npx` 可用。你的自訂 `/usr/local/bin/…` 不可用。
3. **HTTP headers 缺失？** 在雲端，敏感 headers 會被遮蔽但仍會轉發；缺失的 headers 表示 SDK 設定未包含它們。請再次檢查拼寫（某些伺服器是 `Authorization`，而非 `authorization`）。
4. **恢復時未重新傳入 MCP？** 你遺失了內聯伺服器。請在恢復選項中重新傳入它們。
5. **未設定 `settingSources`？** Agent 未載入你專案的 `.cursor/mcp.json`。請新增 `"project"`。

<a id="copy-paste-starters"></a>

## 複製貼上起始範例

<a id="local-filesystem-helper"></a>

### 本機檔案系統輔助工具

```typescript
mcpServers: {
  filesystem: {
    type: "stdio",
    command: "npx",
    args: ["-y", "@modelcontextprotocol/server-filesystem", process.cwd()],
  },
},
```

<a id="cloud--linear-http-cloud-proxied-auth"></a>

### 雲端 + Linear（HTTP，雲端代理認證）

```typescript
mcpServers: {
  linear: {
    type: "http",
    url: "https://mcp.linear.app/sse",
    headers: { Authorization: `Bearer ${process.env.LINEAR_API_KEY!}` },
  },
},
```

<a id="cloud--github-stdio-secret-injected-into-the-vm"></a>

### 雲端 + GitHub（stdio，機密注入 VM）

```typescript
mcpServers: {
  github: {
    type: "stdio",
    command: "npx",
    args: ["-y", "@modelcontextprotocol/server-github"],
    env: { GITHUB_TOKEN: process.env.GITHUB_TOKEN! },
  },
},
```

<a id="cursor-dashboard-configured-server-no-inline-config-needed"></a>

### Cursor Dashboard 設定的伺服器（無需內聯設定）

擁有 Dashboard 設定伺服器的使用者，只要雲端 agent 被允許使用團隊 MCP，即可免費獲得該伺服器。無需內聯設定；僅在你想要額外伺服器時才疊加。
