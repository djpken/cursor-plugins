<a id="multi-phase-or-multi-pr-plan"></a>

### 多階段或多 PR 計畫

**你擁有計畫，而非程式碼。計畫是一份檢查清單，由所有者逐項執行，而操作者根據證據進行稽核。** 計畫是交付成果。不要實作。

1. 當變更僅涉及一或兩個檔案且方法明確時，請跳過計畫。說明並停止。
2. 在撰寫之前，透過原型解決開放問題。對每個問題執行 `playbooks/prototype.md`。保留分支、SHA 和截圖，以供附錄 A 使用。僅在產品或偏好決定無法由任何執行解決時，詢問操作者。提供選項（**永不阻塞於人類**原則技能）。
3. 在子代理中探索，使用 `subagent_type: "poteto-agent"` 並依照子代理章節指定明確模型（**保護上下文視窗**原則技能）。根據子代理章節，為每個子代理選擇推理等級及快速或標準速度。每個子代理返回檔案指標、慣例、測試命令和入口點。不要內嵌輸出。
4. 將以下骨架複製到計畫檔案中並填寫每個佔位符。除非操作者指定路徑，否則將檔案寫入代理儲存庫的 `docs/`。保持每個標題和每個子塊的順序。每個 PR 一個章節。一個 PR 是一個變更，並有其自己的證據（**序列可驗證單位**原則技能）。在**如何閱讀此檔案**中命名執行劇本。根據 `playbooks/autopilot-stack.md` 末尾的規則選擇 `playbooks/autopilot-full.md` 或 `playbooks/autopilot-stack.md`。常駐程式採用 `playbooks/orchestrate.md`。
5. 完整寫入 `/technical-writing`，然後 `/unslop`。正文為一種 Diátaxis 模式，即 how-to。附錄包含說明和參考。每個標題說明任務或發現。不要使用長破折號。不要在句子中間使用冒號。
6. 執行 `node pstack/skills/poteto-mode/scripts/check-plan.mjs <plan.md>` 並修正它列印的每一行（**在結構中編碼經驗**原則技能）。
7. 交回。發布計畫路徑和指令碼輸出，然後停止。執行在操作者明確指示後開始，並依照計畫命名的執行劇本。

**驗證。** 僅測試不足以作為驗證。一個 PR 僅當其單元、實時和效能檢查全部完成時才算驗證（**證明其有效**原則技能）。該句子是驗證規則。每個驗證塊以此開頭。實時塊是必須的。PR 頭部的十個通道驅動真實表面透過其控制技能，依照**群**技能，在 `swarm workers` 模型上（預設 `grok-4.7-xhigh-fast`）。每個通道是一個盒子，包含具體情境、儲存的截圖和其成功條件。一個通道是**對 trunk 的迴歸通道**。它在 trunk 和 head 上執行相同的關鍵情境。如果 trunk 沒有該功能，該通道記錄該事實，並限制 diff 新增的行為以及使用者等待的終態，而不是創造 trunk 結果。效能門是雙面的。trunk 和 head 必須都產生指定的指標。如果 trunk 缺少功能，也需隔離 diff 新增的工作，並設定該工作的絕對預算以及使用者等待的端到端狀態。不要聲稱不同情境之間的比率。效能塊命名指標、交錯探針、先測量的 trunk 基準，以及包含失敗數的規則。改變互動的 PR 需要審核門。操作者在聊天中審核，包含截圖和影片，在合併之前。不改變互動的 PR 寫 `**Review gate.** None. <PR id> is not review-gated.`，並在其下不寫任何盒子。

**控制技能。** 根據表面選擇。瀏覽器、Electron 和網頁 UI 使用 `control-ui` 從 `cursor-team-kit`。CLI 和 TUI 使用 `control-cli` 從 `cursor-team-kit`。原生移動使用儲存庫中存在的模擬器驅動技能。一個 PR 涉及兩個表面則在兩者上都有通道。沒有控制技能的表面是附錄 C 中的風險，其實時塊仍需命名每個通道如何驅動它。

````markdown
# <Program> plan

<Under ten lines. What changes, for whom, the rule the program enforces, and the PR ids in order.>

