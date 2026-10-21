<a id="runtime-choice-local-vs-cloud"></a>

# Runtime 選擇：本地與雲端

SDK 在兩種截然不同的 runtime 之上提供單一介面。選錯不會致命，但會浪費數小時。請刻意做出選擇。

<a id="decision-tree"></a>

## 決策樹

從這裡開始：

1. **agent 是否需要在 GitHub 上開啟 PR？** → 雲端。本地 agent 會修改 `cwd` 中的檔案；它們不會建立分支或推送。
2. **agent 執行的時間是否會超過呼叫端 process 的存活時間？** (觸發即忘任務的 cron、產生夜間工作的 webhook) → 雲端。agent 的生命週期會比你的 script 更長。
3. **agent 是否需要呼叫端機器不具備的運算資源、隔離環境或憑證？** (沙箱評估、受控環境變數、固定 runtime) → 雲端。
4. **這是否為開發迴圈 script、已 checkout 儲存庫的 CI 步驟，或針對使用者目前專案的 CLI？** → 本地。儲存庫已在磁碟上；雲端會無故重新 clone。
5. **使用者是否希望在不向 GitHub 發出網路請求的情況下執行？** (氣隙環境、非 GitHub 儲存庫、對未提交程式碼的實驗) → 本地。雲端需要 GitHub 儲存庫 URL。

如果兩點方向相反，對於生產環境整合，雲端通常是較安全的選擇；對於開發工具，則選擇本地。

<a id="capability-matrix"></a>

## 功能矩陣


| 功能                         | 本地                                 | 雲端 (Cursor 託管)                                         |
| ---------------------------------- | ------------------------------------- | ------------------------------------------------------------- |
| 開啟真實 PR                     | 否                                    | 是 (`cloud.autoCreatePR: true`)                              |
| 處理未提交的本地變更 | 是                                   | 否，從 `startingRef` clone                                |
| 存活時間超過呼叫端 process            | 否                                    | 是，可透過 `agentId` 恢復                                  |
| 執行中可取消                | 是                                   | 是 (伺服器端；建議防禦性地檢查 `run.supports("cancel")`) |
| 產物下載                  | 尚未實作                   | 是                                                           |
| MCP stdio 傳輸                | 是                                   | 是 (命令在雲端 VM 內執行)                        |
| MCP HTTP 傳輸                 | 是                                   | 是                                                           |
| 環境 Cursor 設定            | 透過 `settingSources` 選擇加入           | 一律遵循企業或團隊 hooks                        |
| 需要 GitHub 儲存庫               | 否                                    | 是 (`cloud.repos[].url`)                                     |
| 需要 API key                   | 遠端模型呼叫時 (大多數 prompts) | 一律需要                                                        |


<a id="local-runtime--how-it-actually-works"></a>

## 本地 Runtime，實際運作方式

```typescript
const agent = Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2" },
  local: { cwd: "/absolute/or/relative/path/to/repo" },
});
```

- `cwd` 是 agent 讀取和寫入檔案的位置。該型別接受字串陣列，但本地執行器目前僅使用第一個專案 (`platform.ts` 的 `getCwd` 回傳 `cwd[0]`)；在多重根目錄功能發布前，請傳入單一路徑。
- agent 會啟動 process 內輔助元件 (工具執行、shell runner、MCP stdio process)。請妥善釋放以回收它們。
- 持久化狀態位於 `cwd` 的 Cursor 資料目錄下。`Agent.list({ runtime: "local", cwd })` 會在那裡顯示先前建立的 agent。
- 環境設定 (專案規則、團隊政策、團隊設定的 MCP 伺服器) **預設不會**載入。請在 **`local` 內** 傳入 `settingSources` (例如 `local: { cwd, settingSources: ["project"] }`，或 `"all"` 代表全部) 以選擇加入。
- 本地執行會在呼叫端機器上以呼叫端許可權執行工具。請像對待 `rm -rf` 一樣對待 agent，使用範圍受限的 `cwd`，不要在環境變數中放置你不希望暴露的機密。

