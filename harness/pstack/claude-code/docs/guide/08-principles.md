<a id="steer-with-principle-names"></a>

# 以原則名稱進行引導

pstack 隨附 24 項原則，每項皆為獨立的 skill。`/poteto-mode` 會在每個多步驟任務開始時讀取它們的索引，套用該任務觸發的原則，並在回覆中標明每個已套用原則的名稱及其所改變的決策。

您不需要呼叫原則。您使用它們的名稱來進行引導。每個名稱都指向 agent 已經讀取的完整規則，因此一個短語就能比一段說明更精確地重新導向工作。

<a id="steering-in-practice"></a>

## 實際引導範例

假設 agent 即將把一個新的 adapter 附加到三個現有的 adapter 上：

```text
use subtract before you add. delete the obsolete adapters first, then design what's left.
```

假設它因為建置成功而宣稱任務完成：

```text
apply prove it works. run the real import flow and show me the written records.
```

假設兩個並行的嘗試即將寫入同一個 branch：

```text
separate before serializing shared state. give each attempt its own worktree, no locks.
```

每個短語之所以有效，是因為其背後的規則具體明確。agent 仍需在回覆中說明該規則改變了哪個決策。若引用原則卻沒有對應的決策，表示 agent 只是提到了規則名稱，並未實際套用。

<a id="the-24-briefly"></a>

## 24 項原則簡述

核心原則決定要建置多少內容，以及何時重新思考設計：

- [Laziness Protocol](../../skills/principle-laziness-protocol/SKILL.md) 偏好刪除，並採用能解決問題的最小變更。
- [Foundational Thinking](../../skills/principle-foundational-thinking/SKILL.md) 在撰寫邏輯之前，先選擇核心的資料結構。
- [Redesign from First Principles](../../skills/principle-redesign-from-first-principles/SKILL.md) 整合新需求時，如同該需求從第一天起就存在一般。
- [Attack the Premise](../../skills/principle-attack-the-premise/SKILL.md) 寫下兩個或多個失敗修復所共有的前提，並在進行下一次修復前直接測試該前提。
- [Subtract Before You Add](../../skills/principle-subtract-before-you-add/SKILL.md) 在基於現有內容建置之前，先移除無用的負擔。
- [Minimize Reader Load](../../skills/principle-minimize-reader-load/SKILL.md) 精簡讀者在腦中必須持有的層級與隱藏狀態。
- [Outcome-Oriented Execution](../../skills/principle-outcome-oriented-execution/SKILL.md) 將重寫工作收斂至目標設計，而非保留一次性的相容狀態。
- [Experience First](../../skills/principle-experience-first/SKILL.md) 優先選擇使用者的結果，而非實作的便利性。
- [Exhaust the Design Space](../../skills/principle-exhaust-the-design-space/SKILL.md) 在沒有先例時，建置兩個或三個競爭的原型。
- [Build the Lever](../../skills/principle-build-the-lever/SKILL.md) 建置執行或證明工作的指令碼，讓審查者可以重新執行。當 agent 持續手動執行相同操作時，請讓它撰寫自己希望擁有的工具或 skill。若指令碼可以每次以相同方式執行某個步驟，就使用指令碼，並將 agent 保留給需要判斷的決策。

架構原則決定狀態、驗證與相容性所在的層級：

- [Model the Domain](../../skills/principle-model-the-domain/SKILL.md) 將重複的規則編碼在單一結構中，而非分散在條件判斷中。
- [Boundary Discipline](../../skills/principle-boundary-discipline/SKILL.md) 在邊界進行驗證，並信任內部型別。
- [Type System Discipline](../../skills/principle-type-system-discipline/SKILL.md) 讓非法狀態無法被表示。
- [Make Operations Idempotent](../../skills/principle-make-operations-idempotent/SKILL.md) 讓重試收斂至相同的終端狀態。
- [Migrate Callers Then Delete Legacy APIs](../../skills/principle-migrate-callers-then-delete-legacy-apis/SKILL.md) 在一個波次中完成遷移與刪除。
- [Separate Before Serializing Shared State](../../skills/principle-separate-before-serializing-shared-state/SKILL.md) 在加入協調機制之前，先移除共享。

驗證原則定義什麼才算證明：

- [Prove It Works](../../skills/principle-prove-it-works/SKILL.md) 驗證真實的產物，而非代理指標。
- [Fix Root Causes](../../skills/principle-fix-root-causes/SKILL.md) 在變更程式碼之前，先重現問題並追蹤至根本原因。
- [Sequence Work into Verifiable Units](../../skills/principle-sequence-verifiable-units/SKILL.md) 在開始下一個單元之前，先以檢查結束每個小單元。
- [Test Behavior, Not Implementation](../../skills/principle-test-behavior-not-implementation/SKILL.md) 斷言程式碼使用者可觀察的行為，因此當該行為損壞時測試會失敗，而在內部變更時測試仍能透過。
- [Explain the Number](../../skills/principle-explain-the-number/SKILL.md) 在任何人信任或報告該數字之前，先指出限制該測量數值的因素，並排除它測量了其他事物的可能性。[`/benchmark-checklist`](../../skills/benchmark-checklist/SKILL.md) 將其轉化為七個問題，您需從實際執行中回答。

委派原則讓並行工作保持有序：

- [Guard the Context Window](../../skills/principle-guard-the-context-window/SKILL.md) 將大量讀取工作路由至 subagents，並將發現保留在主對話中。
- [Never Block on the Human](../../skills/principle-never-block-on-the-human/SKILL.md) 繼續執行可逆轉的工作，並呈現結果。

以及一項元原則：

- [Encode Lessons in Structure](../../skills/principle-encode-lessons-in-structure/SKILL.md) 將您重複過兩次的建議轉化為 lint、檢查或指令碼。[`/correct`](../../skills/correct/SKILL.md) 將其應用於整個儲存庫，如 [Make it yours](./09-make-it-yours.md#fix-the-environment-with-correct) 所示。

不要死記硬背這份清單。現在快速瀏覽一遍，然後當您發現 agent 做了某個名稱本可阻止的事情時，再回來檢視。這就是讓詞彙紮根的方式。

下一步：[Make it yours](./09-make-it-yours.md)。
