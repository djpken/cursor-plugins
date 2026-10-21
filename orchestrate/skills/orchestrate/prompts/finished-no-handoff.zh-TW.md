<!-- orchestrate finished-no-handoff
task: {{taskName}}
branch: {{branch}}
agentId: {{agentId}}
runId: {{runId}}
resultStatus: {{resultStatus}}
terminatedAt: {{terminatedAt}}
-->

<a id="taskname-finished-without-handoff"></a>

# {{taskName}} 已完成，但未進行交接

狀態：{{resultStatus}}（雲端 agent 已正常結束，但從未寫入 `## Status` 交接）
雲端 agent：{{agentId}}
執行：{{runId}}
分支：{{branch}}
終止時間：{{terminatedAt}}

<a id="suggested-next-steps"></a>

## 建議的後續步驟
- 檢視 `handoffs/{{taskName}}.md` 中的原始交接內容，以檢視 worker 實際輸出的內容。
- 如果這看起來像是提示詞失誤（worker 產生了散文，但未產生結構化模板），請原樣重試。
- 放棄：跳過此任務，如果目標確實沒有可接受的輸出，請重新規劃以繞過它。{{rawSnippetBlock}}
