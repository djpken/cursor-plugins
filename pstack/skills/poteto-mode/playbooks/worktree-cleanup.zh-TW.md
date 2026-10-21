<a id="worktree-and-simulator-cleanup"></a>

### 工作樹與模擬器清理

1. 建立快照並稽核。記錄 `df -h /`，然後執行 `scripts/worktree-audit.sh [-b <pr-base-ref>] [repo-path]`（原則：建立槓桿）。若沒有 `-b`，它會將 `MERGED` 與 `origin/HEAD` 進行檢查，因此在 PR 指向其他分支時，請傳入 `-b origin/<branch>`。它從 `git worktree list` 讀取路徑，絕不手動輸入，因為手動輸入的 `myrepo-worktrees/x` 會漏掉位於 `.cursor/worktrees/myrepo/x` 的專案（原則：將經驗編入結構）。它根據大小、年齡、合併狀態、未提交的工作、PR 狀態以及最近觸及該工作樹的聊天來分類每個工作樹，然後建議一個分類。轉錄掃描較慢，因此請將其放在背景執行。
2. 分類是建議，而非許可。固定的與正在進行中的聊天是真正的產物（原則：證明它可行）。從使用者或側邊欄取得該集合，並交叉檢查每個候選專案。槓桿已將 `safe` 標記為使用者固定的工作樹，因此固定集合優先。
3. 在刪除之前確認使用情況。對於每個 `verify-recent-chat` 行，或任何您懷疑的專案，派遣子代理讀取轉錄並報告該聊天是否固定或正在進行，以及它觸及哪些工作樹（原則：保護上下文視窗，轉錄是大量資料）。固定的聊天會透過背景子代理將 arena 與 repro 樹產生到同級工作樹中，這些樹即使名稱未出現在側邊欄，仍在使用中。根據子代理章節，為每個子代理選擇適當的推理等級與快速或標準速度。
4. 遇到不可回復的損失時暫停。`wip:N` 是 N 個已追蹤的未提交編輯。先顯示差異並取得決定，因為移除乾淨的工作樹可從其分支回復，但未提交的工作會消失。`scratch:N` 是未追蹤的拋棄專案，可安全刪除，但請列出檔案名稱。根據自主原則，乾淨、已合併且未使用的工作樹可繼續處理。`wip` 與正在使用的工作樹則暫停。
5. 清理已確認的集合。針對每個路徑執行 `git worktree remove --force <path>`。如果資料夾因忽略的建置產物而存活，則執行 `rm -rf`，然後執行 `git worktree prune`。分支參考會保留，因此不會丟失任何提交。以 `df -h /` 確認並重新列出。
6. 模擬器與其他回收專案。模擬器通常是第二大收益。執行 `xcrun simctl --set testing delete all`（XCTestDevices 複製）、`xcrun simctl delete unavailable` 與 `xcrun simctl runtime list`，然後執行 `runtime delete <id>` 清理舊的執行環境。如有需要更多：Xcode 的 `DerivedData` 與 `iOS DeviceSupport`、`~/Library/Application Support/Cursor`（`state.vscdb.backup`，以及 `snapshots/roots/<root>` 中，以您開啟為工作區的資料夾命名的 `<root>` 會膨脹）、套件快取（pnpm、uv、brew、yarn）。僅清除使用者未表示要保留的快取。

這是唯一會刪除使用者狀態且沒有程式碼審查來防止錯誤的劇本，因此上述門控就是審查。

**回應：** 在清理前與清理後顯示 `df -h /`，並顯示回收的空間、已清理的工作樹，以及每個保留專案的一行原因（由哪個聊天使用，或未提交的工作）。