## How to read this

One box is one unit of work. Every box names the evidence that checks it. A nested box is a sub-step of the box above it. Check a box only when its evidence exists, a file, a log line, a screenshot, a test run, or a SHA. The body is a how-to. The appendices explain and record.

The program runs `pstack/skills/poteto-mode/playbooks/<execution playbook>.md`. <Who merges, and which PR ids are the operator's items that stop at merge-ready.>

Tests alone are not sufficient verification. A PR is verified only when its unit, live, and perf boxes are all checked.

## Program checklist

### Arm the program

- [ ] State the protocol and this plan to the operator, then stop. Start execution only on the operator's explicit go.
- [ ] Read these from trunk at program start. Re-read them at every tick.
  - [ ] `git show origin/main:pstack/skills/poteto-mode/playbooks/<execution playbook>.md`
  - [ ] `git show origin/main:pstack/skills/swarm/SKILL.md`
  - [ ] `git show origin/main:<control skill path>`
  - [ ] `git show origin/main:pstack/skills/poteto-mode/playbooks/opening-a-pr.md`
  - [ ] `git show origin/main:pstack/skills/<each other leaf skill the program uses>`
- [ ] On the operator's go, arm the audit tick as `/loop 1h` with the tick prompt below. Never leave the cadence to memory.
- [ ] Use this tick prompt, verbatim. "Re-read the execution playbook from trunk. Audit the operation against it and fix drift in this tick. Probe every active lane and judge progress by side effects only. Stand down a stuck lane and dispatch its replacement now. Then post a short status message to the operator in chat only when the audit found a tracked change that no earlier status message reported, such as a PR opened, a code-ready head, a round launched or closed, a verdict, a merge, a stuck agent and the action taken, a blocker added or cleared, or a decision only the operator can make. Name every such change and nothing else. Do not repeat a table, the merged list, or an unchanged blocker. If the audit found none, end the turn with no reply text. Either way, log this tick's row in your decision trail. The row names the items reported, or none."
- [ ] On the operator's hold or stand-down, send every owner a zero-writes order at once.

### Spawn owners

- [ ] Spawn one owner per PR with the full lifecycle the execution playbook names.
- [ ] Follow this dependency graph. Start dependent work only after its parent merges, or base it on the parent branch when the execution playbook stacks.
  - [ ] <PR id> and <PR id> are independent and first. Both branch from `main`.
  - [ ] <PR id> after <PR id>.
- [ ] Hold the file boundaries. <PR id or class> touches only `<glob>`.
- [ ] Hold the review gate. <PR ids> change an interaction. They wait for the operator's review in chat with screenshots and a video before merge.

### PR mechanics, for every PR

- [ ] Resolve the forge once. Default to `gh`; if `command -v origin` succeeds and Origin can resolve the repository, use `origin pr` for every PR operation. Record any fallback to `gh`. Never require `gt`.
- [ ] Open the PR ready, never draft, per **Opening a PR**. Use the run's built-in PR tool when it has one, else `origin pr create --status open --base <base-branch>` or `gh pr create --base <base-branch>` according to the resolved forge. A stack child targets its parent branch.
- [ ] Run the repo's lint and typecheck once before the PR-facing push. Push with hooks on.
- [ ] Run `/deslop` before each commit and `/no-comments` before review.
- [ ] Triage every Bugbot and security-reviewer comment per `../references/bugbot-triage.md`.
- [ ] Rebase onto current trunk before the code-ready report and babysit. Keep that merge base in fix rounds. Rebase again only at merge prep, on a `git merge-tree` conflict with trunk, or on a CI failure that comes from a change on trunk.

### Verdict and merge, for every PR

- [ ] A full swarm runs once, at the code-ready head. Another full swarm runs only for new behavior, a redesign, or a large new diff. A rebase, a merge-conflict resolution, or a minor fix gets a single verifier at the new head. Run each full swarm per `pstack/skills/swarm/SKILL.md`. One gates lane. The ten live lanes from the PR's **Verify, live** block. The perf lane from its **Verify, perf** block. Two or more audit lanes, each with its own focus, that read the diff and the receipts and distrust the PR body. The root audits the receipts in the merge-ready report before the verdict.
- [ ] Clean only when every lane is `PASS`. Findings go back to the owner, including a defect that a lane filed as a note. A new head gets a fresh verdict, except for results that stay valid under the patch-id rule in `playbooks/shipping.md`.
- [ ] <The merge or append rule from the execution playbook, with the patch-id rule from `playbooks/shipping.md`.>