何時優先選擇本地：

- CLI 工具、編輯器整合、開發 script。
- 儲存庫已 checkout 且希望直接檢視樹狀結構的 CI 步驟。
- 快速迭代：無網路 clone、無 PR、無審查者通知。

<a id="cloud-runtime--how-it-actually-works"></a>

## 雲端 Runtime，實際運作方式

```typescript
const agent = Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2" },
  cloud: {
    repos: [{ url: "https://github.com/your-org/your-repo", startingRef: "main" }],
    autoCreatePR: true,
    skipReviewerRequest: true, // Keep CI quiet; flip to false for review-worthy changes
  },
});
```

- Cursor 會設定 VM，在 `startingRef` 處 clone `repos[].url`，執行 agent，推送分支，並 (若 `autoCreatePR`) 開啟 PR。
- Agent ID 以 `bc-` 為字首 (背景 composer)。SDK 輔助函式 (`Agent.get`、`Agent.archive` 等) 會根據該字首自動路由。
- 呼叫端 (`CURSOR_API_KEY` 後的使用者) 必須對目標儲存庫具有 GitHub 連線。若無，雲端端會回傳 `ERROR_GITHUB_NO_USER_CREDENTIALS`，這是環境設定問題，而非程式碼錯誤。
- `run.cancel()` 在雲端受支援 (伺服器端取消)；仍建議使用 `run.supports("cancel")` 進行防禦性移植。
- 僅在希望 agent 推送到現有分支時設定 `workOnCurrentBranch: true`，此情況罕見，且通常表示你試圖模擬本地；請改用本地。

何時優先選擇雲端：

- 任何需要開啟 PR 供人類審查的任務。
- 不應阻擋本地 process 的排程或自動化工作。
- 跨多個儲存庫或分支的並行扇出。
- 針對呼叫端未 checkout 的儲存庫執行。

<a id="common-i-meant-the-other-one-symptoms"></a>

## 常見「我指的是另一個」症狀

- **「Agent 已建立，但 GitHub 上沒有發生任何事」**，你傳入了 `local:`，但本意是 `cloud:`。本地不會推送。
- **「雲端 agent 看不到我未提交的變更」**，這是設計使然。請提交或使用本地。
- **「雲端 agent 說找不到我的 GitHub 儲存庫」**，呼叫端的 Cursor 帳號對該儲存庫沒有 GitHub 連線。這不是程式碼錯誤；請在儀錶板中處理。
- **「`run.cancel()` 在我的執行中丟擲例外」**，通常是 detached run handle (在 live channel 已消失的 run 上呼叫 `Agent.getRun(...)`)。請在呼叫前使用 `run.supports("cancel")` 進行防護。
- **「我試圖跨機器重用 agent」**，雲端 agent 可在任何地方恢復 (`Agent.resume(bcId, { ... })`)。本地 agent 範圍限定於其 `cwd` 的資料目錄；從另一臺機器恢復會給你一個新的 agent。

<a id="hybrid-running-a-local-and-a-cloud-agent-from-the-same-script"></a>

## 混合：從同一個 script 執行本地和雲端 agent

完全沒問題，這是常見模式 (例如，本地檢視加雲端 PR)。每個 agent 都是獨立的；請釋放兩者。

```typescript
const localAgent = Agent.create({ /* ... local */ });
const cloudAgent = Agent.create({ /* ... cloud */ });
try {
  const summary = await Agent.prompt("Summarize the diff on HEAD", {
    apiKey,
    model: { id: "composer-2" },
    local: { cwd: process.cwd() },
  });
  const cloudRun = await cloudAgent.send(`Follow up on: ${summary.result}`);
  await cloudRun.wait();
} finally {
  await localAgent[Symbol.asyncDispose]();
  await cloudAgent[Symbol.asyncDispose]();
}
```
