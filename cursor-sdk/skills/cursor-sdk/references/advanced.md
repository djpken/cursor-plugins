<a id="advanced-sub-agents-resume-artifacts-inspection"></a>

# 進階：子代理、恢復、產出物、檢查

您已擁有可運作的 `Agent.create` + `send` + `wait` 迴路。以下是您接下來可以使用的功能。

<a id="sub-agents"></a>

## 子代理

子代理在 v1 版本中**僅限雲端**。`AgentOptions` 上的 `agents:` 欄位是透過雲端建立呼叫中的 `customSubagents` 來連線的；本機執行器會靜默忽略它。如果您在 `local: { ... }` 旁邊傳遞 `agents`，不會有任何作用。在本地對等性發布之前，請將子代理設計範圍限定為雲端代理。

定義具名的子代理，讓主要代理可以透過 `Agent` 工具來啟動它們：

```typescript
const agent = Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2" },
  cloud: {
    repos: [{ url: "https://github.com/your-org/your-repo", startingRef: "main" }],
  },
  agents: {
    "code-reviewer": {
      description: "Expert code reviewer for quality and security.",
      prompt: "Review code for bugs, security issues, and proven approaches. Be concrete and cite file:line.",
      model: "inherit",
    },
    "test-writer": {
      description: "Writes tests for code changes.",
      prompt: "Write comprehensive unit and integration tests. Use the project's test framework.",
    },
  },
});
```

金鑰名稱（`"code-reviewer"`）是主要代理用來指稱子代理的方式。`description` 告訴它何時呼叫；`prompt` 是子代理的系統提示詞。

<a id="good-use-cases"></a>

### 良好的使用案例

- **專業審查**：啟動一個審查者子代理，以稽核主要代理剛進行的變更。
- **平行研究**：主要代理同時將「摘要 X」、「摘要 Y」、「摘要 Z」委派給三個子代理。
- **風險隔離**：在具有受限提示詞的具名子代理中隔離破壞性操作。

<a id="bad-use-cases"></a>

### 不良的使用案例

- 取代一般的輔助函式。如果不需要 LLM 推理，就不要將其設為子代理。
- 深度巢狀的「代理呼叫代理再呼叫代理」鏈。一層子代理幾乎總是足夠的；超過此層級時，您通常需要不同的架構。

<a id="sub-agent-mcp"></a>

### 子代理 MCP

雲端子代理透過**名稱**（而非內聯設定）來參考父級 MCP 伺服器。在 v1 中，內聯的 `McpServerConfig` 專案會被拒絕並回報 `ConfigurationError("cloud custom subagents only support string references in v1.")`。請在父級的 `mcpServers` 地圖中設定伺服器，並從子代理中透過金鑰參考它：

```typescript
const agent = Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2" },
  cloud: { repos: [{ url: "https://github.com/your-org/your-repo", startingRef: "main" }] },
  mcpServers: {
    postgres: {
      type: "http",
      url: "https://mcp.example.com/postgres",
      headers: { Authorization: `Bearer ${process.env.PG_RO_TOKEN!}` },
    },
  },
  agents: {
    "db-reader": {
      description: "Answers read-only questions against the database.",
      prompt: "Query with read-only SQL. Never execute writes.",
      mcpServers: ["postgres"],
    },
  },
});
```

父級的 `mcpServers` 是真實來源；子代理會從該地圖中依名稱挑選。

<a id="resuming-agents"></a>

## 恢復代理

代理 ID 會持續存在。稍後可從任何具有正確選項的流程中恢復：

```typescript
const agent = Agent.resume(agentId, {
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2" },
  local: { cwd: process.cwd() },
  mcpServers: sameInlineMcpYouUsedBefore, // inline MCP is not persisted!
});
const run = await agent.send("continue where we left off");
```

<a id="what-persists"></a>

### 持續存在的內容

- 對話歷史記錄和代理狀態。
- 在建立時由伺服器端烘焙進去的代理範圍設定。
- 雲端：整個代理（如有，包括複製、分支、PR 狀態）。

<a id="what-does-not-persist"></a>

### 不持續存在的內容

- **內聯 `mcpServers`**：在恢復時需再次傳遞它們。
- 本機：`cwd` 是透過路徑識別的。如果路徑已不存在，恢復將無法找到本機代理。
- **`local.settingSources`**：環境設定會根據目前的環境重新載入。

<a id="finding-agents-to-resume"></a>

### 尋找要恢復的代理

```typescript
// Local agents under a cwd:
const local = await Agent.list({ runtime: "local", cwd: process.cwd(), limit: 20 });

// Cloud agents for the caller:
const cloud = await Agent.list({ runtime: "cloud", apiKey });

// Or a specific cloud agent by ID:
const one = await Agent.get("bc-abc123", { apiKey });
```

兩個清單呼叫都會傳回 `{ items, nextCursor }`。請使用 `nextCursor` 進行分頁。

<a id="inspecting-runs"></a>

## 檢查執行

