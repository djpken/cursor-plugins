---
name: show-me-your-work
description: "保留可審閱的決策紀錄，適用於長時間執行或無人監督的工作：一個 TSV 紀錄檔，每個決策一行（內容、原因、證據、結果）。預設為本地儲存；當審閱者需要紀錄來信任結果時，才提交到儲存庫。適用於 /show-me-your-work、自主或多階段執行，或人類離開後審閱的工作。"
disable-model-invocation: true
---

<a id="show-me-your-work"></a>

# 展示你的工作

In Claude Code, read [the Claude Code adapter](../../docs/harness/claude-code.md) once per session before you follow this skill. It maps the Cursor names in pstack skills to Claude Code.

保留一個標準紀錄檔。

<a id="the-format"></a>

## 格式

一個 TSV 檔案，每個決策一行。每個欄位保持單行。證據是指向，而非敘述。

複製 `references/decision-log-template.tsv`（標頭行）以開始乾淨的紀錄。欄位：

- **ts.** ISO8601 時間戳。
- **phase.** 階段或工作流。
- **decision.** 選擇或完成的事，一行。
- **why.** 以簡單文字說明原因。若由原則驅動，請直接說明，而非使用專業術語標籤。
- **evidence.** 證明用的連結或路徑：commit SHA、PR 號碼、`file:line`，或 artifact、trace、screenshot 路徑。絕非一段文字。
- **result.** 結果或預測狀態：`tests green`、`reverted`、`pixel-diff 0`、`INCONCLUSIVE`、`open`。

一個簡單易懂的示例，讓審閱者一目瞭然。

```
ts	phase	decision	why	evidence	result
2026-05-24T09:02:00Z	frame	counted the work first, about 100 components and roughly 75 hours	wanted to know the size before starting a long run	commit 3a9f1c2	found 5 things to sort out before starting
2026-05-24T09:40:00Z	harness	took screenshots of the old version before changing anything	so we can compare old against new and catch any visual change	scripts/snapshot.sh, baseline/	saved 120 reference screenshots
2026-05-24T11:15:00Z	widget	moved the widget styles over without changing how it looks	keep the change small and the result identical	commit 7c21e0a, pixel-diff 0	looks identical, tests pass
2026-05-24T12:30:00Z	widget	threw out a helper's work because its screenshots were blank	checked the real files instead of trusting its summary	worktree reset	reverted, tightened the instructions for next time
```

<a id="logging-a-row"></a>

## 紀錄一行

寫下每個條目，如同告訴同事你做了什麼。簡單文字、具體行動，不使用 AI 語調或抽象專業術語（**unslop** 技能也適用於紀錄文字）。

使用輔助工具 `scripts/log.sh <logfile> <phase> <decision> <why> <evidence> <result>`。它會標記 `ts`，首次使用時寫入標頭，移除多餘 tab/換行，並將任何以 `=`、`+`、`-` 或 `@` 開頭的欄位前置單引號。單純的 `printf` 追加一行也可，但若欄位來自生成或使用者提供的文字，需注意同樣的位元組。

紀錄決策點和檢查點，而非每個動作：選擇的分岔、完成的單位及其驗證結果、轉向或回退及其觸發條件、出現的阻礙、修正的門。迴圈執行時，每個迭代一行。跳過瑣碎和顯而易見的內容。

一個執行是一個 agent 對話，包括其後續回合和任何摘要。一個接續、替代 agent 或新聊天開始新的執行。當一個執行新增到已有行的紀錄時，其第一行有 phase `start`，且在其後另一個執行寫入 `start` 行後的第一行也是如此。因此，一個執行在後續回合回到紀錄時，先讀取紀錄的最後幾行，確認是否有其他執行寫入。一個 `start` 行命名其之前未由此執行寫入的行的 `ts` 範圍，其證據命名此執行，例如其 agent id。phase `start` 僅用於此用途。

<a id="where-it-lives"></a>

## 存放位置

預設紀錄是工作 artifact，不提交。保留在工作目錄中的 `decisions.tsv`，或當多個工作同時執行時使用 `.audit/<task-slug>.tsv`，並不納入 git。

僅當工作規模足夠大，審閱者需要紀錄來信任結果時，才提交。

<a id="rules"></a>

## 規則

- 僅追加。錯誤的決策會新增一行取代它。絕不編輯或刪除歷史。
- 優先使用由已提交的指令碼產出的證據，而非手工一次性製作（**encode-lessons-in-structure** 原則技能）。

<a id="audit-the-log-against-the-transcript"></a>

## 審核紀錄與對話紀錄

執行結束時，在交回之前，檢查紀錄是否真實。在 active workspace 的 `agent-transcripts/` 目錄下讀取此執行の對話紀錄（系統提示命名路徑）。不要跨 `~/.cursor/projects/*/` 進行 glob。這會讀取無關的私人聊天。將此執行の行與實際發生的事件對照。每個連續段從此執行的一個 `start` 行開始，或若此執行建立紀錄則從第一行開始，並結束於另一個執行の `start` 行：

- 檢查每個行對應一個真實決策或行動。
- 檢查每個行的證據可解析並顯示該行所宣告的內容。
- 一個影響工作但未紀錄的分岔、轉向或放棄的方法是缺口。請新增。

修正紀錄，而非故事。審核絕不編輯或移除任何行，即使是虛構的。當一行記錄了非真實決策或行動，或其宣告或證據錯誤，新增一行取代它，記錄實際發生的事和可解析的指向。此審核不檢查此執行段之外的行。若此執行自身的工作顯示其中一行錯誤，如同任何錯誤決策，取代它。

<a id="cross-model-review-of-the-trail"></a>

## 跨模型審閱紀錄

在交回之前，啟動一個不同模型家族的 subagent，而非執行工作的模型。自我審閱不是替代。subagent 讀取審核紀錄和執行對話紀錄，然後標出使用者應注意的事項。不是重做工作，而是掃描次優或風險。根據 **poteto-mode** 技能的 Subagents 部分，選擇每個 subagent 的推理等級和快速或標準速度。

- 紀錄中證據薄弱或缺失的決策。
- 跳過或聲稱但無對話紀錄證明驗證步驟。
- 事後看起來風險高的選擇（過早、範圍擴張、掩蓋症狀）。
- 使用者粗略瀏覽時可能忽略的缺口。

每個產生紀錄的執行回應結束時包含「Attention」區段。以審閱者模型名稱獨立一行開頭（`reviewed by <model>`），然後列出每個標點指向特定行或時刻。「No flags」是有效值。模型名稱不是。

<a id="reviewing-the-trail"></a>

## 審閱紀錄

從上到下讀取，追隨證據指向，抽查。GitHub 將提交的 TSV 渲染為表格。`column -s$'\t' -t decisions.tsv` 在終端中渲染。

<a id="composing-this-skill"></a>

## 組合此技能

其他技能將審核紀錄路由至此，而非自行發明。以名稱引用並讓它擁有格式。不要重述欄位。
