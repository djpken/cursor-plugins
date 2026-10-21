<a id="error-handling"></a>

# 錯誤處理

整合錯誤最常見的單一來源：將「agent 無法啟動」與「agent 已執行工作且該工作失敗」視為同一種錯誤。它們並非如此。

<a id="the-two-failure-axes"></a>

## 兩個失敗軸

```
               didn't start                 started, didn't finish cleanly
               ────────────                 ──────────────────────────────
               throws CursorAgentError      returns RunResult { status: "error" | "cancelled" }
               .isRetryable                 .id  (look it up in the dashboard)
               .code / .protoErrorCode      .durationMs   .git   .result
```

務必同時處理兩者。僅使用 try/catch 無法捕捉失敗的執行；僅檢查 `result.status` 無法捕捉驗證失敗。

```typescript
import { Agent, CursorAgentError } from "@cursor/sdk";

async function runOnce(): Promise<void> {
  await using agent = Agent.create({ /* ... */ });
  try {
    const run = await agent.send(prompt);
    const result = await run.wait();

    switch (result.status) {
      case "finished":
        console.log(`ok: ${result.id}`);
        return;
      case "cancelled":
        console.warn(`cancelled: ${result.id}`);
        return;
      case "error":
        throw new Error(`run ${result.id} failed after executing; inspect run state`);
      default: {
        const _exhaustive: never = result.status;
        throw new Error(`unexpected status: ${_exhaustive}`);
      }
    }
  } catch (err) {
    if (err instanceof CursorAgentError) {
      console.error(`startup error (${err.constructor.name}): ${err.message}`);
      if (err.isRetryable) {
        // Backoff-and-retry path
      }
      throw err;
    }
    throw err;
  }
}
```

<a id="cursoragenterror-subtypes"></a>

## `CursorAgentError` 子類別

所有由 SDK 丟擲的錯誤都繼承自 `CursorAgentError`。請檢查具體的子類別以決定後續操作。


| 類別                 | 典型 HTTP | 含義                                       | 修復方式                                                        |
| --------------------- | ------------ | --------------------------------------------------- | ---------------------------------------------------------- |
| `AuthenticationError` | 401          | 金鑰無效、過期或缺失，許可權錯誤      | 修復 `CURSOR_API_KEY`（參見 `[auth.md](auth.md)`）            |
| `RateLimitError`      | 429          | 達到請求或使用量上限                            | 退避；錯誤中攜帶 `isRetryable`                   |
| `ConfigurationError`  | 400/404      | 模型 ID 錯誤、請求格式錯誤、資源未找到 | 請勿重試。修復該呼叫。                                 |
| `NetworkError`        | 503/504      | 上游逾時、暫時性基礎設施問題                   | 若為 `isRetryable`，請加入抖動進行重試                         |
| `UnknownAgentError`   | 無            | 無法透過 proto 程式碼或 HTTP 程式碼進行分類      | 記錄並顯示；請檢查 `.cause` 以檢視原始 `ConnectError` |


它們都攜帶：

- `message` 是面向使用者的描述，已移除 Connect 的 `[unknown]` 字首
- `isRetryable` 來自後端的權威資訊，而非啟發式判斷
- `code` 在相關情況下，代表底層的 Connect/gRPC `Code`
- `protoErrorCode` 是細粒度的後端錯誤程式碼，屬於穩定的列舉值
- `cause` 是用於深入除錯的原始 `ConnectError`，請勿將其洩露給終端使用者

### `UnsupportedRunOperationError`

不同的基底類別與 SDK 有關，而非後端。當您在一個不支援該操作的 `Run` 上呼叫 `run.stream()`、`run.wait()`、`run.cancel()` 或 `run.conversation()` 時，會丟擲此錯誤。常見觸發條件包括：在 live event store 關閉後，從 `Agent.getRun(...)` 取得的已分離 handle 上執行 `cancel()`/`stream()`。Cloud `conversation()` 是受支援的，它會從串流中盡力累積。

請始終優先使用 `run.supports(...)` 而非 `try/catch`：

```typescript
if (run.supports("cancel")) {
  await run.cancel();
} else {
  console.warn(`cancel not supported: ${run.unsupportedReason("cancel")}`);
}
```

<a id="retry-patterns"></a>

## 重試模式

**應重試**（使用退避 + 抖動）：

- `NetworkError` 且 `isRetryable === true`
- `RateLimitError` 且 `isRetryable === true`（罕見；通常表示後端希望您等待的時間比緊密重試更長）
- `UnknownAgentError` 且 `isRetryable === true` 表示後端正在告知您這是暫時性的

