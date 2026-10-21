---
name: cursor-sdk
description: "引導使用者在 Cursor TypeScript SDK (`@cursor/sdk`) 之上建置應用程式、指令碼、CI 管線或自動化流程。只要使用者提到整合、安裝或撰寫針對 Cursor SDK 的程式碼，就使用此技能；只要他們提到 `Agent.create`、`Agent.prompt`、`Agent.resume`、`agent.send`、`run.stream`、`CursorAgentError` 或 `@cursor/sdk`；只要他們要求從指令碼、CI/CD 管線、GitHub Action、後端服務或任何非 Cursor IDE 本身的程式碼中，以程式化方式執行 Cursor agents；以及只要他們想要在本地與雲端 runtime 之間做選擇、為 SDK agent 設定 MCP 伺服器，或處理來自 SDK agent 的串流、取消或錯誤。當使用者正在將 Cursor 接入自動化流程、撰寫執行 Cursor 的 bot，或將 REST `/v1/agents` 呼叫移植到 SDK 時，也應觸發此技能，即使他們沒有明確指名該套件。請積極使用此技能，而非憑記憶回答；SDK 介面會持續演進，此技能及其參考檔案是該外部套件的唯一事實來源。"
---

# Cursor SDK

Cursor TypeScript SDK (`@cursor/sdk`) 可程式化地執行 Cursor agents。相同的介面驅動本地 runtime（agent 在您的機器上針對您的檔案執行）與雲端 runtime（agent 在 Cursor 託管或自託管的基礎設施上，針對複製的儲存庫執行並開啟 PR）。

