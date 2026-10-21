# pstack

我是 [poteto](https://x.com/poteto)。我既不是總統也不是執行長，但我曾在 Meta、Netflix 和 Cursor 處理過數百萬行程式碼。我同時也是 react 核心團隊的成員，協助建置與維護 react compiler。

越來越多人認為 AI 產出了太多低品質的程式碼。我同意。我不想像一支由二十位低品質程式碼生產者組成的團隊那樣發布產品。沒有品質的產出速度並非我追求的目標。如果你想快速推進，請先深入鑽研。

**pstack 是我的答案。** 這些是我每天在 Cursor 發布高品質程式碼時使用的相同技巧。這讓 cursor 變成一支真正的工程團隊。目標不是最大化 LOC，事實上恰恰相反。pstack 幫助你寫更少但品質更高的程式碼。

**pstack 讓你無畏地並行處理。** 當你能深入鑽研單一 agent 並信任它寫出良好且可驗證的程式碼時，你就能真正有信心地進行並行化。使用 `poteto-mode` 啟動多個 agent，並信任它們會將嚴謹的工程原則應用於其工作中。

**cursor 讓你兼得所有優勢。** 每個前沿模型都有其強項與弱項。使用 pstack 搭配任何模型。事實上，我的許多技巧都使用多模型工作流程，以利用每個模型的獨特優勢。

fork 它。改進它。讓它成為你的。歡迎提交 PR！

<a id="install"></a>

## 安裝

```bash
/add-plugin pstack
```

在 claude code 中：

```bash
/plugin marketplace add cursor/plugins
/plugin install pstack@cursor-plugins
```

claude code 會為 plugin 指令和 agents 加上字首，因此 `/poteto-mode` 是 `/pstack:poteto-mode`。[claude code adapter](./docs/harness/claude-code.md) 會對映其他所有 cursor 名稱。在 claude code 中，`/pstack:setup-pstack` 會將你的模型選擇寫入 `~/.claude/rules/pstack-models.md`，而需要 cursor 專屬功能（例如 bugbot 或 automations）的步驟會以一行註記跳過。

claude code 會從 [`harness/pstack/`](https://github.com/cursor/plugins/tree/main/harness/pstack) 安裝 pstack 的生成副本，包括此 readme 和指南，位於儲存庫根目錄。在你修改 pstack 後，從儲存庫根目錄執行 `node scripts/generate-pstack-harness-trees.mjs` 並提交結果。如果生成的副本過期，CI 會失敗。

<a id="get-started"></a>

## 開始使用

兩個步驟：

1. 執行 [`/setup-pstack`](./skills/setup-pstack/SKILL.md)，選擇推理預算，並選擇你想要的模型。
2. 在任何需要嚴謹性的工作中，隨時使用 [`/poteto-mode`](./skills/poteto-mode/SKILL.md)。

新來者？[pstack guide](./docs/guide/README.md) 會引導你完成第一個實際任務，從設定和提示到驗證和夜間執行。卡住了，或不確定哪個技巧適合？詢問 [`/poteto-help`](./skills/poteto-help/SKILL.md)。

就這樣。其他技巧是情境性的，mode 技巧會視需要為你使用它們。預設情況下，mode 會根據模型強項分配工作：程式碼委派（功能、重構、bug 修復、效能、hillclimb）交給 grok，而最困難的變更、散文和判斷交給 opus 5.5。預設面板是 opus 5.5 / grok。[`/setup-pstack`](./skills/setup-pstack/SKILL.md) 可以更改其中任何設定。

<a id="usage"></a>

## 用法

在任務開始時使用 [`/poteto-mode`](./skills/poteto-mode/SKILL.md)。它會讀取你的請求，從一組 playbook 中選擇，並在步驟需要時執行其他技巧。

在輸入任務之前，從 `/` 選單中選擇 `/poteto-mode`，以便 cursor 將該技巧附加到訊息。

<a id="just-use-poteto-mode"></a>

### 只需使用 [`/poteto-mode`](./skills/poteto-mode/SKILL.md)

這個技巧是主要快捷方式。當我需要 agent 執行嚴謹的工程工作時，我會使用它。它附有二十三個 playbook：

```
/poteto-mode this pr has a subtle bug where the scroll drifts every 750ms even when idle. repro
first, then fix and verify.
```

```
/poteto-mode i'm going to bed. land the stack even if ci flakes. i want everything merged by
morning.
```

<details>
<summary>二十三個 playbook</summary>

| playbook | 用途 |
|---|---|
| [investigation](./skills/poteto-mode/playbooks/investigation.md) | 唯讀問題。x 如何運作，y 為何這樣建置，我們是否確定。 |
| [bug fix](./skills/poteto-mode/playbooks/bug-fix.md) | 重現缺陷，找出根本原因，並使用 runtime 證據進行修復。 |
| [perf](./skills/poteto-mode/playbooks/perf-issue.md) | 追蹤測量到的延遲並針對基準進行改進。 |
| [hillclimb](./skills/poteto-mode/playbooks/hillclimb.md) | 針對目標指標進行持續、科學性的改進，迴圈假設並進行前後測量，每次接受的改進對應一個 commit。 |
| [runtime forensics](./skills/poteto-mode/playbooks/runtime-forensics.md) | 從儀器資料診斷即時症狀（洩漏、閒置 CPU 旋轉、故障）。 |
| [trace forensics](./skills/poteto-mode/playbooks/trace-forensics.md) | 診斷已捕獲的剖析產物（cpuprofile、trace、spindump、heap snapshot）。 |
| [feature](./skills/poteto-mode/playbooks/feature.md) | 新或變更的行為，從命名的資料結構建置。 |
| [refactoring](./skills/poteto-mode/playbooks/refactoring.md) | 保留行為的結構或形狀變更。 |
| [prototype](./skills/poteto-mode/playbooks/prototype.md) | 一次性草圖，以低成本做出設計或行為決策，或透過觀察來解決經驗性分歧。 |
| [visual parity](./skills/poteto-mode/playbooks/visual-parity.md) | 兩個實作之間畫素級精確的 UI 等效性。 |
| [authoring a skill](./skills/poteto-mode/playbooks/authoring-a-skill.md) | 撰寫或編輯 SKILL.md。 |
| [eval](./skills/poteto-mode/playbooks/eval.md) | 盲測技巧或提示變更如何影響 agent 行為。 |
| [babysit](./skills/poteto-mode/playbooks/babysit.md) | 推動 PR 或 stack 達到可合併狀態：衝突、審查執行緒、CI。 |
| [shipping](./skills/poteto-mode/playbooks/shipping.md) | 獨立驗證綠色 stack，然後預設透過 github 或可用時透過 origin，從下至上落地連續的已驗證執行。 |
| [autonomous run](./skills/poteto-mode/playbooks/autonomous-run.md) | 不中斷地推動長任務至完成。 |
| [orchestrate](./skills/poteto-mode/playbooks/orchestrate.md) | 將常駐專案交給單一協調者聊天：多天、多個堆疊 PR、子 agent 群組。 |
| [autopilot-full](./skills/poteto-mode/playbooks/autopilot-full.md) | 以每個 PR 一位擁有者的方式執行獨立 PR 至合併。群組驗證每個程式碼就緒的 head，且對 patch 的後續變更都會進行新的檢查。 |
| [autopilot-stack](./skills/poteto-mode/playbooks/autopilot-stack.md) | 建置並驗證一個 linear base-branch stack 供操作者審查和落地。 |
| [session pickup](./skills/poteto-mode/playbooks/session-pickup.md) | 恢復或接手先前 agent 正在進行的工作。 |
| [pause safely](./skills/poteto-mode/playbooks/pause-safely.md) | 乾淨地暫停進行中工作，以便稍後恢復。 |
| [multi-phase plan](./skills/poteto-mode/playbooks/multi-phase-plan.md) | 跨越階段或堆疊 PR 的工作。 |
| [worktree cleanup](./skills/poteto-mode/playbooks/worktree-cleanup.md) | 透過修剪已合併或放棄的 worktree 和過期的 ios 模擬器來回收磁碟空間，並受安全門控保護。 |
| [opening a pr](./skills/poteto-mode/playbooks/opening-a-pr.md) | 從小型有序 commit 開啟就緒 PR，使用慣用 commit 標題和簡報風格的主體。在每個其他 playbook 的結尾呼叫。 |

</details>



呼叫時它會：

1. 將你的任務匹配到 [playbook](./skills/poteto-mode/playbooks/) 並開啟一個 todo 清單，其前幾項是其步驟，逐字複製。
2. 當步驟觸發時，路由到其他技巧。
3. 撰寫針對消費者和維護者框架的非低品質回覆。

完整的規則和 playbook 位於 [`skills/poteto-mode/SKILL.md`](./skills/poteto-mode/SKILL.md)。

要在多輪對話中保持 [`/poteto-mode`](./skills/poteto-mode/SKILL.md) 開啟，請從 `/` 選單中選擇它，並按 option+enter (mac) 或 alt+enter (windows) 而不是 enter。這會使其成為 [custom mode](https://cursor.com/docs/skills#using-a-skill-as-a-custom-mode)，cursor 在 agents 視窗和 cli 中提供此功能。它會在每一輪對話中保持在上下文中，直到你退出該模式。普通 enter 僅將其附加到單則訊息。

[`/poteto-mode`](./skills/poteto-mode/SKILL.md) 與 cursor 的 `/loop` 指令搭配使用效果極佳。您可以讓 cursor 連續工作數小時，同時不犧牲嚴謹性。

## skills

[`/poteto-mode`](./skills/poteto-mode/SKILL.md) 會在步驟需要時為您執行其中大部分（`how`、`why`、`architect`、`arena`、`swarm`、`interrogate`、`unslop`、`no-comments`、`technical-writing`、`tdd`，以及相關原則）。當您希望直接執行某項功能時，請參考下表：

```
/how do we cancel runs? do we have an n+1 when we look up every run to cancel?
```

```
/interrogate review this pr.
```

<details>
<summary>all skills</summary>

| skill | 適用時機 |
|---|---|
| [`/poteto-mode`](./skills/poteto-mode/SKILL.md) | 任何非平凡任務的預設進入點。 |
| [`/poteto-help`](./skills/poteto-help/SKILL.md) | 您是 pstack 的新手，或不確定哪項 skill、playbook 或原則適合。它會找出您嘗試執行的內容，回答該部分問題，並提供一段供您輸入的提示詞。僅在您輸入 `/poteto-help` 時執行。 |
| [`/in-your-own-words`](./skills/in-your-own-words/SKILL.md) | 您希望 agent 在開始前重述您的目標與問題。僅在您輸入 `/in-your-own-words` 時執行。 |
| [`/sharpen`](./skills/sharpen/SKILL.md) | 在傳送提示詞之前，您希望將其深化而非擴大：頂級專業人士會執行您未要求的事項、他們會使用的原始素材、其方法與工具，以及確保結果正確的外部檢查。它會傳回重寫後的提示詞，並保留您的選擇空間。它不會執行任務。僅在您輸入 `/sharpen` 時執行。 |
| [`/how`](./skills/how/SKILL.md) | 您希望瞭解某個子系統如何運作。 |
| [`/why`](./skills/why/SKILL.md) | 您希望瞭解某項功能為何以此方式建置。它會在執行時偵測可用的 MCP，並並行查詢每個證據類別（版本控制、問題追蹤器、長篇檔案、即時聊天、基礎設施可觀測性、錯誤追蹤、分析資料倉儲）。 |
| [`/recall`](./skills/recall/SKILL.md) | 您正在開始或恢復工作，希望根據您自己的聊天記錄與共享記錄，重建該主題的近期脈絡，並以簡潔的現況簡報形式傳回。 |
| [`/ketchup`](./skills/ketchup/SKILL.md) | 您希望以簡潔語言和小型視覺化內容，瞭解自您上一則訊息以來此聊天中的變更。所有等待您處理的專案都會列出，且僅憑回覆即可做出決定，即使這會使回覆變長。 |
| [`/blast-radius`](./skills/blast-radius/SKILL.md) | 您有一個看似微小的變更，希望瞭解它可能破壞其他什麼，並透過執行程式碼（而非斷言）來證明使其安全的單一事實。 |
| [`/architect`](./skills/architect/SKILL.md) | 您即將撰寫跨越函式邊界的程式碼，希望先確定呼叫者的使用方式、型別與模組結構。 |
| [`/arena`](./skills/arena/SKILL.md) | 您希望對同一件事進行 N 次並行嘗試，然後提取每個結果的最佳部分。 |
| [`/swarm`](./skills/swarm/SKILL.md) | 您希望針對不同切片或競態條件進行 N 個並行工作者，然後生成一份彙總報告。 |
| [`/interrogate`](./skills/interrogate/SKILL.md) | 您有一個 diff，希望不同的模型嘗試破壞它，包括嚴格的程式碼品質檢視。 |
| [`/automate-me`](./skills/automate-me/SKILL.md) | 您希望擁有自己的 `-mode` skill，根據您實際的工作方式起草。 |
| [`/make-bot-ui`](./skills/make-bot-ui/SKILL.md) | 您希望建立一個頁面或儀錶板，其按鈕可透過 webhook 喚醒 Grok Bot，包括傳送者金鑰交接與 Tailscale。 |
| [`/setup-pstack`](./skills/setup-pstack/SKILL.md) | 您希望選擇 pstack 依角色使用的模型。它會偵測您的模型並撰寫設定規則。 |
| [`/reflect`](./skills/reflect/SKILL.md) | 一項長任務完成，您希望將該配方記錄為 skill 編輯。 |
| [`/correct`](./skills/correct/SKILL.md) | 您不斷糾正 agent 犯下的相同錯誤。它會從歷史記錄中挖掘錯誤類別，在可行的最高層級修正每個錯誤（架構，然後型別、lint 和 ci，然後測試，最後是檔案），並維護一個表格，將每條規則與其執行機制配對。 |
| [`/teach`](./skills/teach/SKILL.md) | 您希望真正理解某個變更或子系統，而不僅僅是獲得摘要。它會執行 how + why，並編織出一個由圖表逐步構建的簡潔解釋。 |
| [`/tdd`](./skills/tdd/SKILL.md) | 您正在修復 bug，且存在廉價的本地測試路徑。先撰寫失敗的測試，然後撰寫修復。 |
| [`/benchmark-checklist`](./skills/benchmark-checklist/SKILL.md) | 您執行過基準測試或測量過加速或回歸。在報告或採取行動之前，驗證該數值（限制器、調優、錯誤、重複執行、端到端相關性）。 |
| [`/no-comments`](./skills/no-comments/SKILL.md) | 在審查前移除註解；啟動 Comment Sicko，修復已接受的發現，並為聲稱的約束提供編碼。 |
| [`/typescript-best-practices`](./skills/typescript-best-practices/SKILL.md) | 您正在閱讀或編輯 typescript。它將型別系統紀律原則落實於語法中。 |
| [`/figure-it-out`](./skills/figure-it-out/SKILL.md) | 沒有內建的 playbook 適合。它為該任務設計一個嚴謹且可稽核的 playbook。 |
| [`/show-me-your-work`](./skills/show-me-your-work/SKILL.md) | 您希望擁有可審查的決策軌跡。它將決策記錄到您可以 commit 的 tsv 中。 |
| [`/create-verification-skill`](./skills/create-verification-skill/SKILL.md) | 您的專案沒有指令碼化的方式來證明應用程式行為。它為任何語言或平臺生成一個專案本地的 verify skill，並包含功能地圖。 |
| [`/maintain-verification-skill`](./skills/maintain-verification-skill/SKILL.md) | 您的 verify skill 的功能地圖已與應用程式脫節。執行來源波次加一次即時透過，最多一個包含已證明修正的 PR。 |
| [`/create-formal-verification-skill`](./skills/create-formal-verification-skill/SKILL.md) | 測試無法觸及破壞競態、重試、狀態機、解析器或不安全程式碼的案例。它使用合適的工具驗證一個屬性，並生成一個專案本地的 skill，以便在每次變更時重新檢查。 |
| [`/maintain-formal-verification-skill`](./skills/maintain-formal-verification-skill/SKILL.md) | 您的正式驗證涵蓋的程式碼自上次檢查以來已變更。它更新模型、測試框架或屬性以匹配程式碼，重新執行，並說明每個失敗是驗證中的漂移還是程式碼中的 bug。 |
| [`/unslop`](./skills/unslop/SKILL.md) | 您正在清理寫作。它移除 AI 痕跡。 |
| [`/bro`](./skills/bro/SKILL.md) | 您希望以簡潔的人類語言重述最後一則訊息，不使用術語。 |
| [`/technical-writing`](./skills/technical-writing/SKILL.md) | 分層檔案標準（Diátaxis + Google 開發者風格 + STE + Global English），適用於檔案、RFC、readmes、PR 描述、commit 訊息。 |

</details>



### examples

通常我在任務開始時輸入 [`/poteto-mode`](./skills/poteto-mode/SKILL.md)，讓它路由到 playbook。其他 skills 會在步驟需要時觸發。我會直接呼叫其中幾項。


<details>
<summary>all the examples</summary>

```
bug fix:           /poteto-mode this pr has a subtle bug where the scroll drifts every 750ms even
                   when idle. repro first, then fix and verify.
perf:              /poteto-mode a big list takes a second or two to load even though we virtualize.
                   run a cpu trace and tell me why.
feature:           /poteto-mode build a small feature behind a feature flag. verify it really works.
prototype:         /poteto-mode build two prototypes of the markdown renderer so we can compare.
                   spawn an agent for each.
multi-phase:       /poteto-mode open source these skills as a plugin. nothing internal leaks, work
                   in a temp dir, show me the dependency graph first.
overnight run:     /poteto-mode i'm going to bed. land the stack even if ci flakes. i want
                   everything merged by morning.
babysit:           /poteto-mode check on pr 123. anything outstanding?
visual parity:     /poteto-mode the row spacing is too tall when this flag is on. the second image
                   is correct. repro and fix until it matches.
figure it out:     /poteto-mode i'm stepping away. migrate every caller from the synchronous store
                   to the new async one, keeping behavior identical. i want to trust it was done
                   right when i'm back.
how:               /how do we cancel runs? do we have an n+1 when we look up every run to cancel?
why:               /why is this feature flag not on yet?
architect:         design this instrumentation to be high signal with no false positives. /architect
                   this first.
arena:             /arena take my prompt to the arena verbatim. i want to compare their proposals
                   with yours.
swarm:             /swarm check every package under packages/ against its check.sh. one worker per
                   package. one report.
interrogate:       /interrogate review this pr.
tdd:               /tdd implement
unslop:            can we unslop and tighten the new changes?
reflect:           /reflect that took too long. capture what we learned so the next run doesn't
                   repeat it.
correct:           /correct
show-me-your-work: /show-me-your-work keep a decision trail i can review when i'm back.
automate-me:       /automate-me
help:              /poteto-help which skill should i use to review this branch?
```

</details>

<a id="the-poteto-agent-and-comment-sicko-subagents"></a>

## `poteto-agent` 與 Comment Sicko 子代理

[`/poteto-mode`](./skills/poteto-mode/SKILL.md) 在接收它的聊天中執行。該聊天擁有 playbook、待辦事項清單以及每個子代理的啟動。

pstack 也隨附 [`subagent_type: "poteto-agent"`](./agents/poteto-agent.md)，作為執行期間產生的委派代理的包裝器。它會完整讀取 `poteto-mode`，包括其內嵌的原則索引，然後才執行任何工作。若以 `generalPurpose` 取代，則會跳過該讀取步驟並產生偏差。請勿產生 `poteto-agent` 來執行 playbook 本身。

[`/poteto-mode`](./skills/poteto-mode/SKILL.md) 與 [`subagent_type: "poteto-agent"`](./agents/poteto-agent.md) 透過相同的包裝器路由。

pstack 也隨附 [Comment Sicko](./agents/comment-sicko.md)，這是一個唯讀的註解審查員，可作為 `subagent_type: "comment-sicko"` 使用。通常透過 [`/no-comments`](./skills/no-comments/SKILL.md) 呼叫它，而非直接呼叫。

<a id="principles"></a>

## 原則

二十四個簡短的技能，每個對應一個原則。`poteto-mode` 會內嵌索引這些原則，並在任務開始時讀取該索引。獨立檔案的存在是為了讓其他技能能依名稱引用原則，並讓索引能指向每個原則的完整規則。

<details>
<summary>所有二十四個原則</summary>

| 原則 | 群組 | 規則 |
|---|---|---|
| [laziness-protocol](./skills/principle-laziness-protocol/SKILL.md) | 核心 | 傾向刪除，並採用能解決問題的最小變更。 |
| [foundational-thinking](./skills/principle-foundational-thinking/SKILL.md) | 核心 | 在撰寫邏輯前套用：選擇核心型別與資料結構、安排基礎架構與功能工作的順序、詢問並行執行者共享什麼。正確設定資料結構，讓下游程式碼變得直觀。 |
| [redesign-from-first-principles](./skills/principle-redesign-from-first-principles/SKILL.md) | 核心 | 重新設計時，假設該需求從第一天起就是基礎假設，而非事後附加。 |
| [attack-the-premise](./skills/principle-attack-the-premise/SKILL.md) | 核心 | 當兩個或更多共享同一前提的修復方案都未透過同一關卡時套用。寫下該前提，並在撰寫另一個假設該前提成立的修復方案之前，直接測試它。 |
| [subtract-before-you-add](./skills/principle-subtract-before-you-add/SKILL.md) | 核心 | 先移除無用負荷、冗餘驗證器與存根引用，然後在更簡單的基礎上建構。 |
| [minimize-reader-load](./skills/principle-minimize-reader-load/SKILL.md) | 核心 | 計算問題與答案之間的層數，以及讀者心中的隱藏狀態；合併單一呼叫者的包裝器，並縮小可變範圍。 |
| [outcome-oriented-execution](./skills/principle-outcome-oriented-execution/SKILL.md) | 核心 | 在具有明確階段邊界的計畫性重寫與遷移期間套用。收斂至目標架構；不要使用一次性相容性程式碼來維持平滑的中介狀態。 |
| [experience-first](./skills/principle-experience-first/SKILL.md) | 核心 | 選擇使用者驚喜而非實作便利性；發布較少但精緻的功能，而非較多但粗糙的功能。 |
| [exhaust-the-design-space](./skills/principle-exhaust-the-design-space/SKILL.md) | 核心 | 在承諾之前，建立 2-3 個競爭原型並並排比較。 |
| [build-the-lever](./skills/principle-build-the-lever/SKILL.md) | 核心 | 適用於任何非平凡的工作，不僅限於大量工作：編輯、遷移、分析、檢查。建立執行或證明該工作的工具（codemod、指令碼、產生器，或子代理遵循的技能），而非手動操作。該工具是審查員可以重新執行的產出物。 |
| [model-the-domain](./skills/principle-model-the-domain/SKILL.md) | 架構 | 將領域編碼為結構，而非分散的條件判斷。 |
| [boundary-discipline](./skills/principle-boundary-discipline/SKILL.md) | 架構 | 將防護集中在系統邊界（CLI、設定、網路、外部 API）；信任內部型別，並將商業邏輯保持在純函式中。 |
| [type-system-discipline](./skills/principle-type-system-discipline/SKILL.md) | 架構 | 使非法狀態無法表示，為語意原語加上品牌，在邊界解析外部資料，窮舉變體，並從權威模式推導。 |
| [make-operations-idempotent](./skills/principle-make-operations-idempotent/SKILL.md) | 架構 | 無論先前是否有部分執行，都收斂至相同的終態。 |
| [migrate-callers-then-delete-legacy-apis](./skills/principle-migrate-callers-then-delete-legacy-apis/SKILL.md) | 架構 | 在同一波次中遷移呼叫者並刪除舊 API，而非保留相容性層。 |
| [separate-before-serializing-shared-state](./skills/principle-separate-before-serializing-shared-state/SKILL.md) | 架構 | 先消除共享；僅當單一共享寫入者是真正的不變數時，才在結構上序列化。 |
| [prove-it-works](./skills/principle-prove-it-works/SKILL.md) | 驗證 | 在任務完成後、宣告完成前套用。針對實際產出物進行驗證（執行功能、讀取實際值、檢查 diff），而非代理、自我報告或「它編譯透過」。 |
| [fix-root-causes](./skills/principle-fix-root-causes/SKILL.md) | 驗證 | 將每個症狀追溯至系統首次出錯的地方，並在那裡修復。只有當防護處理的狀態是合法時，該防護才算修復。 |
| [sequence-verifiable-units](./skills/principle-sequence-verifiable-units/SKILL.md) | 驗證 | 適用於多步驟工作（清理、遷移、類似編輯的執行）以及堆疊 commit 和 PR 的方式。將工作分解為小型單位，每個單位都以可驗證的狀態結束，在下一個之前檢查每個單位，並安排交付順序，使序列能向審查員證明自身。 |
| [test-behavior-not-implementation](./skills/principle-test-behavior-not-implementation/SKILL.md) | 驗證 | 在撰寫、變更或保留測試時套用。斷言程式碼使用者可觀察的內容，使測試在該行為中斷時失敗，並在僅內部變更時繼續透過。 |
| [explain-the-number](./skills/principle-explain-the-number/SKILL.md) | 驗證 | 在信任、報告或根據你測量的數字採取行動前套用：加速、回歸、吞吐量、延遲或評估結果。找出限制它的因素，並排除它測量了非預期工作的可能性。 |
| [guard-the-context-window](./skills/principle-guard-the-context-window/SKILL.md) | 委派 | 將大量工作路由至子代理；在主執行緒中保留摘要，而非原始負載。 |
| [never-block-on-the-human](./skills/principle-never-block-on-the-human/SKILL.md) | 委派 | 繼續執行，呈現結果，讓人類事後修正方向；僅對不可逆操作保留確認。 |
| [encode-lessons-in-structure](./skills/principle-encode-lessons-in-structure/SKILL.md) | 元 | 將規則編碼為 lint、中繼資料旗標、runtime 檢查或指令碼，而非更多文字。 |

</details>

<a id="not-shipped-here"></a>

## 未在此處發布

`poteto-mode` 引用但未捆綁的幾項內容：

- `/deslop` 與 `deslop` 技能隨 `cursor-team-kit` 外掛發布。
- `control-cli`（用於 CLI 和 TUI）與 `control-ui`（用於瀏覽器、Electron、網頁）也隨 `cursor-team-kit` 發布。
- `/create-skill` 是 cursor 內建功能。cursor 也隨附一個內建 `/autopilot`，用於監控 pr。在 `poteto-mode` 中，pr-status 請求會轉送至 [babysit playbook](./skills/poteto-mode/playbooks/babysit.md)。

若想要完整集合，請將 `cursor-team-kit` 與 pstack 一起安裝。

<a id="why-are-there-no-planning-skills"></a>

## 為什麼沒有規劃技能？

cursor 已經擁有一個很棒的 plan mode，與 pstack 搭配效果極佳。但就個人而言，我不相信規劃。最好的規格就是程式碼。如果你確實想要制定計畫，[`/poteto-mode`](./skills/poteto-mode/SKILL.md) 涵蓋了這一點，但它不是預設選項。

<a id="make-it-yours"></a>

## 使其成為你的風格

`poteto-mode` 是我的風格。你可能不想要完全相同的風格。

輸入 [`/automate-me`](./skills/automate-me/SKILL.md)。它會挖掘您近期的對話記錄，根據您實際的工作方式起草一個 `<your-name>-mode` skill，並在底層透過 pstack 進行路由。您保留 pstack 作為基礎，最終會獲得一個與 `poteto-mode` 並列的自訂路由 skill。

模型也是可設定的。輸入 [`/setup-pstack`](./skills/setup-pstack/SKILL.md)。它會偵測您有許可權使用的模型，並撰寫一個小型的常駐規則，將每個角色（程式碼、判斷、審查面板）對應到一個模型。每個 skill 都會讀取此規則，並在規則不存在時回退到合理的預設值，因此您只需覆寫想要調整的部分。

當預設值變更時，在變更前撰寫的規則仍會固定使用舊的預設值。請刪除這些角色行，或刪除該檔案，然後再次執行 `/setup-pstack`。重新執行時，會保留任何模型與預設值不同的角色。

<a id="automations"></a>

## 自動化

pstack 也隨附一個休眠的 [benny automation pack](https://github.com/cursor/plugins/tree/main/pstack/automations/benny)。benny 會分級處理 slack 問題報告，然後使用真實的 ui 證據重現並修復已確認的 bug。其檔案未註冊為 slash skills。

要進行設定，請將 cursor 指向 [`FOR_AGENTS.md`](https://github.com/cursor/plugins/blob/main/pstack/automations/benny/FOR_AGENTS.md)。設定過程會將該 pack 複製到目標儲存庫的 `.cursor/automations/benny/`，在那裡啟用 pstack 以共用 skills，並讓使用者設定保留在複製的 pack 之外。benny 執行於 cursor automations，因此僅限 cursor 使用。

<a id="license"></a>

## 授權條款

MIT
