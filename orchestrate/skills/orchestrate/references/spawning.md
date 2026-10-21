<a id="spawning-tasks"></a>

# 建立任務

`plan.json` 專案與指令碼建立的雲端 agent 之間的合約。機制（驗證、`CursorAgentError` 與 `RunResult.status === "error"`）位於 `cursor-sdk/SKILL.md`。請先閱讀該檔案。

<a id="branch-naming"></a>

## 分支命名

雲端 agent 擁有其工作分支。`state.json` 以確定性的佔位符（`orch/<rootSlug>/<task-name>`）開始，以便建立前的狀態可讀，然後在交接後將其替換為 `Run.git.branches[].branch` 報告的分支。強制使用 Kebab-case（`orchestrate.ts` 中的 `TASK_NAME_RE`），以便任務名稱仍能用於檔案系統路徑和提示文字，而無需轉義。沒有自動管理的整合分支。分支獨立存在，直到合併任務將它們整合（請參閱 `handoffs.md` →「合併是任務」）。

不要要求 worker 建立或重新命名分支以匹配佔位符。如果下游任務需要上游任務的程式碼，請依賴該上游任務，以便指令碼可以等待交接並使用記錄的實際分支。

<a id="agent-naming"></a>

## Agent 命名

在 `Agent.create` 時，雲端 agent 會被賦予一個 `name`，以便 Cursor agent 列表（`cursor.com/agents`，IDE agent 列表）將單次 orchestrate 執行分組在一起，並在數十個並行子任務中保持可讀性。

| 建立位置 | Agent 名稱 |
|------------|------------|
| 根規劃器（`cli.ts kickoff`） | `<first line of goal, up to 100 chars>` |
| Worker / 子規劃器 / 驗證器（`spawnTask`） | 回顯任務的分支：`<rootSlug>/<taskName>`，不含 `orch/` 字首 |
| 模型目錄探查（`probe-models`） | `probe: <modelSlug>` |

伺服器將名稱限制為 100 個字元，並拒絕空值或僅包含空白值的輸入；輔助函式會處理這兩種情況。當省略 `name` 時，雲端後端會從第一個提示自動生成一個名稱，因此這純粹是可讀性的升級，移除名稱不會改變行為。

<a id="starting-refs-and-dependencies"></a>

## 起始引用與依賴

| 欄位 | 控制項 | 預設值 | 搭配使用 |
|-------|----------|---------|-----------|
| `startingRef` | 雲端 agent 從哪個分支複製 | `plan.baseBranch` | 依賴其他任務的工作時，搭配 `dependsOn` |
| `dependsOn` | 任務何時允許建立 | `[]` | 指令碼在交接後從 `Run.git` 記錄上游分支 |

沒有 `dependsOn` 的 `startingRef` 提供一個時間點快照：即建立時該分支上存在的任何 commit，可能為空。除非您確實意指「從目前的頂端開始，即使為空」，否則請搭配使用它們。

驗證器預設將 `startingRef` 設為其目標的分支，並自動在 `dependsOn` 中包含目標；規劃器不需要明確設定這兩者。

任何任務都可以設定 `verify`：一個 Markdown 格式的計畫（設定、自動化、手動、注意事項）。Worker 將其視為目標規格；驗證器繼承目標的 `verify` 作為其配方。

<a id="spawn-design-decisions"></a>

## 建立設計決策

指令碼呼叫 `Agent.create` 時帶有兩個刻意設定的預設值：

- **PR 是每個任務選擇性啟用的。** 雲端 agent 建立呼叫中的 `autoCreatePR` 會反映任務的 `openPR` 旗標（預設為 false）。伺服器端的 `cloud_agent_pr_control` 閘門目前使該旗標無效，因此真正的機制是 worker 提示：當 `openPR: true` 時，worker 會收到指示，在推送後透過 ManagePullRequest 工具對 `plan.prBase ?? plan.baseBranch` 開啟一個草稿 PR。子規劃器和驗證器從不開啟 PR；它們會交接給其父級。任務驅動的 PR 為規劃器提供了一個具體保證：此任務將作為其自身的 pull request 發布。
- **PR 基底與 worker 起始引用。** `plan.baseBranch` 是未指定自身起始引用的 worker 的起始引用。`plan.prBase`（選填，預設為 `baseBranch`）是 openPR worker 開啟 PR 的目標位置。當您希望 worker 從規劃器的分支繼承規劃器端的設定，但仍對 `main` 開啟 PR 時，請將兩者分開（這樣每個 worker PR 都可以合併，而無需先落地規劃器的分支）。若要使用 worker PR 堆疊在規劃器分支上的經典模式，請保持 `prBase` 未設定。
- **沒有共享的整合分支。** 在合併任務將它們整合之前，每個任務都是其自身的島嶼。自動管理的整合分支會將規劃器層級的編碼決策偷偷帶入基礎設施，違反核心原則 #1。

