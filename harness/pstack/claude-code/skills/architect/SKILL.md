---
name: architect
description: "在撰寫程式碼之前，先勾勒型別、簽名與模組結構，並在實作逐步補完時持續參與。適用於 /architect、「設計此架構」、「規劃此設計」，或任何若直接跳入程式碼就會鎖定錯誤結構的非平凡工作。"
disable-model-invocation: true
---

<a id="architect"></a>

# 架構師

In Claude Code, read [the Claude Code adapter](../../docs/harness/claude-code.md) once per session before you follow this skill. It maps the Cursor names in pstack skills to Claude Code.

使用 `not implemented` 主體與偽程式碼，勾勒型別、函式簽名、類別結構與模組邊界。綜合多個模型視角，再依據選定的草圖填入程式碼。

<a id="start"></a>

## 開始

開始前，開啟一份待辦清單，每個階段對應一個專案。

1. 接地
2. 草圖
3. 共識
4. 實作
5. 棄用

<a id="phase-a-ground-the-problem"></a>

## 階段 A：問題接地

為新程式碼所觸及的每個系統建立真實的心智模型。對相關子系統執行 **how** 技能。僅命名檔案並非接地。產出 `how` 所規定的架構說明。若設計重新定義了所有權或分層，也需對現有結構執行 **why** 技能，使設計理由成為約束條件，而非猜測。

僅當工作確實是全新領域（greenfield），且無周圍系統需要整合時，才可跳過階段 A。

<a id="phase-b-sketch"></a>

## 階段 B：草圖

使用設計草圖任務與階段 A 的接地產物執行 **arena** 技能。將 `references/runner-prompt.md` 作為每個執行器的提示詞。

從 `pstack-models.mdc` 規則中的 `architect runners` 行取得執行器，取代 `arena runners` 行。若規則或該行缺失，則使用 `claude-opus-5-5-xhigh` 與 `grok-4.7-xhigh-fast`。別名與被拒絕的專案遵循 **arena** 技能階段 A 中的執行器規則。

設計兩次。在綜合之前，要求至少兩個結構上不同的候選方案，即使第一個看起來已足夠。這是將 **exhaust-the-design-space** 原則技能具體化的做法。追求整體結構的替代方案，而非單一結構內的區域性修正。

在綜合之前，依據 [`references/design-red-flags.md`](references/design-red-flags.md) 篩選每個候選方案。假設下一位貢獻者是一個只能看到已開啟檔案、複製最近範例，並採取最短路徑以透過編譯的 agent。優先選擇那種從單一檔案看來正確的變更，對整個儲存庫而言也是正確的設計。

比較可行候選方案的介面深度。優先選擇將更多複雜性隱藏在更小、更簡單公開介面後方的設計。豐富的介面可以透過集中能力而非將其分散在各層，來保持呼叫鏈的簡短。

Arena 回傳一個綜合設計套件。其綜合決策將填入理由說明中的「Synthesis decision」章節。

<a id="phase-c-agree-opt-in"></a>

## 階段 C：共識（選填）

預設：直接依據綜合設計進入實作。無人為檢查點。

當呼叫者明確要求時，選填檢查點：「/architect with checkpoint」、「實作前暫停並展示給我」或類似表述。然後展示綜合設計並暫停等待核準。

無論如何，綜合結果都可以作為獨立的 commit 發布，作為 **foundational-thinking** 原則技能的「先搭骨架」模式。依據 **outcome-oriented-execution** 原則技能，填入程式碼期間的計畫內與範圍內的破壞是可以接受的。若要在實作前對設計進行對抗性壓力測試，請對綜合草圖執行 **interrogate** 技能。

若人類對結構提出異議（在檢查點期間或事後），將其視為階段 A 的證據。重新接地並重新執行階段 B，再撰寫更多程式碼。

<a id="phase-d-implement-against-the-sketch"></a>

## 階段 D：依據草圖實作

將 `not implemented` 主體替換為程式碼，將偽程式碼替換為邏輯。綜合草圖即為合約。偏離草圖是應浮現的訊號，而非應靜默吸收的摩擦。若函式需要草圖未預期的引數，請詢問是草圖錯誤、需求遺漏，還是實作過度延伸。

<a id="phase-e-scrap-when-the-architecture-is-wrong"></a>

## 階段 E：架構錯誤時棄用

若實作持續產生草圖無法吸收的摩擦，請丟棄草圖。依據 **redesign-from-first-principles** 與 **fix-root-causes** 原則技能，不要將修正補丁強加在錯誤的設計上。

訊號是一種*模式*，而非單一例項。跡象包括：

- 相同形狀的變通方法在無關程式碼中反覆出現。
- 多個無關的邊界情況，全都需要特殊情況分支。
- 型別需要逃生出口（`any`、強制轉型、實務上總是設定的選填欄位）才能透過編譯。
- 當草圖指出狀態未共享時，卻出現「我們需要鎖」的反射動作。
- 呼叫者必須瞭解抽象層的內部規則才能使用它。
- 實作中出現兩個或更多相同形狀的獨立階段 D 偏離。

運用判斷力。少數邊界情況不會判處架構死刑。某些問題確實複雜。資料中的複雜性不等於設計中的複雜性。

當您棄用時：

1. 對已建置的內容重新執行 **how** 技能。
2. 依據 redesign-from-first-principles，假設新約束條件是首日假設，重新設計。
3. 依據 **subtract-before-you-add** 原則技能，先減後加。新草圖在成長之前，應比舊草圖更小。
4. 返回階段 B 並重新執行 arena。

<a id="outputs"></a>

## 輸出

先撰寫呼叫者的使用方式，再從中推導型別草圖。小型變更使用單一檔案包含新型別與簽名。較大工作使用模組地圖加上型別定義。理由說明隨附發布，依據 `references/rationale-template.md` 塑形，包含使用草圖與綜合決策。