**請勿重試**：

- `AuthenticationError` 表示金鑰不會變好
- `ConfigurationError` 表示錯誤的輸入不會變好
- 任何包含 `isRetryable === false` 的情況都表示後端正在告知您這是終止性的

agent 啟動的重試次數請保持較少（≤3）；重新啟動 agent 的代價高昂。若首次嘗試以 `isRetryable === true` 失敗於 `RateLimitError`，請至少退避 30 秒。

```typescript
import { Agent, CursorAgentError, RateLimitError, NetworkError } from "@cursor/sdk";

async function createWithRetry(options: Parameters<typeof Agent.create>[0]) {
  const maxAttempts = 3;
  for (let attempt = 1; attempt <= maxAttempts; attempt++) {
    try {
      return Agent.create(options);
    } catch (err) {
      const retryable =
        err instanceof CursorAgentError &&
        err.isRetryable &&
        (err instanceof NetworkError || err instanceof RateLimitError);
      if (!retryable || attempt === maxAttempts) throw err;
      const backoffMs = 2 ** attempt * 1000 + Math.random() * 500;
      await new Promise(r => setTimeout(r, backoffMs));
    }
  }
  throw new Error("unreachable");
}
```

`Agent.create` 是惰性的，在 `send()` 之前不會存取後端。大多數「啟動」錯誤會在那裡出現。請為 `agent.send(...)` 加上重試，而不僅是 `Agent.create(...)`。

<a id="runresultstatus--error--what-to-do"></a>

## `RunResult.status === "error"` 是應採取的行動

執行至少部分完成，遇到了 agent 無法恢復的問題，並報告了錯誤。`result` 中沒有堆疊追蹤；訊號是：

- `result.id` 是執行 ID。請使用 `Agent.getRun(result.id, { runtime: "cloud", agentId, apiKey })`（cloud）或 `Agent.getRun(result.id, { runtime: "local", cwd })`（local）擷取它，然後讀取 `run.conversation()` 以檢視 agent 嘗試了什麼。
- `result.durationMs` 若為 0 或極小，表示失敗發生得很早，不太可能是 runtime 問題。
- `result.git` 在 cloud 上，用於告知您在失敗前是否已建立分支。
- `result.model` 用於確認執行了哪個模型，在測試多個模型時很有用。

您通常*不會*自動重試 `status: "error"`。agent 已經消耗了 tokens 並承諾了某個方向；盲目重試很可能會做同樣的事情。請為人工分診進行設計：記錄 ID、顯示儀錶板連結，並升級處理。

在以下情況下，重試是合理的：

- 您正在執行大量/扇出工作，且可接受較低的錯誤率。
- 提示詞是純唯讀且冪等的。
- 您也檢查了對話，並知道失敗是環境性的（例如，不穩定的 MCP 伺服器）。

<a id="status-cancelled--what-to-do"></a>

## `status: "cancelled"` 是應採取的行動

在成功的 `run.cancel()`（local 或 cloud）之後，執行會報告此狀態。請將取消視為非致命性：記錄、清理、繼續進行。對於在伺服器端被取消的 cloud 執行（例如，透過儀錶板或同級呼叫者），當您最終 `wait()` 時，也會看到 `"cancelled"`。

<a id="debugging-in-production"></a>

## 生產環境除錯

請始終至少記錄：

- `agent.agentId` 發生在 create/resume 之後，在任何 `send()` 之前
- `run.id` 發生在 `send()` 之後，在串流之前
- `result.status`、`result.durationMs`、`result.git` 發生在 `wait()` 之後
- 發生錯誤時：完整的 `err.message`、`err.constructor.name`、`err.isRetryable`，以及（僅供內部紀錄使用）`err.protoErrorCode`

這五項足以將使用者報告的任何問題與 Cursor 儀錶板中的特定執行進行關聯。

<a id="dont"></a>

## 請勿

- **請勿在每個錯誤上都 `process.exit(1)`**。帶有 `isRetryable: true` 的 `RateLimitError` 需要退避迴圈，而不是退出。
- **請勿將 `err.cause` 記錄給終端使用者**，因為它是包含內部欄位的原始 `ConnectError`。請將 `err.message` 記錄給人類，並將 `err.cause` 僅記錄給內部可觀測性。
- **請勿將 `CursorAgentError` 靜默吞沒**為通用的 `console.warn`。子類別才是訊號。
- **請勿重試 `AuthenticationError` 超過一次**，即使 `isRetryable: true` 也是如此，因為這幾乎總是表示金鑰仍然無效。