<a id="task-prompt-contract"></a>

## 任務提示合約

指令碼從 `scopedGoal`、`pathsAllowed`、`pathsForbidden`、`acceptance`、`startingRef`、`type` 和 `openPR` 渲染提示。如果提示不符合您的意圖，請修正計畫專案。不要修補提示範本。每個建立的提示都會告訴 agent：

- 它是隔離的。不與其他 agent 通訊。
- Commit 到目前的雲端 agent 分支並推送。不要重新命名分支、合併或 rebase。僅當任務設定 `openPR: true` 時才開啟草稿 PR（僅限 worker；子規劃器和驗證器從不開啟 PR）。
- 最終訊息是 `handoffs.md` 中結構的交接。這是規劃器唯一閱讀的內容。

子規劃器的提示也以 `/orchestrate` 開始，以便在 agent 啟動時自動載入 skill。

Worker 無法在執行期間提出澄清問題。規格不足的 `scopedGoal` 會導致靜態漂移。請將每個任務撰寫得彷彿您永遠沒有機會再次引導它。

雲端 agent VM 可能會作為提示注入防禦而刪減環境變數值，因此不要依賴環境變數來儲存多個 agent 必須共享的資料。請改用規劃器撰寫的產物模式：將檔案 commit 到基底分支，並在 `scopedGoal` 中透過路徑引用它，以便複製時能獲取它。永遠不要將憑證貼到 `scopedGoal` 中；它會被傳送到模型供應商，並在啟用狀態同步時可能進入 git 歷史。

<a id="slack-visibility"></a>

## Slack 可見性

當設定 `SLACK_BOT_TOKEN` 時，Slack 流量由指令碼擁有。Agent 不驅動生命週期。指令碼將啟動執行緒發布到 `plan.slackChannel`，在 `plan.slackKickoffRef` 中記錄結果，在該執行緒中映象任務狀態訊息，並讀取啟動訊息上的 `:rotating_light:` 以進行 Andon。

建立提示仍包含一個 Slack 區塊，因為 worker 可能需要留下備註：

- 當沉默會隱藏有用上下文時，`comment "<note>" --thread-ts <run-thread-ts> --workspace <workspace>` 會透過重試佇列發布備註。也接受帶有 `--workspace` 的 `--task <name>`；CLI 會驗證任務並在執行執行緒中發布。
- 對於 Linear、GitHub 或其他外部系統，請直接呼叫相關的 MCP。Orchestrate 的 `comment` CLI 僅限 Slack。

如果 Slack 評論失敗，請繼續工作並在交接中說明發生了什麼。磁碟交接對於下游提示組裝仍然是權威的。

<a id="tracking--recovery"></a>

## 追蹤與恢復

每次成功建立後，指令碼會將 `agentId`、`runId` 和 `parentAgentId` 持久化到 `state.json`，以便稍後的重新執行可以透過 `Agent.getRun` 重新附加，並從磁碟讀取系譜。重新啟動時，具有部分識別資訊（恰好是 `agentId` / `runId` 之一）的列會被標記為 `error` 並附上說明備註。請重新命名並重新建立，或修剪。

每次交接後，指令碼會協調相依驗證者的 `startingRef`。實際的工作者分支取自交接主體中的 `## Branch` 行。SDK 會將工作者執行時的 `Run.git.branches[].branch` 留空，因此主體內容為權威來源。任何 `verifies` 指向剛完成交接的任務，且 `startingRef` 仍為 `orch/<rootSlug>/<task>` 佔位符的驗證者，都會更新為該實際分支。由規劃者撰寫的 `startingRef` 覆寫具有優先權；每次傳播都會記錄到 `attention.log`。載入時也會掃描已交接的列，以便從磁碟恢復的狀態在下次生成前收斂。

<a id="lineage"></a>

## 血緣

每個 `state.tasks[]` 列的 `parentAgentId` 是生成規劃者在生成時來自 `plan.selfAgentId` 的雲端 agent id。`kill-tree --agent-id` 會沿著這些父級連結向下遍歷。如果規劃者從未設定 `selfAgentId`，子項將獲得 `parentAgentId: null`，且該子樹在範圍限定的終止操作中會被略過；省略 `--agent-id` 即可取消整棵樹。

生成模板使用子項的 id 作為 `{{selfAgentId}}`，並使用父項的 id 作為 `{{parentAgentId}}`。來自 `Agent.create` 的 id 在 `send()` 之前有效；雲端客戶端會在建立時傳送該 id，並拒絕不匹配的伺服器回應。
