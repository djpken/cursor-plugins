<a id="streaming-and-run-lifecycle"></a>

# 串流與執行生命週期

`run.stream()` 是 `SDKMessage` 事件的非同步產生器。本機與雲端執行環境使用相同的事件結構，因此只需撰寫一個消費者即可在所有環境中運作。

<a id="when-to-stream-vs-just-wait"></a>

## 何時使用串流，何時僅使用 `wait()`


| 情境                                             | 使用串流？         | `wait()`？ |
| ----------------------------------------------------- | --------------- | --------- |
| 向使用者即時呈現輸出（CLI、聊天、網頁介面）   | 是             | 是       |
| 只需成功或失敗結果的即發即忘指令碼   | 否              | 是       |
| 希望記錄工具呼叫以便除錯的 CI 步驟    | 是，輸出至 stderr  | 是       |
| 可觀察性：記錄每個事件以便稍後重放 | 是，將其持久化 | 是       |
| 輪詢您未啟動的另一個執行                 | 使用串流即可  | 是       |


您幾乎總是希望使用 `wait()`。有時您可能不需要 `stream()`。不存在正確的「串流但不等待」模式，串流告訴您發生了什麼，而 `wait()` 告訴您是否成功。

<a id="the-canonical-consumer"></a>

## 標準消費者

```typescript
for await (const event of run.stream()) {
  switch (event.type) {
    case "assistant":
      for (const block of event.message.content) {
        if (block.type === "text") process.stdout.write(block.text);
        // block.type === "tool_use" means the assistant announced a tool call;
        // the actual execution will follow via tool_call events.
      }
      break;
    case "thinking":
      // Reasoning content. Usually hidden from end users, kept for logs.
      process.stderr.write(`[thinking] ${event.text}\n`);
      break;
    case "tool_call":
      console.error(`[tool] ${event.name} ${event.status} (${event.call_id})`);
      if (event.args !== undefined) console.error(`  args: ${JSON.stringify(event.args)}`);
      if (event.result !== undefined) console.error(`  result: ${JSON.stringify(event.result)}`);
      break;
    case "status":
      console.error(`[status] ${event.status}`);
      break;
    case "task":
      if (event.text) console.error(`[task] ${event.text}`);
      break;
    case "user":
      // Echo of the prompt. Usually ignorable.
      break;
    case "system":
      // Init metadata (model, tool list). Useful for logs.
      break;
    case "request":
      // Request tracking. Log event.request_id for correlation.
      break;
  }
}

const result = await run.wait();
```

<a id="event-reference"></a>

## 事件參考

每個事件都有 `agent_id` 和 `run_id`。`type` 用於區分其他所有內容。

### `"assistant"`

模型文字或工具使用公告。

```typescript
{
  type: "assistant",
  message: { role: "assistant", content: Array<TextBlock | ToolUseBlock> }
}
```

- `TextBlock` = `{ type: "text", text: string }`，請呈現此內容。
- `ToolUseBlock` = `{ type: "tool_use", id, name, input }`，助理正在請求呼叫工具。您無需採取行動，執行環境會執行並發出 `tool_call` 事件。對於希望在 LLM 請求時立即顯示「正在呼叫 `grep`…」的介面來說，這很有用。

### `"thinking"`

推理內容。請將其排除在主要介面之外（使用者不需要），但保留在紀錄中（除錯時極具價值）。

```typescript
{ type: "thinking", text: string, thinking_duration_ms?: number }
```

### `"tool_call"`

實際的工具執行生命週期。

```typescript
{
  type: "tool_call",
  call_id: string,
  name: string,
  status: "running" | "completed" | "error",
  args?: unknown,
  result?: unknown,
  truncated?: { args?: boolean; result?: boolean }
}
```

先以 `status: "running"` 發出一次（引數可用，結果未定義），然後再以 `status: "completed"` 或 `"error"` 發出（結果可用）。`truncated` 旗標表示負載已在伺服器端修剪，請勿嘗試完整解析。

### `"status"`

執行生命週期轉換。與 `SDKStatusMessage` 對應：


| `status` 值 | 含義                                   |
| -------------- | --------------------------------------- |
| `"CREATING"`   | 雲端執行正在設定中（複製、啟動） |
| `"RUNNING"`    | 正在積極執行                      |
| `"FINISHED"`   | 成功完成                  |
| `"ERROR"`      | 執行中途失敗                   |
| `"CANCELLED"`  | 執行已被取消                       |
| `"EXPIRED"`    | 執行已過期                            |


