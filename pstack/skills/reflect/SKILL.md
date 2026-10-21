---
name: reflect
description: "在活動轉錄稿上同時啟動三個平行的審閱子代理，挖掘學習成果，並將每個結果路由到現有技能的具體編輯。當使用者說 reflect 時使用。"
disable-model-invocation: true
---

<a id="reflect"></a>

# 反思

挖掘當前對話中的持久學習成果，然後將其路由到技能編輯。

當使用者說 "reflect" 或 "/reflect" 時啟動。當對話是瑣碎、離題或已由現有技能正確涵蓋時，跳過。一次性事件不是學習成果。

<a id="1-locate-the-active-transcript"></a>

## 1. 定位活動轉錄稿

在展開之前，在系統提示命名的 `agent-transcripts/` 目錄中找到此對話的轉錄稿。不要跨 `~/.cursor/projects/*/` 進行全域搜尋。這會跨越工作區邊界並讀取來自不相關專案的私人聊天。

```bash
ls -t <agent-transcripts>/*.jsonl <agent-transcripts>/*/*.jsonl <agent-transcripts>/*/subagents/*.jsonl 2>/dev/null | head -10
```

這涵蓋了舊的平面（`<id>.jsonl`）、當前巢狀（`<id>/<id>.jsonl`）和子代理（`<parent>/subagents/<child>.jsonl`）佈局。對於每個候選，讀取第一個 JSONL 行。選擇其 `message.content[0].text` 包含對話開場使用者提示的檔案。如果沒有匹配，則傳遞會話的緊湊摘要。

<a id="2-spawn-three-reviewers-in-parallel"></a>

## 2. 同時啟動三個審閱者

一條訊息，三個 `Task` 呼叫，`subagent_type: generalPurpose`，代理模式（`readonly: false`）。審閱者需要 MCP 存取來查閱轉錄稿所參考的票、聊天執行緒和可觀察性追蹤，而 readonly 會移除 MCP。

每個審閱者和綜合者在 `pstack-models.mdc` 規則中命名一個角色行和一個預設值。將 `model` 設定為該行的值，或如果規則或行缺失則使用預設值。當值為 `auto` 或 `inherit-parent` 時，不設定 `model`。如果 Task 工具拒絕一個 slug，使用預設值並說明。如果它拒絕預設值，則從其錯誤訊息中使用同家族最接近的有效 slug。根據 **poteto-mode** 技能的 Subagents 部分，選擇每個子代理的推理等級和快速或標準速度。

| 鏡頭 | 角色行 | 預設 `model` | 提示模板 |
|---|---|---|---|
| 判斷 | `reflect judgment, divergent, synthesizer` | `claude-opus-5-5-xhigh` | `references/judgment-reviewer.md` |
| 工具 | `reflect tooling` | `grok-4.7-xhigh-fast` | `references/tooling-reviewer.md` |
| 分歧 | `reflect judgment, divergent, synthesizer` | `claude-opus-5-5-xhigh` | `references/divergent-reviewer.md` |

逐字傳遞每個模板，在標記處替換轉錄稿路徑或摘要。審閱者在 `Task` 回應體中返回結果。

<a id="3-synthesize"></a>

## 3. 綜合

一個 `Task` 呼叫，`subagent_type: generalPurpose`，從 `reflect judgment, divergent, synthesizer` 行的 `model`（預設 `claude-opus-5-5-xhigh`），代理模式（`readonly: false`）。它透過 MCP 抽查引用，而 readonly 移除 MCP。逐字傳遞 `references/synthesizer.md`，在標記處內嵌每個審閱者的完整輸出。它返回一個 Accepted / Rejected / Backlog 列表。

<a id="4-structural-enforcement-check"></a>

## 4. 結構性強制檢查

將任何 Accepted 專案移動到 Backlog，如果 lint 規則、指令碼、metadata 標旗或 runtime 檢查能更可靠地強制執行。參見 **encode-lessons-in-structure** 原則技能。

<a id="5-apply"></a>

## 5. 應用

呈現綜合者的完整 Accepted / Rejected / Backlog 輸出，並在應用任何 Accepted 編輯之前等待明確批准。使用者選擇子集並可能重新導向路由。技能更改影響組織中每個未來代理。不要自動應用。

將每個 Backlog 專案立即提交到團隊的 devex 或 backlog 追蹤器，無需等待。只有 Accepted 列表等待批准。

嚴格遵循每個批准行的 Routing：

- 瑣碎現有技能編輯（一行 bullet、緊縮的句子、修正過時事實）：父代理直接執行。
- 實質性現有技能編輯（新節、新模式表、超過約 10 行）：交給 Cursor 的內建 `create-skill` 技能並執行其 draft / test / iterate 迴圈。
- `tune description: <skill path>`（技能存在但未在應觸發時觸發）：交給 `create-skill` 並執行其描述最佳化迴圈。
- `new skill via create-skill: <kebab-name>`：交給 `create-skill` 進行建立。不要隨意發明形狀。

如果你的環境提供 SKILL.md 驗證器，在每個觸及的技能上執行它，然後才宣告完成。

<a id="6-summarize-for-the-user"></a>

## 6. 為使用者總結

簡短列表，無前言：

- 已應用的編輯：`<skill path>`。每個一行說明變更。
- 新建立的技能：`<skill path>`。每個一行（罕見）。
- 已提交到 devex 追蹤器的 Backlog：`<issue title>`（`<tags>`）。每個一行。
- 已丟棄：每個拒絕結果一行 + 綜合者的原因。
