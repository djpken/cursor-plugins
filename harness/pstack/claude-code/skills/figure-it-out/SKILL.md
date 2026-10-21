---
name: figure-it-out
description: "當沒有更精確的 playbook 適用時，設計一個可稽核的 playbook：大型遷移、雄心勃勃的多部分變更，或人類在離開後再審查的工作。根據任務調整嚴格程度，執行假設迴圈，並記錄決策。用於 /figure-it-out 或「自行研究」。"
disable-model-invocation: true
---

<a id="figure-it-out"></a>

# 自行研究

In Claude Code, read [the Claude Code adapter](../../docs/harness/claude-code.md) once per session before you follow this skill. It maps the Cursor names in pstack skills to Claude Code.

當任務不符合任何 playbook 時，設計一個。在任何程式碼之前的交付物是工作流程本身：一系列階段，根據任務調整嚴格程度，執行科學方法，並留下人類在離開後可稽核的決策軌跡。

<a id="start"></a>

## 開始

開啟一個 todolist，其第一個專案是閱讀 **poteto-mode** skill 的 Principles 部分。然後將以下階段新增為 todos。

<a id="phase-a-frame"></a>

## 階段 A：框架

先建立基礎，再做出承諾。在你能陳述以下內容之前，不要開始執行：

- 完成定義，作為可證偽的謂詞（**prove-it-works** 原則 skill）。
- 範圍，量化：粗略的單位與工作量，加上建立基礎時發現的阻礙因素。
- 嚴格程度，傾向較高。單向門和高影響範圍的變更需要更高的嚴格程度。可逆的低風險步驟需要較低的嚴格程度。嚴格程度是閘門與產出物，而非「更努力嘗試」。

在承諾長時間執行之前，呈現框架與權衡。可逆的工作繼續進行（**never-block-on-the-human** 原則 skill），但數小時的執行值得設一個檢查點。

<a id="phase-b-design-the-workflow"></a>

## 階段 B：設計工作流程

分解為原子的、可獨立交付的單位。依風險最高的未知事項優先排序。在功能之前建立骨架與驗證（**foundational-thinking** 原則 skill）。

- 在工作之前建立驗證框架，並從變更前的狀態擷取基準值，以便檢查讀取為「舊值與新值」。
- 對於單向門的設計決策，執行 **architect** skill（它會執行 **arena**）。對於形狀已具體的機械性工作，跳過此步驟。對已確定的設計進行第二次 arena 是過度設計（**laziness-protocol** 原則 skill）。
- 決定哪些部分可以並行展開。僅在縫隙處並行化，並為每個 worker 提供自己的 worktree 或 branch（**separate-before-serializing-shared-state** 原則 skill）。不要過度並行。根據 **poteto-mode** skill 的 Subagents 部分，為每個 subagent 選擇適合該工作的推理層級與快速或標準速度。
- 將設計好的階段清單寫下來。該清單就是人類審查的內容。

然後執行設計。將其步驟作為具體專案新增到 todolist 中，位於階段 C 與階段 D 專案之間。在階段 C 的迴圈紀律下執行每個步驟，並穿插階段 D 的記錄，每當一個步驟完成時就新增一行，而不是將整個軌跡留到最後。

<a id="phase-c-run-the-loop"></a>

## 階段 C：執行迴圈

每個單位都是一個實驗。陳述假設，做出最小變更，針對真實產出物上的謂詞進行測量，如果有所進展則保留，如果沒有則還原。應用 **sequence-verifiable-units** 原則 skill，在開始下一個單位之前驗證每個單位，而不是在最後批次檢查。

- 透過檢查產出物來驗證，絕非自我報告。當某事透過得太容易時，先懷疑觀察方法，而非系統。
- 將委託的工作與裁判配對。如果 worker 作弊透過閘門，重置並強化合約。如果閘門本身錯誤，在變更中修正閘門，而不是繞過它。
- 判決結果為 VERIFIED、NOT VERIFIED 或 INCONCLUSIVE。INCONCLUSIVE 不算透過。不要隱藏負面結果。

<a id="phase-d-keep-the-audit-trail"></a>

## 階段 D：保留稽核軌跡

透過 **show-me-your-work** skill 記錄執行過程。figure-it-out 的工作通常足夠雄心勃勃，值得提交軌跡，以便審查者可以在 PR 中閱讀。軌跡加上 diff 讓人類回來後能信任該工作。

<a id="phase-e-verify-and-hand-back"></a>

## 階段 E：驗證並交還

針對真實產品，而非僅是框架，檢查整體是否符合階段 A 的謂詞。將任何重複的修正編碼為閘門、lint 規則、檢查或指令碼（**encode-lessons-in-structure** 原則 skill）。

**回覆：** 你設計的 playbook、嚴格程度及其原因、決策軌跡路徑、已針對謂詞驗證的內容，以及仍未解決的問題。