**不要將 `FINISHED` 狀態事件視為「我可以跳過 `wait()`」**，這只是預先通知，而非終端結果。`wait()` 會回傳包含使用量、持續時間和 git 資訊的 `RunResult`，這些資訊無法從串流中取得。

### `"task"`

較高層級的任務狀態訊息（例如「規劃中」、「正在編輯檔案」）。選填；對於摘要進度介面很有用。

### `"user"`

執行開始時使用者提示的回顯。消費者通常會忽略此內容。

### `"system"`

初始化中繼資料：實際使用的模型、工具目錄。建議在串流開始時記錄一次。

### `"request"`

用於與伺服器端可觀察性進行關聯的 Request-ID 追蹤。如果您有內部追蹤系統，請記錄 `event.request_id`。

<a id="callbacks-vs-the-stream"></a>

## 回呼與串流

`agent.send(...)` 也接受回呼：

```typescript
await agent.send(prompt, {
  onDelta: ({ update }) => { /* raw executor delta */ },
  onStep:  ({ step })   => { /* batched step after text/thinking/tool settle */ },
});
```

- `onDelta` 會在每個原始執行器增量上觸發，其粒度遠小於 `SDKMessage` 串流。對於希望進行區塊內更新的本地介面很有用。在整合中較少見。
- `onStep` 在邏輯步驟完成時觸發（文字、思考與工具捆綁在一起）。類似於從串流中遍歷 `assistant`+`tool_call`，但已預先組裝。

在下一個更新進入管線之前，會等待這兩個回呼完成，您可以透過回傳 Promise 來施加背壓。請謹慎在 `onDelta` 中放入緩慢的 I/O 操作，否則可能會導致執行停滯。

大多數消費者應優先使用 `run.stream()`。只有在建立本地介面且需要更細粒度結構時，才使用 `onDelta` / `onStep`。

<a id="cancellation"></a>

## 取消

```typescript
if (run.supports("cancel")) {
  setTimeout(() => run.cancel(), 30_000);
}
```

`run.cancel()` 支援本機和雲端執行。對於雲端，它會向伺服器的取消端點傳送 POST 請求，並根據伺服器的權威回應協調本地狀態。無論如何都請使用 `run.supports("cancel")` 進行保護，因為分離或重放的執行控制程式碼（`Agent.getRun(...)`）可能沒有實時的取消通道，而保護是正確的防禦姿態。

取消後，請繼續消費串流直到其結束；您會看到一個終端 `status` 事件，且 `run.wait()` 將以 `status: "cancelled"` 解決。

<a id="status-listener-stream-free-observation"></a>

## 狀態監聽器（無串流觀察）

如果您只想知道執行何時轉換，而不關心內容：

```typescript
const unsubscribe = run.onDidChangeStatus(status => {
  console.error(`[status-listener] ${status}`);
});

await run.wait();
unsubscribe();
```

這不需要 `stream()`，並會在每次轉換時觸發。對於不呈現代理輸出的進度介面很有用。

<a id="observing-a-run-you-didnt-launch"></a>

## 觀察您未啟動的執行

```typescript
const existing = await Agent.getRun(runId, { runtime: "cloud", agentId: "bc-abc123", apiKey });
if (existing.supports("stream")) {
  for await (const event of existing.stream()) {
    // same loop
  }
}
const result = await existing.wait();
```

重放串流會從持久化狀態重建事件。當原始執行捕獲了工具負載（引數/結果）時，它們會存在；較舊的執行可能較為稀疏。

<a id="backpressure-and-long-streams"></a>

## 背壓與長串流

非同步迭代器會自然施加背壓，執行環境不會以快於您的 `for await` 消耗事件的速度產生事件。但如果您的消費者對每個事件執行繁重的工作（資料庫寫入、網路呼叫），可能會導致執行在其生命週期內停滯。在重要時，請將事件排隊並在帶外處理。

<a id="common-mistakes"></a>

## 常見錯誤

- **未消耗串流**，會導致資源保持開啟。如果您開啟了 `run.stream()`，必須完全消耗它或呼叫 `run.cancel()`。
- **假設工具 `args`/`result` 總是存在**，它們是選填的；在解構前請先檢查。
- **在未檢查 `name` 的情況下將 `tool_call.result` 解析為特定結構**，每個工具都有其自身的結構。如果需要強型別，請先根據 `event.name` 進行分支。
- **將 `"status": "FINISHED"` 視為結束而做出反應**，這是終端狀態，但 `wait()` 仍必須解決才能提供使用量/git/持續時間。請務必也使用 `await run.wait()`。
