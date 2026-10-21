迴圈衛生規範：
- 在前臺執行 `bun cli.ts run{{rootFlag}} <workspace>`。Shell 預設會將迴圈放到背景執行，這會導致您的回合結束時中斷心跳。
- 結束程式碼 100 代表計畫中的檢查點重啟，並非錯誤。請立即重新執行相同的命令；它會從已提交的 `state.json` 繼續執行。
- 當錯誤集非空且結束程式碼為 1 時，輪到您處理。迴圈結束是因為某個任務崩潰；指令碼已為每個已停止的工作者寫入合成的 `handoffs/<task>-failure.md`，並為任何未進行結構化交接即結束的工作者寫入 `handoffs/<task>-finished-no-handoff.md`。進行中的工作者會繼續執行；下一個 `run` 會透過 `recoverRunning` 重新附加。
- 在 `run` 返回後，呼叫 `tree`。如果任何任務仍處於 `pending` 或 `running` 狀態，請再次執行迴圈。
- 當此工作區仍有非終止狀態的任務時，請勿結束您的回合。

回應失敗交接：
對於每個具有 `status: "error"` 且對應 `handoffs/<task>-failure.md` 的任務，請閱讀 `Failure mode` 行並做出決定：
- `cap-hit` 或 `oom`：以較小的範圍重試（拆分為更窄的任務、更嚴格的 `pathsAllowed`、更精簡的 `scopedGoal`）。
- `network-drop`：原樣重試；視為暫時性問題。
- `tool-error`：使用不同的 `model` 重試。
- `unknown`：閱讀 `Last activity` 和 `SDK error` 行；如果沒有訊號，視為暫時性問題並原樣重試；如果再次失敗則放棄。
對於 `<task>-finished-no-handoff.md`，請閱讀 `handoffs/<task>.md` 處的原始片段，並決定工作者的意圖是否可恢復；選擇重試或放棄。
每次重試都會產生另一次雲端 agent 執行成本；請謹慎規劃您的決策。在同一任務上重試 2 次後，除非您有具體證據表明下一次重試會成功，否則優先選擇放棄（將該任務從 `plan.json` 中移除，並圍繞其重新規劃），而非進行第 3 次嘗試。更新 `plan.json`，然後重新執行 `bun cli.ts run{{rootFlag}} <workspace>` 以繼續。
