---
name: poteto-help
description: "引導使用者完成 pstack 設定、/poteto-mode，以及為任務選擇技能、劇本或原則。輸入 /poteto-help 並附上問題。"
disable-model-invocation: true
---

<a id="poteto-help"></a>

# Poteto 幫助

回答使用者關於 pstack 的問題，給他們一個可傳送的提示，並連結答案來源的檔案。對於幫助類問題，不要開始工作。使用者詢問的是方法，而 pstack 執行會消耗實際 token，因此讓他們傳送提示。

請求工作的訊息，例如“使用 pstack 修復這個 bug”，不是幫助類問題。閱讀 [`poteto-mode`](../poteto-mode/SKILL.md)，在其下執行工作，並提及一次自定義模式可保持其持續執行。

此檔案將問題對映到包含答案的技能和指南頁面。這些檔案擁有細節。在引用之前閱讀你路由到的檔案，並在它與此對映不一致時信任它。這裡的連結指向安裝的外掛，使用者可能無法開啟，因此給使用者檔案的公開副本：`https://github.com/cursor/plugins/blob/main/pstack/` 後接其路徑。

<a id="find-out-what-they-need"></a>

## 找出他們需要什麼

從訊息和對話推斷需求。已命名的情境，例如“哪個技能審查 PR？”，直接進入其章節。如果需求仍不清楚，提出一個多項選擇題，選項如下，然後僅回答他們選擇的章節：

- 進行設定
- 使用 `/poteto-mode` 開始任務
- 為情境選擇技能
- 修復失敗的執行
- 讓 pstack 成為我的專屬

檢查會改變答案的狀態，並僅在它確實改變時提及：

- 沒有 `~/.cursor/rules/pstack-models.mdc` 意味著 `/setup-pstack` 尚未為此使用者執行，因此每個角色使用其預設模型。
- 專案中沒有 `verify-*` 技能或其他應用框架意味著代理沒有指令碼化方式來驅動應用。當問題涉及證明變更有效時，提及 `/create-verification-skill`。
- 專案中沒有正式驗證技能意味著沒有屬性被檢查超出測試覆蓋範圍。當問題涉及競態、不變式或其他測試很少觸及的情況時，提及 `/create-formal-verification-skill`。當存在一個技能且其覆蓋的程式碼在記錄的最後同步提交之後發生變化，且問題涉及該程式碼時，提及 `/maintain-formal-verification-skill`。

當模型規則缺失且影響答案時，詢問使用者是否現在要為每個角色選擇模型和推理預算。這在新使用者、問題涉及設定或成本，或答案取決於執行的模型時很重要。每個聊天最多詢問一次。如果需求也不清楚，同時提出兩個問題。提供兩個選項：

- 現在：給他們 `/setup-pstack` 輸入，並同時回答他們的問題。
- 稍後：回答他們的問題，並新增一行說明每個角色保持預設模型直到他們執行 `/setup-pstack`。

<a id="get-set-up"></a>

## 進行設定

1. 在聊天中使用 `/add-plugin pstack` 安裝，或從側邊欄的“自定義”中安裝。
2. 執行 [`/setup-pstack`](../setup-pstack/SKILL.md)。它會詢問推理預算，將模型對映到每個角色，並寫入規則。規則適用於新聊天。然後它各提供一次生成驗證技能和正式驗證技能的機會，除非專案已有這些技能。
3. 使用 `/poteto-mode`、一個目標和一個可成功或失敗的檢查開始實際任務。

安裝不會改變任何東西，直到使用者呼叫技能。只有 `/setup-pstack` 從使用者的話語中載入。 [README](../../README.md) 和 [指南頁 1](../../docs/guide/01-setup.md) 有詳細資訊。根據 [`references/prompting.md`](references/prompting.md)，與他們一起措辭第一個提示。

如果成本是擔憂，說明 token 的去向和如何減少消耗。pstack 在子代理和審查面板上消耗額外 token。重新執行 `/setup-pstack` 並選擇較小的預算或更便宜的模型。設定為 `auto` 或 `inherit-parent` 的角色在聊天模型上執行，當聊天使用 Auto 或更便宜的模型時節省 token。較短的面板列表執行較少子代理，每個條目一個。將 `/poteto-mode` 保留給需要嚴謹性的工作。

