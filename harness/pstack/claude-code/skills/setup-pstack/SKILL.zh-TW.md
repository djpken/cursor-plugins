---
name: setup-pstack
description: "設定 pstack 每個角色使用的模型及推理預算。偵測您可用的模型並寫入一個始終套用的規則，覆蓋技能預設值。適用於 /setup-pstack、\"設定 pstack 模型\"、\"pstack 預算\"或更改 pstack 的模型選擇。"
---

<a id="setup-pstack"></a>

# 設定 pstack

寫入 `~/.cursor/rules/pstack-models.mdc`，一個始終套用的規則，設定 pstack 每個角色的模型。

<a id="steps"></a>

## 步驟

<a id="1-detect-available-models"></a>

### 1. 偵測可用模型

列出您在此會話中可傳遞給 `Task` 子代理的模型 slug。這是可靠的來源。如果 Cursor 也提供列出使用者有權模型的 API 或 CLI，優先使用它以求完整。若無法偵測任何模型，請要求使用者貼上他們可使用的 slug。切勿寫入未確認可使用的真實 slug。別名 `inherit-parent` 和 `auto` 始終有效，即使它們不是偵測到的 slug。

<a id="2-load-current-state"></a>

### 2. 載入當前狀態

預設的角色對模型對映是步驟 5 所示的規則形狀。如果 `~/.cursor/rules/pstack-models.mdc` 已存在，讀取它並將其 `# budget` 行及角色值視為當前選擇。否則從這些預設開始。角色不在步驟 5 中的行，如 `how critics`，來自已退役的角色。刪除它。

<a id="3-budget-map-and-confirm"></a>

### 3. 預算、對映與確認

**(a) 詢問預算。** 優先使用 AskQuestion 而非自由文字。提供以下四個選項，使用這些確切標籤，並在規則記錄預算時命名當前預算。無規則時，說明 `large` 符合技能預設。

- `unlimited — max reasoning`
- `large — xhigh reasoning`
- `medium — high reasoning`
- `small — medium reasoning`

**(b) 套用。** 從技能預設建立工作表格，在重新執行時保留您按家族、列表或別名更改的任何角色（`inherit-parent`、`auto`）。`unlimited`、`large`、`medium` 和 `small` 將每個真實 slug 的 effort token（包括面板條目）設定為 `max`、`xhigh`、`high` 或 `medium`。effort token 是最後一個 token，或是在尾隨 `fast` 之前的一個，位於階梯 `max` > `xhigh` > `high` > `medium` > `low`。如果結果不是偵測到的 slug，使用同一家族中偵測到的 slug，其 effort 在目標以下最高，否則標記該角色為需要選擇。`inherit-parent` 和 `auto` 不變。因此 `unlimited` 將 `claude-opus-5-5-xhigh` 轉為 `claude-opus-5-5-max`。Grok slug 最高為 `xhigh`，所以在 `unlimited` 下，fallback 將 Grok 放在 `xhigh` 並保持 `grok-4.7-xhigh-fast` 不變。`large` 保持兩個預設。`small` 將它們轉為 `claude-opus-5-5-medium` 和 `grok-4.7-medium-fast`。

**(c) 顯示角色並確認。** 顯示每個角色及其模型，標記任何不在偵測集合中的真實 slug為需要選擇。也列出步驟 2 刪除的每一行。詢問是否接受現狀或更改特定角色，提供偵測到的模型加上 `inherit-parent` 和 `auto`（兩者均表示：此角色執行在父聊天模型上，這是 Auto 使用者保持在 Auto 的方式）作為選項。優先使用 AskQuestion 而非自由文字。對於面板角色（arena 執行者、architect 執行者、interrogate 審閱者），值是一個列表，每個條目執行一個子代理，包括別名條目，因此列表長度設定數量。`arena cross-judge pool` 也是列表，但 Arena 從其中選擇一個值，其模型家族在可能時與父模型不同。`swarm workers` 是每個 worker 的預設模型，除非競賽或比較為每個臂指派另一個模型。

<a id="4-validate"></a>

### 4. 驗證

每個寫入的實際 slug必須在偵測集合中。`inherit-parent` 和 `auto` 始終透過。如果選擇的實際 slug不可用，停止並再次詢問。

<a id="5-write-the-rule"></a>

### 5. 寫入規則

寫入 `~/.cursor/rules/pstack-models.mdc` 包含 `alwaysApply: true`、一個 `# budget` 行帶有選擇的標籤及其目標 effort，以及每個角色一行，使用 poteto-mode 使用的相同標籤。覆寫整個檔案，使重新執行保持冪等。形狀：

```
---
description: pstack per-role model choices (overrides skill defaults)
alwaysApply: true
---
# pstack model configuration. One line per role. Delete a line to fall back to the skill default.
# `inherit-parent` or `auto` as a value: the role runs on the parent chat model (omit Task `model`). Alias entries in a panel list still count toward its fan-out.
# budget: large (xhigh)
feature, refactoring: grok-4.7-xhigh-fast
bug-fix: grok-4.7-xhigh-fast
perf-issue: grok-4.7-xhigh-fast
hillclimb: grok-4.7-xhigh-fast
judgment and prose: claude-opus-5-5-xhigh
hardest tasks: claude-opus-5-5-xhigh
how explorer: grok-4.7-xhigh-fast
how explainer: claude-opus-5-5-xhigh
why investigators: grok-4.7-xhigh-fast
why synthesizer: claude-opus-5-5-xhigh
reflect tooling: grok-4.7-xhigh-fast
reflect judgment, divergent, synthesizer: claude-opus-5-5-xhigh
arena runners: claude-opus-5-5-xhigh, grok-4.7-xhigh-fast
arena cross-judge pool: claude-opus-5-5-xhigh, grok-4.7-xhigh-fast
swarm workers: grok-4.7-xhigh-fast
architect runners: claude-opus-5-5-xhigh, grok-4.7-xhigh-fast
interrogate reviewers: claude-opus-5-5-xhigh, grok-4.7-xhigh-fast
```

<a id="6-confirm"></a>

### 6. 確認

告知使用者規則已寫入並適用於新會話。重新執行此技能會更新它。

<a id="7-offer-a-verification-skill-optional"></a>

### 7. 提供驗證技能（可選）

檢查專案是否有驅動真實應用進行證明的方式（`verify-*` 技能或現有 harness）。如果沒有，提供一次："想要一個專案本地驗證技能，讓代理能以使用者方式驅動應用並證明更改有效？我可以用 /create-verification-skill. 生成一個" 若同意，呼叫 `/create-verification-skill`（在 pstack 安裝位置解析：workspace、user 或 plugin）。若不同意，繼續而不推。

<a id="8-offer-a-formal-verification-skill-optional"></a>

### 8. 提供正式驗證技能（可選）

檢查專案是否已有正式驗證技能（一個專案技能執行模型檢查器、證明或其他驗證器對程式碼）。如果沒有，提供一次："想要一個正式驗證技能，讓代理能證明程式碼的關鍵屬性在範圍內每個案例成立，不僅測試達到的案例？我可以用 /create-formal-verification-skill. 生成一個" 若同意，呼叫 `/create-formal-verification-skill`（在 pstack 安裝位置解析：workspace、user 或 plugin）。若不同意，繼續而不推。
