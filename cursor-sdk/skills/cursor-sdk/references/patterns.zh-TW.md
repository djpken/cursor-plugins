<a id="integration-patterns"></a>

# 整合模式

五個起始範本，對應人們實際建構的形狀。複製其中一個，刪除不需要的部分。

每個模式都應用了本 skill 其他部分的 [錯誤處理](error-handling.md) 與 [串流](streaming.md) 最佳實務，在調整時請勿移除這些內容。

---

<a id="1-github-action-automated-code-review-on-prs"></a>

## 1. GitHub Action：在 PR 上進行自動程式碼審查

目標：當 PR 開啟時，針對該 PR 執行雲端 agent，並發布審查意見。

```typescript
import { Agent, CursorAgentError } from "@cursor/sdk";

async function main() {
  const {
    PR_URL,
    REPO_URL,
    HEAD_REF,
    BASE_REF,
    CURSOR_API_KEY,
    GITHUB_TOKEN,
  } = process.env;

  if (!CURSOR_API_KEY || !REPO_URL || !HEAD_REF) {
    console.error("Missing required env: CURSOR_API_KEY, REPO_URL, HEAD_REF");
    process.exit(1);
  }

  await using agent = Agent.create({
    apiKey: CURSOR_API_KEY,
    model: { id: "composer-2" },
    cloud: {
      repos: [{ url: REPO_URL, startingRef: HEAD_REF }],
      workOnCurrentBranch: true,
      skipReviewerRequest: true, // don't re-page reviewers
    },
    mcpServers: GITHUB_TOKEN
      ? {
          github: {
            type: "stdio",
            command: "npx",
            args: ["-y", "@modelcontextprotocol/server-github"],
            env: { GITHUB_TOKEN },
          },
        }
      : undefined,
  });

  const prompt = `Review the changes on ${HEAD_REF} vs ${BASE_REF} for ${PR_URL}.
Focus on: correctness, security, readability. Post GitHub review comments inline
for concrete issues. No praise-only comments. If nothing to flag, say so.`;

  try {
    const run = await agent.send(prompt);
    console.log(`[review] agent=${agent.agentId} run=${run.id}`);

    for await (const event of run.stream()) {
      if (event.type === "status") console.log(`[review] ${event.status}`);
      if (event.type === "tool_call" && event.status !== "running") {
        console.log(`[review] tool: ${event.name} -> ${event.status}`);
      }
    }

    const result = await run.wait();
    if (result.status !== "finished") {
      console.error(`[review] run ${result.id} ended as ${result.status}`);
      process.exit(2);
    }
    console.log(`[review] done: ${result.durationMs}ms`);
  } catch (err) {
    if (err instanceof CursorAgentError) {
      console.error(`[review] startup failed: ${err.message}`);
      process.exit(err.isRetryable ? 75 : 1); // EX_TEMPFAIL for transient
    }
    throw err;
  }
}

main();
```

為何採用此形狀：

- 雲端 runtime（需要發布 GitHub 意見；可獨立於 runner 運作）。
- `skipReviewerRequest: true` 讓該 action 在 CI 中保持靜音。
- 結束碼：`0` 已完成，`1` 永久性啟動失敗，`2` 執行結束且狀態為 `error`，`75` 暫時性可重試失敗。

---

<a id="2-scheduled-triage-cron-driven-cloud-runs-with-resume"></a>

## 2. 排程分級：由 cron 驅動的雲端執行與恢復

目標：每天早上，恢復昨天的分級 agent，並要求它對今天新的 Linear 工單進行分級。