pstack 專為 Cursor 構建，也可在 Claude Code 中安裝，使用 `/plugin marketplace add cursor/plugins`，然後 `/plugin install pstack@cursor-plugins`。在那裡，每個命令帶有 `pstack:` 字首，例如 `/pstack:poteto-mode`。 [Claude Code 介面卡](../../docs/harness/claude-code.md) 對映 Cursor 名稱，需要 Cursor 專屬功能（如 Bugbot 或 Automations）的步驟會跳過並附一行說明。

其他工具可以讀取技能，因為它們使用 Agent Skills 格式。但大多數工作流技能，包括 `/poteto-mode`、`/how`、`/why` 和 `/teach`，會生成 Cursor 子代理並使用角色模型，而自定義模式和 `/loop` 是 Cursor 功能，因此這些部分可能在那裡無法工作。

<a id="start-a-task-with-poteto-mode"></a>

## 使用 `/poteto-mode` 開始任務

`/poteto-mode` 將任務匹配到劇本，將劇本步驟複製到待辦列表，並按步驟需求執行其他技能。跳過步驟保留在列表中為 `skip: <reason>`。好的提示陳述目標和如何判斷完成。它不列出技能，因為手寫序列往往會遺漏或重排劇本保留的步驟。閱讀 [`references/prompting.md`](references/prompting.md) 之前幫助措辭。 [指南頁 2](../../docs/guide/02-poteto-mode.md) 有示例。

`/poteto-mode` 是否持續執行取決於使用者如何啟動它：

- 在 `/poteto-mode` 上按 Enter 將技能附加到一條訊息。隨著聊天進展它會淡化。
- Mac 上的 Option+Enter 或 Windows 上的 Alt+Enter，或從技能條目使用“用作模式”，使其成為自定義模式。它在每輪對話中保持上下文直到使用者退出模式，並避免出現在日常輪次中。
- Cursor 文件在 Agents Window 和 CLI 中列出自定義模式。其他情況下，每次新任務以 `/poteto-mode` 開始。

