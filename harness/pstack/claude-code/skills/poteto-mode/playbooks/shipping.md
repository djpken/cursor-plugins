<a id="shipping"></a>

### 運送

**你擁有落地內容。獨立驗證每個 PR，僅從根部落地已驗證的執行，然後保持不干涉堆疊。**

1. **先解決 forge，然後獨立驗證每個 PR 才能落地。** GitHub CLI (`gh`) 是預設。如果 `command -v origin` 成功且 Origin 能解析儲存庫，則對每個 PR 操作使用 `origin pr`。否則停留在 `gh` 並記錄回退。絕不要求 Graphite (`gt`)。每個 PR 一個驗證者，每個都是 Cursor 雲端代理，以使用者方式執行變更並搭配相符的控制技能（例如來自 `cursor-team-kit` 的 `control-ui` 或 `control-cli`），以 head 為基準比較，並在該 PR 上發布 `PASS`、`PASS+NOTES` 或 `FAIL`。只有未撰寫該程式碼的代理的判定才算有效，因為作者檢查的是它打算建構的內容。綠色 CI 和贊成的 bot 審查不算判定，因為這兩者都不會以使用者方式執行變更。
2. **僅落地從底部連續的已驗證執行。** 從最低的未合入 PR 向上走，並停在第一個沒有 `PASS` 或 `PASS+NOTES` 的 PR。其下方的 PR 構成執行，最上方的是天花板。位於未驗證 PR 上方的已驗證 PR 會等待，因為它無法在下方程式碼未落地時落地。其分支既不持有也不需要該程式碼的 PR 可以移到 trunk 並自行落地。它需要先取得新的判定，因為舊判定是在該程式碼位於其下方時執行，而 trunk 沒有該程式碼。
3. **重新檢查判定仍描述當前程式碼。** 判定涵蓋一個 patch，而鏈 rebase 會將其上每個 PR 移到新的 SHA，且不會使任何檢查失敗。在信任較舊判定之前，比較判定 SHA 時的 `git patch-id` 與當前 head。當 patch 僅在測試、docs 或 lint 設定上不同，一個 lane 的結果仍成立，如果它所執行內容在兩個 SHA 上建構相同，除噪音外。噪音是差異也會出現在判定 SHA 的兩次建構之間，或嵌入的 commit SHA。報告每種噪音及其檔案，讓讀者可以檢查判斷。變更本身仍會獲得新的檢查和審查，因為舊判定從未看到它。沒有建構輸出的 lane（例如 dev server）無法顯示此情況，因此重新執行它，並重新驗證任何其他漂移的專案。
4. **自下而上落地，一次一個 PR。** 始終合入執行中最低的未合入 PR，使用 `origin pr merge <pr>` 或 `gh pr merge <pr> --squash`。在 Origin，普通命令也會合入你指定的 PR 下方的所有 PR；在 GitHub，base 仍是其父分支的 PR 會合入該分支，而非 trunk。讀回 PR 以確認結果（`origin pr view <pr>` 或 `gh pr view <pr> --json state,mergedAt,baseRefName`），因為合入命令可能報告成功，但合入佇列僅排入 PR，而請求的 auto-merge 不算合入。已落地的 PR 讀為已合入且 base 為 trunk，而非其父分支。僅在判定涵蓋其當前 head 的 PR 上啟用 `--auto`（Origin merge-when-ready 或 GitHub auto-merge），因為它會落地任何 ready 的 head。合入落地後，將 base 為已合入分支的 PR 重新指向 trunk（`origin pr edit <pr> --base <trunk>` 或 `gh pr edit <pr> --base <trunk>`），將其 patch 移到合入結果上，並用 `--force-with-lease` 推送。判定涵蓋 patch，而非其 commits，因此步驟 3 的 patch-id 規則決定判定是否存活。一旦其檢查透過且判定存活，就合入它。
5. **在落地期間保持不干涉堆疊。** 步驟 4 中移動下一個 PR 是唯一對分支或 base 的變更，因為合入會落地其分支和 base 在那一刻持有的內容，無論判定與否。在 GitHub，使用來自 `playbooks/babysit.md` 的 queued watcher（`scripts/watch-pr/watch-pr --queued-stack --stack-prs <bottom-to-top>`）在 `/loop` 下監看執行，直到它報告天花板上的 `COMPLETE`。在 Origin，每次 `origin pr checks <pr> --watch` 返回時用 `origin pr view <pr> --checks` 重新讀取當前 PR，直到天花板讀為已合入。當其父合入時 base 移到 trunk 是預期行為，不是需要修復的損壞。如果合入停滯，先找出原因，再變更任何專案。
6. **停在天花板。** 延伸執行需要新的判定，因此這是重新透過步驟 1。

**回應：** 已驗證的執行及其天花板、每個 PR 的判定及產生者、已落地內容及確認方式，以及下一缺口需要什麼。