```typescript
import { Agent, CursorAgentError } from "@cursor/sdk";
import { readFile, writeFile } from "node:fs/promises";

const STATE_PATH = "/var/lib/triage/state.json";

const mcpServers = {
  linear: {
    type: "http" as const,
    url: "https://mcp.linear.app/sse",
    headers: { Authorization: `Bearer ${process.env.LINEAR_API_KEY!}` },
  },
};

async function main() {
  const state = await readState();

  await using agent = state.agentId
    ? Agent.resume(state.agentId, {
        apiKey: process.env.CURSOR_API_KEY!,
        model: { id: "composer-2" },
        cloud: { repos: [{ url: process.env.REPO_URL!, startingRef: "main" }] },
        mcpServers, // must re-pass on resume
      })
    : Agent.create({
        apiKey: process.env.CURSOR_API_KEY!,
        model: { id: "composer-2" },
        cloud: { repos: [{ url: process.env.REPO_URL!, startingRef: "main" }] },
        mcpServers,
      });

  console.log(`[triage] agent=${agent.agentId}`);

  try {
    const run = await agent.send(
      "Triage new Linear tickets opened in the last 24h. Label, assign, comment with next steps."
    );
    const result = await run.wait();
    if (result.status === "error") {
      console.error(`[triage] run ${result.id} errored`);
    }
    await writeState({ agentId: agent.agentId, lastRunId: result.id });
  } catch (err) {
    if (err instanceof CursorAgentError && err.isRetryable) {
      console.error(`[triage] transient: ${err.message}, will retry next tick`);
      return;
    }
    throw err;
  }
}

async function readState(): Promise<{ agentId?: string; lastRunId?: string }> {
  try { return JSON.parse(await readFile(STATE_PATH, "utf-8")); }
  catch { return {}; }
}
async function writeState(s: { agentId: string; lastRunId: string }) {
  await writeFile(STATE_PATH, JSON.stringify(s));
}

main();
```

為何採用此形狀：

- 在 cron 呼叫之間持久化 `agentId` 可保留對話記憶（例如：「記住我們昨天已經分級了哪些工單」）。
- 每次恢復時重新傳遞 MCP。
- 對可重試錯誤採取優雅處理：跳過本次執行，嘗試下一次。

---

<a id="3-one-shot-analysis-script"></a>

## 3. 一次性分析指令碼

目標：開發人員對本地儲存庫執行指令，並獲得書面分析。

```typescript
#!/usr/bin/env node
import { Agent } from "@cursor/sdk";

const prompt = process.argv.slice(2).join(" ").trim();
if (!prompt) {
  console.error("Usage: analyze.ts <question>");
  process.exit(1);
}

const result = await Agent.prompt(prompt, {
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2" },
  local: { cwd: process.cwd() },
});

console.log(result.result ?? "(no output)");
process.exit(result.status === "finished" ? 0 : 2);
```

為何採用此形狀：

- `Agent.prompt` 會為您進行清理。非常適合一次性 CLI。
- 無串流，因為這是一次性執行。
- 結束碼攜帶狀態；可用於 shell 管線（`&&`、`|| fallback`）。

---

<a id="4-backend-service-user-scoped-agents-behind-an-http-api"></a>

## 4. 後端服務：HTTP API 後方的使用者範圍 agent

目標：您的後端服務公開一個端點，代表使用者執行 Cursor agent。每位使用者擁有自己的持久化 agent。

```typescript
import express from "express";
import { Agent, CursorAgentError } from "@cursor/sdk";

const app = express();
app.use(express.json());

// Pretend this lives in your DB
const userAgents = new Map<string, string>();

app.post("/agents/:userId/send", async (req, res) => {
  const { userId } = req.params;
  const { prompt } = req.body;
  const apiKey = process.env.CURSOR_SERVICE_ACCOUNT_KEY!;

  const existing = userAgents.get(userId);
  const options = {
    apiKey,
    model: { id: "composer-2" as const },
    cloud: { repos: [{ url: "https://github.com/your-org/workspace", startingRef: "main" }] },
  };

  try {
    await using agent = existing
      ? Agent.resume(existing, options)
      : Agent.create(options);

    const run = await agent.send(prompt);
    const result = await run.wait();

    userAgents.set(userId, agent.agentId);
    res.json({
      agentId: agent.agentId,
      runId: result.id,
      status: result.status,
      durationMs: result.durationMs,
    });
  } catch (err) {
    if (err instanceof CursorAgentError) {
      res.status(err.code === undefined ? 500 : 502).json({
        error: err.constructor.name,
        message: err.message,
        retryable: err.isRetryable,
      });
      return;
    }
    res.status(500).json({ error: "internal" });
  }
});

app.listen(3000);
```

