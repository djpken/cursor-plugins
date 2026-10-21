<a id="handoffs"></a>

# 交接

交接是節點之間傳遞資訊的唯一方式。工作者產出交接，規劃者讀取並做出決策。沒有共用分支，沒有狀態 API，沒有跨兄弟節點的閒聊。正是這種一致性讓樹狀結構在沒有全域協調的情況下持續運作。

指令碼指示每個產生的 agent 以結構化的最終訊息結束（狀態、分支、摘要、備註、後續事項）。確切格式位於 `prompts/*.md` 的範本中。指令碼會將最終訊息逐字儲存到 `<workspace>/handoffs/<task-name>.md`，並附上可追溯性標頭。不要豐富或清理內容；規劃者需要未經過濾的工作者原話。

<a id="measurements-worker-handoff"></a>

## 測量（工作者交接）

工作者在 `## Measurements` 下自我報告；格式見 `prompts/worker.md`。當任務宣告 `measurements[]` 時，指令碼會在工作者的分支上重新執行每個命令，並將超過 10% 的數值偏差或單位不匹配標記到 `attention.log`；工作者仍會進行交接，由規劃者決定是否重新產生。撰寫細節見 `references/planner.md` → `measurements[]`。

<a id="reading-handoffs"></a>

## 讀取交接

對於每個新的 `handoffs/*.md`：

1. **Status** 不是 `success`：決定是否重試、修復，或透過後續任務釐清。
2. **Branch**：記錄它；如果另一個任務需要基於它建構，請參考它。
3. **What I did**：視為事實，但快速瀏覽是否有與你預期不符的宣告。
4. **Notes / concerns / deviations / findings / thoughts / feedback**：最豐富的章節。每個專案都可能成為新任務。工作者關於範圍或任務清晰度的回饋特別有價值：它告訴你計畫中的提示是否發揮了作用。
5. **Suggested follow-ups**：候選任務。接受、拒絕或合併。

`Status: blocked` 是單一任務的死衚衕；規劃者重試、修復或釐清，樹狀結構繼續前進。只有當持續在樹狀結構中產生任務會浪費精力時，才使用 Andon（見 `references/planner.md` → Failure recovery）。

<a id="synthetic-failure-handoffs"></a>

## 合成失敗交接

當工作者在沒有寫下自己的交接時死亡（達到上限、OOM、工具錯誤、網路中斷、未捕獲的 SDK 錯誤），指令碼會寫入 `handoffs/<task>-failure.md`，讓規劃者看到事後分析而不是沉默。然後迴圈返回退出碼 1 並進行檢查點同步，以便規劃者可以立即反應。

```markdown
<!-- orchestrate failure handoff
task: <name>
branch: <actual or placeholder branch>
agentId: bc-...
runId: run-...
failureMode: cap-hit | oom | network-drop | tool-error | unknown
terminatedAt: <iso>
-->

# <name> — failure handoff

Status: error (cloud agent terminated without writing a handoff)
Failure mode: cap-hit | oom | network-drop | tool-error | unknown
Cloud agent: bc-...
Started: <iso>
Terminated: <iso>
Duration: <ms>
Last activity: <iso> — <status text from state.json lastUpdate>
Last tool call: <name from SDK stream tail, or (unknown)>
Branch: <branch>
SDK error: <truncated error text>

## Suggested next steps
- <one bullet per option, planner picks>
```

分類器啟發式規則：
- 當持續時間為 70 至 80 分鐘且執行為終端錯誤時，使用 `cap-hit`
- 當輸出或 SDK 錯誤包含 `out of memory` / `OOMKilled` / `exit code 137` 時，使用 `oom`
- 當 SDK 錯誤匹配 `fetch failed` / `ETIMEDOUT` / `ECONN` / `socket` / `dns` / `disconnect` 時，使用 `network-drop`
- 當 SDK 錯誤提及 `tool_use_failed` 或 `tool-error` 時，使用 `tool-error`
- 其他情況使用 `unknown`

依模式的預設重試策略：
- `cap-hit` / `oom`：以較小範圍重試
- `network-drop`：原樣重試（視為暫時性問題）
- `tool-error`：使用不同的 `model` 重試
- `unknown`：原樣重試一次，然後放棄