使用此技能協助使用者**快速建立可運作的整合**，並**避免幾個常讓新手踩坑的陷阱**。正式檔案位於 [https://cursor.com/docs/api/sdk/typescript](https://cursor.com/docs/api/sdk/typescript)；此技能僅補充決策邏輯、失敗模式預防，以及可直接擴充套件的正規化。

<a id="voice-and-posture"></a>

## 語氣與姿態

此技能協助使用者**使用 SDK 進行建置**。這裡不是驗證、讚美或推銷 SDK 作為選項的地方。使用者的意圖是輸入；你的工作是執行。

- **當使用者明確指名 SDK 時**（提到 "Cursor SDK"、`@cursor/sdk`、`Agent.create`、`Agent.prompt` 等）：假設他們知道 SDK 是什麼，且已決定使用它。跳過背景說明，跳過鼓勵，直接產出整合。不要說「好訊息」、「SDK 非常適合這個場景」或「這幾乎就是 X 設計的正規化」。
- **當使用者描述一個適合 SDK 的問題但未指名時**（例如「我想要一個審查我 PR 的 bot」、「我想要一個向 Cursor 詢問關於我儲存庫問題的指令碼」）：SDK 尚未成為確認的選擇。簡短地以問題形式提出，然後等待：*「Cursor SDK 是我會在此處選擇的工具，要我以此方式設計嗎，還是你有其他 runtime 的構想？」* 若他們確認，則繼續。若他們反對或想要選項，則提供選項。
- **無論哪種情況，都不要將使用者的意圖重複說給他們聽。** 他們知道自己想要什麼。直接進入設計。

避免使用以下特定開場白（及其近似變體）：

- 「好訊息：這正是該正規化…」
- 「SDK 就是為這種結構而生的。」
- 「太好了，你來對地方了。」
- 「這幾乎就是 SDK 設計的 X。」
- 任何讚美使用者選擇，或以恭維語氣重述其目標的開場白。

建議使用：

- 以設計決策或他們需要知道的第一件事開場。
- 若你確實有設計選擇需要標記（本地 vs 雲端、prompt vs send、同步 vs 串流），用一句話點名並解釋原因；不要以驗證作為字首。

<a id="when-to-open-a-reference-file"></a>

## 何時開啟參考檔案

保持此頁面簡短。僅當使用者的任務明確落在參考檔案範圍內時，才閱讀該檔案：


| 若使用者正在...                                                                    | 閱讀                                                           |
| ------------------------------------------------------------------------------------ | -------------------------------------------------------------- |
| 在本地與雲端 runtime 之間做選擇，或不確定該用哪個           | [`references/runtime-choice.md`](references/runtime-choice.md) |
| 除錯驗證（401、"Missing CURSOR_API_KEY"、團隊 vs 使用者金鑰、本地 vs 生產）    | [`references/auth.md`](references/auth.md)                     |
| 處理錯誤、重試、速率限制、`CursorAgentError`、`result.status === error` | [`references/error-handling.md`](references/error-handling.md) |
| 消費串流、選擇事件型別、取消，或決定串流 vs 等待       | [`references/streaming.md`](references/streaming.md)           |
| 設定 MCP 伺服器（HTTP、stdio、雲端 vs 本地傳輸、驗證注入）      | [`references/mcp.md`](references/mcp.md)                       |
| 使用 sub-agents、resume、artifacts、列出/檢視 agents、`Agent.messages`     | [`references/advanced.md`](references/advanced.md)             |
| 建置特定整合（CI 審查 bot、排程分級、聊天、webhook）     | [`references/patterns.md`](references/patterns.md)             |


以下內容是 80% 任務所需的最小資訊。

<a id="the-three-invocation-patterns"></a>

## 三種呼叫正規化

幾乎所有 SDK 整合都可歸納為三種結構之一。選擇適合任務的那一種，不要混用。

<a id="1-agentprompt--one-shot"></a>

### 1. 一次性：`Agent.prompt(...)`

```typescript
import { Agent } from "@cursor/sdk";

const result = await Agent.prompt("Refactor src/utils.ts for readability", {
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2" },
  local: { cwd: process.cwd() },
});
console.log(result.status, result.result);
```

適用於即發即忘的指令碼、GitHub Actions 步驟，或任何「傳送此 prompt、取得結果、結束」的流程。無串流、無後續、無需記住的清理。若你正在使用此正規化並立即想要 resume，你應該改用正規化 2。

<a id="2-agentcreate--agentsend--durable-with-follow-ups"></a>

### 2. 持久化且含後續：`Agent.create(...)` + `agent.send(...)`

```typescript
import { Agent } from "@cursor/sdk";

const agent = Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2" },
  local: { cwd: process.cwd() },
});

try {
  const run = await agent.send("Find the bug in src/auth.ts");
  for await (const event of run.stream()) {
    if (event.type === "assistant") {
      for (const block of event.message.content) {
        if (block.type === "text") process.stdout.write(block.text);
      }
    }
  }
  const result = await run.wait();

  // Follow-up keeps full conversation context.
  const run2 = await agent.send("Now write a regression test for it");
  await run2.wait();
} finally {
  await agent[Symbol.asyncDispose]();
}
```

適用於需要串流、多輪對話或生命週期操作（取消、狀態監聽器）的場景。這是大多數非平凡整合的結構。

<a id="3-agentresume--pick-up-an-existing-agent-later"></a>

### 3. 稍後接手現有 agent：`Agent.resume(...)`

```typescript
const agent = Agent.resume(previousAgentId, {
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2" },
  local: { cwd: process.cwd() },
});
const run = await agent.send("Also update the changelog");
await run.wait();
```

適用於跨程式邊界：繼續昨晚清理工作的 cron、擴充套件使用者 agent 的 webhook、重新載入對話狀態的互動式 CLI。**內嵌的 `mcpServers` 不會在 resume 之間持久化**。請在 resume 呼叫時再次傳遞它們。

<a id="top-five-traps-read-these-before-writing-code"></a>

## 五大陷阱（撰寫程式碼前請閱讀）

這些陷阱幾乎會讓每個新整合都踩坑。一旦知道它們，預防起來都很簡單。

<a id="1-missing-cloud--repos--silently-defaults-to-local"></a>

### 1. 缺少 `cloud: { repos }` 會靜默預設為本地

`AgentOptions` 不要求 `local` 或 `cloud`；若你省略兩者，SDK 會選擇本地 runtime。陷阱在於：若你意圖使用雲端 agent 卻忘記 `cloud:` 欄位，你會靜默地得到一個本地 agent，沒有錯誤，只有一個本地 agent ID 和本地執行器。當你想要雲端時，請明確傳遞 `cloud: { repos }`；即使本地是預設值，想要本地時也請明確傳遞 `local: { cwd }`。選擇正確的 runtime：請見 [`references/runtime-choice.md`](references/runtime-choice.md)。

<a id="2-two-different-kinds-of-failure-one-instinct-to-conflate-them"></a>

### 2. 兩種不同的失敗型別，卻有一種將它們混為一談的直覺

```typescript
try {
  const run = await agent.send(prompt);
  const result = await run.wait();
  if (result.status === "error") {
    // Agent started but failed mid-run. Inspect transcript, git state, tool outputs.
    console.error(`run failed: ${result.id}`);
    process.exit(2);
  }
} catch (err) {
  if (err instanceof CursorAgentError) {
    // Didn't start. Auth, config, network. Fix environment, retry.
    console.error(`startup failed: ${err.message}, retryable=${err.isRetryable}`);
    process.exit(1);
  }
  throw err;
}
```

`CursorAgentError` 被丟擲 → 執行從未開始（驗證、設定、網路）。`result.status === "error"` → agent 已執行工作，且該工作失敗。修復方式不同、結束程式碼不同、可觀測性也不同。完整分類請見 [`references/error-handling.md`](references/error-handling.md)。

<a id="3-forgetting-await-agentsymbolasyncdispose-leaks-resources"></a>

### 3. 忘記 `await agent[Symbol.asyncDispose]()` 會洩漏資源

SDK 會持有本地執行器、持久化執行儲存庫以及雲端 API 客戶端的控制程式碼。若未進行釋放，將導致子程式洩漏、資料庫連線未關閉，以及在長期執行的服務中造成記憶體持續增長。請務必在 `finally` 中執行釋放操作，或使用 `Agent.prompt()`（它會自動為您釋放），或者如果您的 tsconfig 目標支援，請使用 `await using` 語法：

```typescript
await using agent = Agent.create({ /* ... */ });
```

<a id="4-streaming-is-optional-but-wait-is-almost-required"></a>

### 4. 串流是選用的，但 `wait()` 幾乎是必需的

`run.stream()` 是用來觀察執行狀態的方式；`run.wait()` 是用來取得最終結果的方式。您可以跳過串流，但如果跳過 `wait()`，您將無法判斷該執行是已完成、發生錯誤還是被取消，而且會洩漏該執行內部的觀察器。請務必呼叫 `wait()`。如果您不需要即時輸出，只需單獨呼叫 `wait()` 即可。請參閱 [`references/streaming.md`](references/streaming.md) 以查詢事件型別參考。

<a id="5-not-every-run-operation-is-supported-on-every-runtime"></a>

### 5. 並非每個 `run` 操作都受所有執行環境支援

`Run` 公開了四個操作：`stream`、`wait`、`cancel` 和 `conversation`，而執行環境可能支援也可能不支援這些操作。請務必在呼叫前先使用 `run.supports("...")` 進行檢查，而不是假設其可用：

```typescript
if (run.supports("cancel")) await run.cancel();
if (run.supports("conversation")) console.log(await run.conversation());
```

目前有一個值得注意的缺口：分離或重新水合的執行（即在即時事件儲存庫關閉後，您從 `Agent.getRun(...)` 取得控制程式碼的情況）可能不支援 `stream()`，且 `conversation()` 可能為空。`run.unsupportedReason(op)` 會告訴您原因。雲端 `run.conversation()` 是受支援的，它會從串流中盡力累積資料。

<a id="local-vs-cloud-in-one-sentence-each"></a>

## 本地與雲端，各用一句話說明

- **本地**：在呼叫者的機器上針對 `cwd` 執行，重用其環境和憑證，適合開發迴圈以及已擁有儲存庫檢出（checkout）的 CI 環境。
- **雲端**：在 Cursor 託管的虛擬機器上針對新克隆的 `repos[].url` 執行，適合長時間任務、即發即忘（fire-and-forget）的自動化，以及開啟實際的 PR（`autoCreatePR: true`）。

決策樹、功能差異以及功能缺口（產出物、取消、MCP 傳輸）：[`references/runtime-choice.md`](references/runtime-choice.md)。

<a id="auth-minimum-viable"></a>

## 驗證，最小可行設定

```bash
export CURSOR_API_KEY="cursor_..."  # user API key or team service-account key
```

如果未傳遞 `apiKey`，SDK 會讀取 `CURSOR_API_KEY`。使用者金鑰（來自 [https://cursor.com/dashboard/cloud-agents](https://cursor.com/dashboard/cloud-agents)）和團隊服務帳戶金鑰（團隊設定 → 服務帳戶）均可用於本地和雲端執行。

如果您遇到 401 錯誤，常見原因包括：金鑰貼上時包含周圍的空白字元、金鑰是在不同環境中生成的，或者該金鑰屬於沒有儲存庫存取許可權的使用者（針對雲端執行）。完整疑難排解：[`references/auth.md`](references/auth.md)。

<a id="model-selection"></a>

## 模型選擇

```typescript
import { Cursor } from "@cursor/sdk";

const models = await Cursor.models.list({ apiKey: process.env.CURSOR_API_KEY! });
```

`composer-2` 是目前大多數整合的預設選項。`{ id: "auto" }` 讓伺服器自行選擇。模型 ID 會變動；在呼叫 `Cursor.models.list()` 確認呼叫者具有存取許可權之前，請勿硬編碼罕見的模型 ID。

模型在**本地執行中為必填**，在**雲端執行中為選填**（伺服器會從呼叫者的帳戶解析預設值）。

<a id="production-best-practices"></a>

## 生產環境最佳實務

將以下建議應用於任何無人值守的整合：

1. **將每個 `Agent.create` / `Agent.prompt` / `Agent.resume` 包裹在包含 `[Symbol.asyncDispose]()` 的 try/finally 區塊中**。這是不容商議的。
2. **區分啟動失敗與執行失敗**：`CursorAgentError` 使用結束程式碼 1，`result.status === "error"` 使用結束程式碼 2，僅在 `finished` 時使用結束程式碼 0。這能讓 CI 失敗訊息真正具有可讀性。
3. **在 `send()` 之後立即記錄 `run.id` 和 `agent.agentId`**，然後再開始串流。如果串流掛起，這些 ID 是您在儀錶板或透過 `Agent.getRun(...)` 進行調查時所需的關鍵資訊。
4. **尊重 `error.isRetryable`**：這是後端告知您特定失敗可以安全重試的訊號。盲目重試可能會導致雲端執行重複；而尊重此旗標則不會。
5. **除非需要環境設定，否則請使用 `local: { settingSources: [] }`（預設值）。** 選擇 `"all"` 會從呼叫者的環境中載入專案、使用者、團隊或 MDM 設定，這通常不是服務端所期望的。注意：`settingSources` 位於 `local` 之下，而非頂層；它對雲端代理程式沒有影響（雲端始終遵循團隊、專案和外掛設定）。
6. **對於 CI 中的雲端代理程式，請設定 `skipReviewerRequest: true`**，除非需要通知人類。這會抑制審查者請求步驟，並保持 PR 通知安靜。
7. **在共用基礎架構程式碼中，請務必明確傳遞 `apiKey`**，而不是依賴環境變數。這能讓憑證依賴關係一目瞭然，並防止跨租戶錯誤。
8. **對於真正的一次性任務，請優先使用 `Agent.prompt(...)`**：它會自動為您釋放資源，且較不易發生洩漏。

包含範例的詳細版本：[`references/patterns.md`](references/patterns.md)。

<a id="observing-a-run-you-didnt-launch"></a>

## 觀察您未啟動的執行

您可以稍後透過 ID 檢查任何代理程式或執行：

```typescript
// Cloud: IDs that start with "bc-" auto-route to the cloud API
const info = await Agent.get("bc-abc123", { apiKey });
const run = await Agent.getRun(runId, { runtime: "cloud", agentId: "bc-abc123", apiKey });

// Local: you need the cwd where the agent was created
const localInfo = await Agent.list({ runtime: "local", cwd: process.cwd() });
```

以雲端 `bc-` 為字首的代理程式 ID **並非**執行 ID。如果您只有執行 ID（來自紀錄或 webhook），請將其與執行環境提示一起傳遞給 `Agent.getRun`；請勿混淆這兩者。

<a id="offering-a-canvas"></a>

## 提供 Canvas

如果使用者的整合涉及監控、列出或視覺化代理程式（例如：活躍執行儀錶板、對話回放、工具呼叫時間軸），請提供 Cursor Canvas 來呈現。如果對方接受，請完全交由 `canvas` skill 處理。

<a id="what-this-skill-doesnt-cover"></a>

## 此 Skill 未涵蓋的內容

- Cloud Agents REST API（`/v1/agents/*`）。如果使用者需要非 TS 客戶端，REST API 的說明檔案位於 <https://cursor.com/docs/cloud-agent/api>；在假設其與 SDK 功能對等之前，請先在那裡查詢目前的功能。
- `.cursor/hooks.json` hooks。雲端代理程式會執行它們，但 SDK 不會管理它們；請參閱 Cursor 的 Hooks 說明檔案。
- 私人工作者 / 自託管雲端。請引導使用者前往 Private Workers 說明檔案。
- Python / 非 TS SDK。撰寫本文時，尚無其他語言的第一方 SDK；REST 是可移植的選項。