連結 [Cursor 的技能文件](https://cursor.com/docs/skills) 當此話題出現。聊天中途，“新任務”使模式匹配新劇本。`/poteto-mode` 已使用 `poteto-agent` 為其劇本步驟生成的子代理。若要從你自己的子代理獲得相同風格，使用 `subagent_type: "poteto-agent"` 生成它。

<a id="pick-a-skill"></a>

## 選擇技能

預設答案是 `/poteto-mode`，它在其步驟需要時執行大多數其他技能。當使用者想要比劇本提供的更多或更少時，直接命名技能。閱讀技能後再推薦，並提供一個示例提示。

| 使用者想要 | 技能 |
|---|---|
| 以嚴謹性完成任何非平凡任務 | [`/poteto-mode`](../poteto-mode/SKILL.md) |
| 檢查代理在開始前理解目標和問題 | [`/in-your-own-words`](../in-your-own-words/SKILL.md) |
| 獲得更深入而非更大的提示改寫，在傳送之前：頂級專業人士會新增的內容、他們的原始材料和方法，以及外部檢查。它返回提示而不執行任務 | [`/sharpen`](../sharpen/SKILL.md) |
| 瞭解程式碼當前如何運作，或新程式碼應放在哪裡 | [`/how`](../how/SKILL.md) |
| 瞭解程式碼為何如此成形，或數字來源 | [`/why`](../why/SKILL.md) |
| 以簡潔易懂的方式理解某項變更或子系統 | [`/teach`](../teach/SKILL.md) |
| 追趕自己近期在特定主題上的工作進度 | [`/recall`](../recall/SKILL.md) |
| 以簡潔語言和小型視覺化內容，追趕本次對話中自使用者上則訊息以來的變更，並列出所有等待使用者處理的專案，使其僅憑回覆即可做出決定 | [`/ketchup`](../ketchup/SKILL.md) |
| 瞭解小型 diff 可能在其自身範圍外造成的破壞 | [`/blast-radius`](../blast-radius/SKILL.md) |
| 在撰寫跨越函式邊界的程式碼之前，先確定型別和模組結構 | [`/architect`](../architect/SKILL.md) |
| 針對同一簡報進行多次嘗試，並合併為最佳版本 | [`/arena`](../arena/SKILL.md) |
| 對切片執行並行檢查，或讓工作者競賽，作為雲端 agent 執行 | [`/swarm`](../swarm/SKILL.md) |
| 讓不同模型審查 diff 並嘗試破壞它 | [`/interrogate`](../interrogate/SKILL.md) |
| 當存在廉價的本地測試時，採用測試優先的方式修復 bug | [`/tdd`](../tdd/SKILL.md) |
| 將 TypeScript 規則應用於 `.ts` 或 `.tsx` 工作 | [`/typescript-best-practices`](../typescript-best-practices/SKILL.md) |
| 在審查前移除註解，並使用未撰寫這些註解的審查者 | [`/no-comments`](../no-comments/SKILL.md) |
| 清除散文中的 AI 痕跡 | [`/unslop`](../unslop/SKILL.md) |
| 按照標準撰寫檔案、RFC、README、PR 描述或 commit 訊息 | [`/technical-writing`](../technical-writing/SKILL.md) |
| 以簡潔語言再次聆聽上則回覆 | [`/bro`](../bro/SKILL.md) |
| 為 agent 提供指令碼化方式來驅動應用程式並證明行為 | [`/create-verification-skill`](../create-verification-skill/SKILL.md) |
| 讓驗證 skill 及其功能地圖與應用程式保持一致 | [`/maintain-verification-skill`](../maintain-verification-skill/SKILL.md) |
| 正式驗證競態、不變數或測試很少涵蓋的其他屬性 | [`/create-formal-verification-skill`](../create-formal-verification-skill/SKILL.md) |
| 在受覆蓋的程式碼變更後，讓正式驗證保持一致，並判斷失敗是漂移還是 bug | [`/maintain-formal-verification-skill`](../maintain-formal-verification-skill/SKILL.md) |
| 在報告或採取行動之前，審核效能數字 | [`/benchmark-checklist`](../benchmark-checklist/SKILL.md) |
| 執行大型或橫跨性的變更，或執行離開後需審查的變更 | [`/figure-it-out`](../figure-it-out/SKILL.md) |
| 在執行期間維護決策紀錄，並在事後審查 | [`/show-me-your-work`](../show-me-your-work/SKILL.md) |
| 為每個角色選擇模型和推理預算 | [`/setup-pstack`](../setup-pstack/SKILL.md) |
| 將自己的工作習慣轉化為個人模式 skill | [`/automate-me`](../automate-me/SKILL.md) |
| 將已完成任務所學轉化為 skill 編輯 | [`/reflect`](../reflect/SKILL.md) |
| 防止 agent 在此儲存庫中重複相同的錯誤 | [`/correct`](../correct/SKILL.md) |
| 建置一個頁面，其按鈕透過 webhook 喚醒 Grok Bot | [`/make-bot-ui`](../make-bot-ui/SKILL.md) |
| 在 pstack 中摸索方向 | `/poteto-help` |

如果此目錄旁邊的 skill 目錄未出現在表格中，請閱讀其 frontmatter 並根據其描述進行路由。`principle-*` 目錄涵蓋在下方原則中。

艱難的抉擇：

- `/how` 解釋程式碼做了什麼。`/why` 解釋原因。`/teach` 執行其中一個或兩者，並以簡潔方式解釋結果。
- `/arena` 為每個工作者提供相同的簡報，並合併最佳部分。`/swarm` 將工作分割為切片或競賽，並返回一份報告。
- `/architect` 在確定設計後立即實作。加上「with checkpoint」可在撰寫程式碼前審查設計。
- `/interrogate` 審查 diff。`/blast-radius` 尋找 diff 範圍外的破壞，並證明使變更安全的那個事實。
- `/recall` 重建近期對話中的上下文。`/ketchup` 僅涵蓋本次對話中自使用者上則訊息以來的內容。恢復特定對話或分支是 Session pickup playbook。
- `/figure-it-out` 設計一次嚴謹的執行。Orchestrate playbook 執行跨越數天且涉及多個 PR 的程式。Autonomous run playbook 驅動單一任務直至完成條件。

pstack 中沒有：

- `/deslop`、`control-cli` 和 `control-ui` 包含在 `cursor-team-kit` plugin 中。
- `/loop` 和 `/create-skill` 是 Cursor 內建功能。
- pstack 沒有 `/orchestrate` skill。Orchestrate 是 `/poteto-mode` playbook。如果斜線選單顯示 `/orchestrate`，則是由另一個 plugin 提供。

<a id="playbooks-and-principles"></a>

## Playbooks 和原則

Playbooks 是 `/poteto-mode` 內的步驟清單，而非 skills，因此沒有斜線命令。在 `/poteto-mode` 中，描述任務會選擇其中一個，而這些短語會直接命名其中一個：

- 「babysit this pr」或「check on pr 123」會執行 Babysit。它會驅動 PR 至可合併狀態並在此停止。除非使用者要求合併、落地或發布，否則它不會合併。
- 「land the stack」會執行 Shipping。
- 「take over this branch」會執行 Session pickup。
- 「pause safely」會執行 Pause safely。
- 「full autopilot on this queue」會執行 Autopilot-full。「stack them, don't ship」會執行 Autopilot-stack。
- 「run the eval playbook」會執行 Eval。

沒有 `/poteto-mode` 時，像「babysit this pr」這樣的短語可能會啟動 Cursor 自己的 skill 來執行相同的工作。[`poteto-mode`](../poteto-mode/SKILL.md) 的 Playbooks 部分列出了每個 playbook 及其適用時機。[Guide page 6](../../docs/guide/06-verify-and-ship.md) 涵蓋開啟、babysitting 和落地 PR。

pstack 沒有規劃 skill。Cursor 的 Plan Mode 可與其並行運作。對於跨越階段或堆疊 PR 的工作，向 `/poteto-mode` 請求計劃會執行 [Multi-phase plan playbook](../poteto-mode/playbooks/multi-phase-plan.md)，它會撰寫計劃但不實作。對於設計問題，Prototype playbook 或 `/architect` 會先在程式碼中解決。

原則是單一規則的 skills，`/poteto-mode` 會在回覆中閱讀並引用它們。使用者很少直接呼叫它們。使用者通常透過名稱來引導，例如「apply prove it works. show me the real output.」。輸入 `/principle-<name>` 仍可隨時載入一個。[Guide page 8](../../docs/guide/08-principles.md) 列出了它們。

<a id="fix-a-run-that-went-wrong"></a>

## 修復失敗的執行

| 症狀 | 修復 |
|---|---|
| 模式在幾輪對話後停止應用 | 它是用 Enter 啟動的。請將其作為 Custom Mode 啟動，或每個任務都以 `/poteto-mode` 開始。 |
| 問題被視為上一個任務的下一步 | 請說「new task」，或說這輪對話不需要該模式。 |
| 新的模型選擇沒有效果 | `/setup-pstack` 的規則適用於新對話。請開始一個新對話。 |
| 執行成本高於預期 | 請參閱 Get set up 下的成本段落。 |
| Skill 未自動載入 | 只有 `/setup-pstack` 會從使用者的話語中載入。其他 skill 在使用者輸入它們或 `/poteto-mode` 執行它們時載入，而它不會執行每個 skill。 |
| 並行 agent 互相覆蓋 | 為每個 agent 提供自己的 worktree，或將它們作為雲端 agent 執行，每個 agent 都有自己的機器。 |
| 過夜執行有移動但未完成任何事 | `/loop` 需要一個可以透過或失敗的檢查，而非持續時間。請參閱 [guide page 7](../../docs/guide/07-overnight.md)。 |
| 回覆聲稱從綠色構建成功 | 要求真實的命令、流程、儲存的值或設定。這就是證明它有效原則。 |

對於漂移的執行，[`references/prompting.md`](references/prompting.md) 有單行調整。 [指南頁 10](../../docs/guide/10-recipes-and-pitfalls.md) 有更多陷阱和值得複製的配方。

<a id="make-pstack-my-own"></a>

## 讓 pstack 成為我的專屬

- [`/automate-me`](../automate-me/SKILL.md) 從使用者自己的歷史中起草個人模式技能，與 `/poteto-mode` 一起使用。
- [`/reflect`](../reflect/SKILL.md) 在會話結束後將其經驗轉為使用者批准的技能編輯。
- `/poteto-mode write a skill for <workflow>` 執行作者劇本。評估劇本盲測技能變更。
- 在技能本身的 PR 中修正行為不佳的技能，而不是在功能工作中發生錯誤的地方。

[指南頁 9](../../docs/guide/09-make-it-yours.md) 涵蓋每一個這些。

<a id="reply"></a>

## 回覆

先提供答案。最多在程式碼塊中提供一個示例提示，從 [`references/recipes.md`](references/recipes.md) 中調整一個適合的，然後連結到該檔案。保持簡短，除非使用者要求整個地圖。