在同一任務上重試 2 次後，除非你有具體證據表明下一次重試會成功，否則優先選擇放棄（從 `plan.json` 中移除，並繞過它重新規劃），而不是進行第 3 次嘗試。

<a id="finished-without-handoff-sidecar"></a>

## 完成但無交接的 sidecar

當執行以 `status=finished` 結束，但主體沒有 `## Status` 標題時，指令碼會在原始 `<task>.md` 旁邊寫入 `handoffs/<task>-finished-no-handoff.md`。將原始主體視為工作者的意圖；如果看起來可恢復則重試，否則放棄。

<a id="upstream-handoffs-in-downstream-prompts"></a>

## 下游提示中的上游交接

工作者生活在兄弟分支上，**無法在執行時讀取彼此的分支**。如果下游任務依賴上游任務的輸出，規劃者必須轉達它。

指令碼處理轉達。當產生一個其 `dependsOn` 包含已交接任務的任務時，它會將每個上游交接主體貼到下游提示中。在產生之前使用 `bun cli.ts prompt <workspace> <task>` 預覽。

後果：

- `dependsOn` 具有語義意義，不僅僅是排程閘門。只要下游任務需要上游發現，就使用它，即使 Git 層級的順序沒有嚴格要求。
- 未宣告的 `dependsOn` + 需要的上游上下文 = 工作者猜測。
- 長扇入會膨脹提示大小。如果變得笨重，請將摘要下推到上游交接中，而不是膨脹下游提示。
- 交接逐字渲染：草率的 `## What I did` 章節會汙染每個下游任務。格式是共用上下文公地；請尊重它。

<a id="producing-your-own-handoff-subplanner"></a>

## 產出你自己的交接（subplanner）

subplanner 的最終訊息是給其父級的交接。向上彙總子項；不要轉發原始子項交接。父級有更多全域上下文，但較少本地細節。

| 欄位 | 規則 |
|-------|------|
| `Status` | 只有當所有驗收標準都滿足時才使用 `success`。如果任何子項是部分的，使用 `partial`。如果任何硬性阻擋者仍然存在，使用 `blocked`。 |
| `Branch` | 你正在上交的實際交付物分支，而不是你的簿記分支（`orch/<parent-rootSlug>/<your-name>`）。通常它是你的子樹中最後一個合併任務的輸出。在孤兒恢復後，或如果整合工作者合併到子分支中，交付物可能是該子分支。下游任務基於你在此處命名的內容建構，因此請明確命名實際交付物。 |
| `What my subtree did` | 每個有意義的切片一個專案，而不是每個子項一個。父級關心工作，而不是你的組織架構。 |
| `Notes / concerns` | 提出任何兄弟子樹或根可能衝突的內容。對真實風險的沉默比冗餘的瑣事更糟。 |
| `Suggested follow-ups` | 針對你父級範圍的任務，而不是你自己的。 |

<a id="verifier-handoffs"></a>

## 驗證者交接

驗證者的最終訊息是針對單一目標任務驗收標準的裁決。它不是實作摘要。

```markdown
## Verification
<one of: live-ui-verified | unit-test-verified | type-check-only | verifier-blocked | verifier-failed>

## Target
`<target-name>` on branch `<target-branch>`

## Branch
`<verifier-branch>` (or "(no branch)" if you committed nothing)

## Execution
- <command run> → <outcome>
- <test suite> → <pass/fail counts>
- <manual repro step> → <observed behavior>
(list every meaningful thing you actually ran; this section is what distinguishes a real verification from pattern-matching)

## Findings
Per acceptance criterion:
- [x] <criterion text>: <evidence> (met | not met | n/a)
Other findings (severity-ordered):
- (high) <finding>: evidence
- (med) <finding>: evidence
- (low) <finding>: evidence

## Notes & suggestions
- <anything the planner should know: flaky tests, adjacent issues noticed, suggestions for follow-up tasks>
```

`## Verification` 由指令碼解析並持久化在 *目標* 任務的狀態行上（`state.json` 中的 `tasks[].verification`），以便執行後分類器按品質對「已修復並驗證」進行分組，而不是將每個非失敗視為等同。權威定義位於 `prompts/verifier.md`。簡短版本：