```typescript
const runs = await Agent.listRuns(agentId, { runtime: "local", cwd: process.cwd() });
const run  = await Agent.getRun(runs.items[0].id, { runtime: "local", cwd: process.cwd() });

// Replay the stream (works when the persisted events are available):
if (run.supports("stream")) {
  for await (const event of run.stream()) { /* ... */ }
}
```

`run.conversation()` 傳回累積的 `ConversationTurn[]`，對於渲染對話記錄 UI 很有用。即時本機執行包含工具呼叫詳情；重播的執行會從持久化的串流重建，如果來源執行時未捕捉工具引數或結果，內容可能會較稀疏。

<a id="cloud-run-ids-vs-agent-ids"></a>

### 雲端執行 ID 與代理 ID

雲端代理 ID 以 `bc-` 開頭。雲端執行 ID 看起來像一般的 UUID。**不要將 `bc-` ID 傳遞給 `getRun` 並期望它能運作**：您需要的是執行 ID。

要從雲端 `bc-` 代理取得執行 ID：

```typescript
const runs = await Agent.listRuns("bc-abc123", { runtime: "cloud", apiKey });
for (const r of runs.items) console.log(r.id);
```

<a id="persisted-messages"></a>

## 持久化訊息

`run.conversation()` 範圍限定於單一執行。要取得代理所有執行的完整持久化對話：

```typescript
const messages = await Agent.messages.list(agentId, { runtime: "local", cwd: process.cwd() });
```

訊息是原始且模式穩定的結構：使用者回合、助理回合、中繼資料。您通常會在程式碼中透過 `extractReadableMessages(...)` 型別的輔助函式來傳遞它們以進行渲染。

<a id="artifacts"></a>

## 產出物

在雲端代理上，代理可以產生超出 git diff 的產出物檔案：例如測試結果、覆蓋率報告、產生的資產：

```typescript
const artifacts = await agent.listArtifacts();
for (const a of artifacts) console.log(a.path, a.sizeBytes);

const buffer = await agent.downloadArtifact(artifacts[0].path);
```

本機代理目前從 `listArtifacts()` 傳回空清單，並從 `downloadArtifact()` 丟擲例外。目前請將產出物流程視為僅限雲端。

<a id="lifecycle-archive-unarchive-delete"></a>

## 生命週期：封存、取消封存、刪除

```typescript
await Agent.archive("bc-abc123",   { apiKey });
await Agent.unarchive("bc-abc123", { apiKey });
await Agent.delete("bc-abc123",    { apiKey });
```

- **封存**會將代理隱藏於預設清單中，但保留歷史記錄（在 `Agent.list({ runtime: "cloud" })` 上設定 `includeArchived: true` 以包含它們）。
- **取消封存**會將其恢復。
- **刪除**是破壞性的：無法復原。請謹慎設定範圍。

缺少 ID 會丟擲例外。不要吞掉錯誤；缺少 ID 通常表示您的記錄管理有誤。

<a id="account-and-catalog"></a>

## 帳戶與目錄

```typescript
import { Cursor } from "@cursor/sdk";

const me      = await Cursor.me({ apiKey });               // apiKeyName, userEmail, createdAt
const models  = await Cursor.models.list({ apiKey });       // available model IDs
const repos   = await Cursor.repositories.list({ apiKey }); // GitHub repos the caller has connected
```

- `Cursor.models.list()`：如果您不知道有哪些可用選項，請在構建選項前呼叫。不要硬編碼特殊的模型 ID。
- `Cursor.repositories.list()`：提供呼叫者實際可用的 `cloud.repos[].url` 專案。如果您正在建置一個要求使用者選擇儲存庫的 UI，這就是您的來源。
- `Cursor.me()`：確認金鑰的身分。在運維工具中很有用。

這三者均僅限雲端，且需要 API 金鑰。

## `agent.reload()`

如果您希望底層本機執行器在重新建立代理之前就能拾取變更，請在變更本機設定（`.cursor/*`、MCP 設定檔、hook 檔案）後呼叫：

```typescript
await agent.reload();
```

不適用於進行中的執行：它影響未來的 `send()` 呼叫。

<a id="putting-it-together--long-lived-service"></a>

## 整合應用：長生命週期服務

```typescript
import { Agent } from "@cursor/sdk";

export class MyAgentOrchestrator {
  async runOrContinue(agentId: string | undefined, prompt: string) {
    const apiKey = process.env.CURSOR_API_KEY!;
    const options = {
      apiKey,
      model: { id: "composer-2" },
      local: { cwd: "/var/app/repo" },
      mcpServers: {
        linear: {
          type: "http" as const,
          url: "https://mcp.linear.app/sse",
          headers: { Authorization: `Bearer ${process.env.LINEAR_API_KEY!}` },
        },
      },
    };
    await using agent = agentId
      ? Agent.resume(agentId, options)
      : Agent.create(options);

    const run = await agent.send(prompt);
    const result = await run.wait();
    return { agentId: agent.agentId, runId: run.id, status: result.status };
  }
}
```

關鍵選擇：

- `await using` 語法表示您不會忘記釋放資源。
- MCP 設定在每次呼叫時重新傳遞，以便恢復能正常運作。
- 在呼叫者的資料庫中持久化 `agentId`；在後續請求中恢復。