為何採用此形狀：

- 使用服務帳戶金鑰，而非使用者金鑰，因為這是共享基礎設施。
- 每個請求使用 `await using`；請求之間不保留 agent 控制程式碼。
- `userAgents` 是您的資料庫的替代方案；為每位使用者持久化 `agentId` 以進行恢復。
- 錯誤介面會傳遞 `isRetryable`，以便呼叫者能智慧地進行退避。

如果您需要向客戶端串流回應，請不要這樣做，改為使用 `run.stream()` 進入伺服器傳送事件端點。

---

<a id="5-fan-out-run-an-agent-against-many-repos-in-parallel"></a>

## 5. 扇出：並行對多個儲存庫執行 agent

目標：為清單中的每個儲存庫啟動一個雲端 agent，並收集結果。

```typescript
import { Agent, CursorAgentError } from "@cursor/sdk";

async function dispatchOne(repoUrl: string, apiKey: string, prompt: string) {
  await using agent = Agent.create({
    apiKey,
    model: { id: "composer-2" },
    cloud: {
      repos: [{ url: repoUrl, startingRef: "main" }],
      autoCreatePR: false,
      skipReviewerRequest: true,
    },
  });
  try {
    const run = await agent.send(prompt);
    const result = await run.wait();
    return { repoUrl, agentId: agent.agentId, status: result.status, runId: result.id };
  } catch (err) {
    if (err instanceof CursorAgentError) {
      return { repoUrl, error: err.constructor.name, message: err.message };
    }
    throw err;
  }
}

const repos = [
  "https://github.com/your-org/service-a",
  "https://github.com/your-org/service-b",
  "https://github.com/your-org/service-c",
];

const prompt = "Audit Dockerfile for outdated base images. Propose an update.";
const apiKey = process.env.CURSOR_API_KEY!;

const results = await Promise.allSettled(repos.map(r => dispatchOne(r, apiKey, prompt)));
console.log(JSON.stringify(results, null, 2));
```

為何採用此形狀：

- `Promise.allSettled`，一個儲存庫失敗不會導致其他儲存庫失敗。
- 使用雲端 runtime，因為雲端 agent 實際上是獨立的 VM；本地扇出會透過呼叫者的機器序列化。
- 每個 agent 使用 `await using`，以便即使其他 agent 仍在執行，每個 agent 也能完成清理。
- 使用 `autoCreatePR: false`，因為我們希望先審查結果；在選定透過審核的專案後，執行後續操作以開啟 PR。

**速率限制感知**：一次扇出 100 個 agent 會觸及後端限制。對於大型 N，請分批處理：

```typescript
async function runInBatches<T, R>(items: T[], size: number, fn: (item: T) => Promise<R>) {
  const results: R[] = [];
  for (let i = 0; i < items.length; i += size) {
    const batch = items.slice(i, i + size);
    results.push(...await Promise.all(batch.map(fn)));
  }
  return results;
}
```

---

<a id="cross-cutting-best-practices"></a>

## 橫向最佳實務

應用於所有五個模式：

- **在串流之前記錄 `agent.agentId` 和 `run.id`。** 每次失敗調查都從這兩個 ID 開始。
- **根據失敗型別區分結束/回應碼。** 您的運維團隊應能一眼分辨「無法驗證」與「agent 做了工作但出錯」。
- **尊重 `isRetryable`。** 對暫時性錯誤進行退避，對終止性錯誤不要重試。對失敗的雲端執行進行盲目重試會產生重複的 PR。
- **`await using` 或明確的 `finally`。** 每個 `Agent.create` / `Agent.resume` 都需要一個明確的清理路徑。
- **明確傳遞 `apiKey`。** 不要依賴共享基礎設施程式碼中的環境變數。
- **不要提交 `CURSOR_API_KEY`。** 不要記錄它。當您需要確認使用的是哪個金鑰時，僅使用字首（`cursor_01ab...`）。