### Boot recipe, for every live lane

Each live lane runs on its own cloud VM at the PR head. Drive through `control-ui` or `control-cli` from `cursor-team-kit`.

- [ ] `git fetch origin <head-branch> && git checkout <head SHA>`.
- [ ] <Start the backend and the surface. Wait for ready.>
- [ ] <Deliver input only through the control skill's commands. Name the read-only diagnostics.>
- [ ] Save every screenshot to `/tmp/swarm-<pr-id>/worker-<n>/<slug>.png` and return the paths with the report.

## <Task as a verb phrase> (<PR id>)

**Depends on.** <PR id, or None.>

**Files.**

- [ ] Edit `<path>`.
- [ ] Create `<path>`.
- [ ] Delete `<path>`.

**Build.**

- [ ] <One change. Name the symbol and the file.>

**You see.**

- [ ] <One observable result, with the exact log line or screen state.>

**Verify, unit.** Tests alone are not sufficient verification. A PR is verified only when its unit, live, and perf boxes are all checked.

- [ ] <Test file and the case it gains.> Run `<command>`.

**Verify, live.** Tests alone are not sufficient verification. A PR is verified only when its unit, live, and perf boxes are all checked. Ten lanes on `<swarm workers model>` at the PR head, per the boot recipe.

- [ ] Lane 1. Regression lane against trunk. Run <the same load-bearing scenario> at trunk and head. If trunk lacks the feature, record that and gate <the behavior the diff adds plus the end state the user waits for>. Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 2. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 3. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 4. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 5. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 6. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 7. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 8. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 9. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 10. <Scenario.> Save `<slug>.png`. Pass when <predicate>.

**Verify, perf.** Tests alone are not sufficient verification. A PR is verified only when its unit, live, and perf boxes are all checked.

- [ ] Metric. <What is measured at both trunk and head. If trunk lacks the feature, also name the diff-added work and the end-to-end state the user waits for.>
- [ ] Probe. <The command or procedure, run at trunk and at the head, interleaved. Both sides must produce the metric.>
- [ ] Baseline. Record the trunk <value> first.
- [ ] Rule. <Head against trunk, with the number that fails. If the scenarios differ, add absolute budgets for the diff-added work and the user-visible end state instead of an invalid ratio.>

**Review gate.** The operator reviews before merge.

- [ ] Copy lane <n> screenshots into `<media path>/<pr-id>-review-<slug>.png`.
- [ ] Record a 30 to 60 second video of the change on a lane VM. Save it as `<media path>/<pr-id>-review.mp4`.
- [ ] Post the screenshots and the video in chat. Stop at merge-ready. Wait for the operator's click.

**Merge.**

- [ ] Root's clean verdict at the exact head SHA.
- [ ] Bugbot triage done.
- [ ] Rebased onto current trunk after the verdict, patch-id unchanged.
- [ ] <The owner squash-merges its own PR, or the root appends it to the base-branch stack and the operator lands it bottom-up.>

## Close the program

- [ ] Every box above is checked with its evidence.
- [ ] Reply to the operator with the report the execution playbook names.

## Appendix A. Prototype evidence

<Each open question a prototype answered, with the branch, the SHA, and the artifact links. Each question that stays unproven.>

## Appendix B. Alternatives rejected

<Each approach weighed and why it lost.>

## Appendix C. Risks

<Each risk with the PR it lands in and what the owner watches.>

## Appendix D. Links and reading list

<Docs to read before editing. Which PRs get `pstack/skills/how/SKILL.md` and `pstack/skills/interrogate/SKILL.md`. The trail per `pstack/skills/show-me-your-work/SKILL.md`.>
````

**回覆：** 計畫路徑、PR ID 及其依賴和審核門集合、原型證明的內容和仍未證明的內容，以及檢查指令碼的輸出。
