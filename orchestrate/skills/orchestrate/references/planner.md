root 與 subplanner 的操作手冊。Dispatcher 會讀取 `dispatcher.md`。

# Planner

root 與 subplanner 的行為方式相同。root 向使用者報告；subplanner 向其父層報告。

<a id="prerequisites"></a>

## 先決條件

在採取行動前載入：

1. 載入 [cursor-sdk plugin](https://github.com/cursor/plugins/tree/main/cursor-sdk) 以進行驗證、啟動，以及 `CursorAgentError` 與 `RunResult.status === "error"` 的處理。

指令碼預期 `bun` 在 PATH 上。在此 skill 的 `scripts/` 目錄內使用 `bun install` 安裝相依套件。

在計畫或狀態結構變更後，於 `scripts/` 中使用 `bun run generate-schemas` 從 `scripts/schemas.ts` 重新產生 `schemas/*.json`。

Slack 可見性使用 `SLACK_BOT_TOKEN`。必要的許可權範圍：

- 發布與編輯訊息：`chat:write`。
- 在 bot 訊息上設定自訂使用者名稱與圖示：`chat:write.customize`。
- 在加入前即可於公開頻道發布訊息：`chat:write.public`。
- 上傳交接產出物：`files:write`。
- 與 `files:write` 搭配用於 upload v2 流程：`files:read`。
- 監控 kickoff 訊息上的 Andon `:rotating_light:` 反應：`reactions:read`。
- 透過 `conversations.replies` 讀取執行緒回覆：`channels:history`。如果執行執行緒位於私人頻道，請改為新增 `groups:history`。

選填：

- 針對 dispatcher 的 git 電子郵件進行盡力而為的名稱查詢：`users:read.email`。若無此許可權，請明確傳遞 `--dispatcher-name`。

在這些許可權範圍生效前，Slack 呼叫會在 `attention.log` 中失敗並顯示 Slack 的 `missing_scope` 錯誤。執行仍會繼續，因為 git 與磁碟是權威來源。

<a id="source-of-truth"></a>

## 權威來源

Git 與磁碟是基礎。

- `plan.json` 包含任務圖、Slack 設定、儲存庫 URL 與模型選擇。
- `state.json` 包含任務狀態、agent/run id、分支名稱與 Slack 訊息時間戳記。
- `handoffs/*.md` 包含 worker 與 verifier 的輸出。
- `attention.log` 包含操作者可見的失敗與決策。

Slack 是人類可見性，而非任務狀態。指令碼發布一則 kickoff 訊息，在該執行緒中映象任務狀態，並讀取 kickoff 訊息上的 `:rotating_light:` 以進行 Andon。Kickoff 後，Slack 寫入操作保留在執行執行緒中；adapter 要求這些寫入操作具備 `threadTs`。如果 Slack 中斷，編排正確性不會改變。

Orchestrate 擁有 Slack 狀態映象、Andon 與評論重試佇列。Agents 仍可直接呼叫 MCP 以進行 Linear、GitHub、Slack、Notion 及其他即席外部工作。這些系統不是 orchestrate 的目的地。

<a id="phase-1-publish-tasks"></a>

## 階段 1：發布任務

在 `<workspace>` 處撰寫 `plan.json`；預設工作區為 `.orchestrate/<rootSlug>/`。

```json
{
  "$schema": "<path-to-orchestrate>/schemas/plan.schema.json",
  "goal": "<verbatim user goal>",
  "summary": "ship the dark-mode toggle end to end",
  "rootSlug": "dark-mode",
  "baseBranch": "main",
  "repoUrl": "https://github.com/example-org/example-repo",
  "tasks": [
    {
      "name": "frontend-toggle",
      "type": "worker",
      "scopedGoal": "Add a Settings UI toggle that flips `useDarkMode` in localStorage.",
      "pathsAllowed": ["packages/ui/src/settings/**"],
      "acceptance": ["Toggle renders in Settings > Appearance"]
    }
  ]
}
```

`summary` 是給 Slack 執行緒中的人類看的；`goal` 是 agent 的完整上下文。當 `summary` 未設定時，kickoff 會回退到截斷的 `goal`。

在第一次 `run --root` 時，指令碼使用 `plan.slackChannel` 進行 Slack kickoff 並寫入 `plan.slackKickoffRef`。Root 計畫從 `kickoff --slack-channel`、`run --root --slack-channel` 或 `SLACK_CHANNEL_ID` 取得 `slackChannel`。Subplanners 繼承這兩個欄位，以便整個樹狀結構映象到一個執行緒中。

規劃規則：

- 合併是任務。發布一個 worker，其 `scopedGoal` 說明要合併哪些分支、衝突意圖與驗證。
- 除非你能說出 subplanner 會進行的分解，否則優先使用 worker。
- 一個 worker 可以承擔很多工作。Workers 與 verifiers 是具備數小時執行時間的完整雲端 agents：多檔案切片、多步驟重構、完整的重現/修復/測試週期都適合在單一 spawn 中完成。每次 spawn 都會產生雲端 agent 執行時間、Slack 雜訊以及你自己的協調開銷。預設使用較少、較廣泛的 workers；僅當切片真正獨立或存在實際競爭風險時，才使用更細粒度的分割。
- 預設使用 verifiers。使用 `type: "verifier"` 與 `verifies: "<target-task-name>"`。
- 使用 `verify` 作為具體的檢查配方。Workers 將其讀取為目標行為；verifiers 從其目標繼承它。
- 僅對希望作為其自身 draft PR 發布的獨立 worker 任務設定 `openPR: true`。
- 為定量宣告新增 `measurements[]`。指令碼會在交接後在 worker 分支上重新執行每個命令並記錄漂移。
- 保持 fan-in 規模小。如果任務需要許多上游交接，請先發布一個聚合 worker。
- 最小化路徑重疊。當兄弟所有權重要時，列出禁止的路徑。
- 將任務規格放在 `plan.tasks[]` 中。將共享產出物放在 git 中並透過路徑引用它們。
- 使用 `comment` CLI 進行透過重試佇列路由的 Slack 筆記。僅對必須送達的訊息使用 `--criticality required`。對於非 Slack 目的地，agents 直接呼叫相關的 MCP。

<a id="phase-2-drive-the-workspace"></a>

## 階段 2：驅動工作區

所有操作者動作都透過 `scripts/cli.ts` 進行。

```bash
bun <path-to-orchestrate>/scripts/cli.ts <subcommand> <workspace> [...]
```

`run` 啟動其相依性已滿足的待處理任務，等待交接，寫入交接，並重複直到無法再取得進展。結束程式碼 `0` 表示乾淨完成。結束程式碼 `100` 表示計畫中的檢查點重啟；重新執行相同的命令。其他非零程式碼表示讀取 `state.json` 與 `attention.log`。

不要分離 `run`。指令碼是狀態、交接、Slack 映象、重試佇列清空與 Andon 輪詢的心跳。當它結束時，呼叫 `tree`。如果任何任務仍為 `pending` 或 `running`，請再次執行迴圈。

`state.json` 是權威來源。使用 `tree`、`list` 與 `status` 進行檢查。

<a id="comments"></a>

## 評論

`comment` CLI 僅限 Slack 使用，且永不發布 kickoff。傳遞 `--task <name>` 以驗證任務上下文並解析執行執行緒，或明確傳遞 `--thread-ts <ts>`。

範例：

```bash
bun cli.ts comment "worker-one is blocked on auth" --task worker-one --workspace .orchestrate/root
bun cli.ts comment "no-repro on the upstream report; need a Linear ticket filed before retrying" --thread-ts 1714500000.000100 --criticality required --workspace .orchestrate/root
```

`--workspace` 與 `--task` 搭配使用時為必填，且用於非操作者的檔案上傳。執行外的操作者透過設定為 `0600` 的由目前使用者擁有的 `~/.orchestrate/operator-mode` 檔案來啟用操作者模式。假設 workers 無法寫入操作者的 OS 主目錄。

必填評論使用 `comment-retry-queue.json` 搭配現有的退避排程。

對於外部追蹤器（Linear、GitHub、on-call 分頁），agents 直接呼叫相關的 MCP。Orchestrate 不路由這些系統。

<a id="failure-recovery"></a>

## 失敗恢復

指令碼處理機械性的存活狀態。Planner 處理意義。

- 暫時性的 spawn 失敗會在 `spawnTask` 內重試。
- 重啟的迴圈透過 `recoverRunning` 重新附加到執行中的任務。
- `RunResult.status === "error"` 或阻塞的交接是 planner 的決策：重新啟動、分割、升級或丟棄。
- 當上游失敗時，下游任務保持 `pending`。修復上游並重新執行，或 `kill` 已放棄的下游工作。
- Subplanner 重新啟動會在第一次嘗試後從其自身分支複製，以便已提交的子狀態與交接得以保留。
- `maxAttempts` 限制自動啟動。僅當另一次嘗試是有意為之時，才在任務定義中增加它。
- 計畫中的檢查點重啟會在結束 `100` 前提交狀態與交接；重新執行相同的命令。

### Andon

Andon 會暫停整個樹狀結構中的新執行個體生成。根節點透過 `:rotating_light:` 輪詢 Slack 啟動訊息。子節點透過 `plan.andonStateRef` 和 `plan.andonStatePath` 使用 git 讀取快取的根節點狀態。

```bash
bun <path-to-orchestrate>/scripts/cli.ts andon raise --reason "<why>" --workspace <workspace>
bun <path-to-orchestrate>/scripts/cli.ts andon clear --workspace <workspace> [--note "<what changed>"]
```

`--reason` 在 `raise` 上是必需的。根節點透過 `:rotating_light:` 輪詢 Slack 啟動訊息，並掃描最近符合條件的 `🚨 ANDON RAISED ...: <reason>` 執行緒回覆，然後將截斷後的原因寫入 `state.andon.reason`。子節點透過 git 讀取該快取狀態，因此它們可以看到編排暫停的*原因*，而無需自行呼叫 Slack。Andon 狀態由操作員輸入，上限為 500 個字元，並與 `state.json` 的其他部分處於相同的信任圈中。

僅當繼續生成會為樹狀結構產生垃圾時，才觸發 Andon：上游輸出錯誤、驗收失敗，或無法恢復的驗證/基礎設施問題。任務本身的障礙應記錄在其交接檔案中，而非 Andon。

<a id="finding-agents"></a>

## 尋找代理

使用 `bun cli.ts tree <workspace>` 和 `bun cli.ts list <workspace>` 來追蹤系譜、狀態和代理 ID。不要依賴雲端代理的顯示標題。

`syncStateToGit` 預設為開啟，以便遠端觀察者可以從 git 讀取 `state.json` 和交接檔案。當這些產物應保留在本地時，請將其設定為 `false`。

```bash
bun cli.ts crawl <local-repo-path> <root-branch> <root-slug>
bun cli.ts kill-tree <local-repo-path> <root-branch> <root-slug> [-y] [--agent-id <id>]
```

這兩個命令都會遍歷 `.orchestrate/<rootSlug>/state.json`；每個 subplanner 列都會遞迴進入 `orch/<rootSlug>/<subplanner-name>`。