| 值 | 含義 | 規劃者回應 |
|---|---|---|
| `live-ui-verified` | 驗證者即時重現了 bug 並確認修復移除了它。 | 信任為已發布；除非出現其他發現，否則無需後續。 |
| `unit-test-verified` | 目標測試執行變更的程式碼路徑並透過。 | 對非 UI bug 可接受。對 UI bug，一旦環境允許，請跟進一次 `live-ui-verified` 透過。 |
| `type-check-only` | 只有型別檢查 / 建置透過。 | 弱；僅對僅型別變更足夠。任何行為變更都需要更強的驗證者。 |
| `verifier-blocked` | 驗證者遇到環境失敗（Docker 速率限制、埠、缺少憑證）。 | 修復可能正確但未證明。一旦環境健康，重新產生驗證者，或升級。不要計為已驗證。 |
| `verifier-failed` | Verifier 已執行，但修正並未解決該 bug。 | 後續修正任務，不會自動重新啟動。 |

Workers 和 subplanners 也可以寫入 `## Verification` 行，以自我報告其自身的證據。後續的 verifier 會覆蓋同一目標列上的該自我報告。

該指令碼也接受舊版的 `## Verdict pass | fail | inconclusive` 格式，並將其遷移至最保守的新值：`pass` → `type-check-only`，`fail` → `verifier-failed`，`inconclusive` → `verifier-blocked`。新的 verifier 提示會直接輸出 `## Verification`。

在 `plan.json` 中明確發布 verifiers：

```json
{
  "name": "frontend-toggle",
  "type": "worker",
  "scopedGoal": "Add a Settings → Appearance toggle that persists `editor.experimentalDarkMode` through the existing settings service.",
  "pathsAllowed": ["packages/ui/src/settings/**"],
  "acceptance": [
    "Toggle renders in Settings → Appearance",
    "Toggling on persists `editor.experimentalDarkMode=true`",
    "Reloading Settings shows the persisted toggle state"
  ],
  "verify": "## Setup\n- Start the Settings UI dev environment with the existing repo workflow.\n\n## Automated\n- Run the focused Settings UI test that covers Appearance settings persistence.\n\n## Manual\n- Open Settings → Appearance, toggle dark mode on, reload Settings, and confirm the toggle remains on.\n\n## Gotchas\n- Make sure the test account starts with no existing `editor.experimentalDarkMode` override."
},
{
  "name": "verify-frontend-toggle",
  "type": "verifier",
  "verifies": "frontend-toggle",
  "scopedGoal": "Verify the Settings → Appearance toggle works against every acceptance criterion by running the UI test or manually exercising the screen.",
  "acceptance": ["Verification section includes execution evidence for all frontend-toggle acceptance criteria"]
}
```

<a id="merges-are-tasks"></a>

## 合併是任務

由於 planners 不撰寫程式碼，合併是透過任務進行的。發布一個 worker，其 `scopedGoal` 同時指定兩個分支和解析策略，並以 `dependsOn` 同時控制兩個 sibling：

```json
{
  "name": "merge-frontend-and-theme",
  "type": "worker",
  "scopedGoal": "Merge `orch/dark-mode/frontend-toggle` into the current branch. On conflict in `packages/ui/src/settings/Settings.tsx`, prefer frontend-toggle's hook wiring. After merge, verify `pnpm -w typecheck` passes.",
  "startingRef": "orch/dark-mode/theme-system",
  "dependsOn": ["frontend-toggle", "theme-system"],
  "acceptance": ["Merge committed with both parents in history", "pnpm -w typecheck passes"]
}
```

其 handoff 會告知你合併是否成功、衝突是否明顯，以及驗收是否透過。請將其視為任何其他 handoff 一樣處理。

<a id="continuous-motion"></a>

## 持續運作

只要子任務可能仍會產生 handoff，planner 就未嚴格意義上「完成」。如果在你已總結或向上傳送 handoff 之後收到一個：

- 閱讀它。
- 如果它改變了你的結論，請說明：「又有一個 worker 剛回來，帶來了 X，正在修訂」。
- 如有需要，發布後續任務。
- 產生一份新的 handoff / 摘要。

僅在你決定停止發布，且所有進行中的任務都已終止時，才硬性停止。
