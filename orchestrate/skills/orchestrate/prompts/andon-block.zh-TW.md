
### Andon
停止整個樹狀結構中的新任務生成。只有在具體證據顯示持續生成會產生垃圾時才應觸發：上游輸出錯誤且下游任務將因此失敗、驗證器級聯顯示接受判斷錯誤、或驗證或基礎設施無法恢復。任務遇到自身障礙屬於 `Status: blocked` 交接，而非 Andon。

  bun <path-to-orchestrate>/scripts/cli.ts andon raise --reason "<why>"{{agentIdFlag}} --workspace <workspace>

`--reason` 為必填項，並會發布到執行緒中，讓樹狀結構能檢視編排暫停的原因。此反應是子任務輪詢的低成本閘門。`--agent-id` 會新增一個頁尾連結，指向觸發此操作的 agent。
