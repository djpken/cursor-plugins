<a id="opening-a-pr"></a>

### 開啟 PR

在其他每個 playbook 結束時呼叫。

**工作樹。** 從 main 分支的 git 工作樹進行工作。子代理會繼承它。同一分支上的多次 `Task` 呼叫各自獲得自己的工作樹，或在其間使用 `git fetch && git reset --hard origin/<branch>`。有不相關工作的髒分支：先移除 patch，建立全新工作樹，再套用。糾結的工作樹：從 main 重置，最小化重做。

**提交。** 自由提交。在開啟 PR 之前，重組為小而有序的提交。每個提交是未來的一個 PR：可落地、有序地敘述故事。當修正屬於剛建立的提交時，進行 amend。當可獨立時，建立新提交。提交內容不重述主題。

**PR。** 在提交前從 `cursor-team-kit` 執行 `/deslop` 對 diff，並在審閱前執行 `/no-comments`。用 `/technical-writing` 撰寫每個 PR 標題、描述和提交內容（除 Diátaxis 以外的每一層，並套用 STE 詞彙規則：每個動作一個詞，保留冠詞，在可行時使用簡單動詞而非 `-ing`），然後套用 `/unslop`。

**標題。** Conventional Commits `type(scope): subject`，無結尾句號。型別如 `feat`、`fix`、`docs`、`refactor`、`test`、`chore` 或 `perf`。範圍是領域，如 `pstack` 或 `poteto-mode`。主題是簡短、命令式，並在變更涉及真實符號時命名它，例如 `fix(pstack): retarget opening-a-pr babysit trigger`。

**描述。** PR 內容是一分鐘簡報，不是實驗記錄，針對擁有 diff 的審閱者。它說明變更為何存在、遺漏什麼、可能破壞什麼，以及如何證明它運作。寫簡短、簡單的句子，少用識別符，避免冗長文字。內容成為 squash 提交內容，因此保持該提交約 40 行以內。使用 `##` 標題，而非粗體前置，按此順序，並刪除無內容的段落：

- `## Why`：問題與方法，1–3 個簡短句子。無 SHA、rebase 譜系或「基於 main」前言。
- `## What changed`：1–3 個簡短專案。僅當變更涉及真實符號或路徑時命名它。命名重新命名或重新目標的兩側。
- `## Scope`：始終。1–3 個簡短專案，說明它涵蓋什麼及刻意遺漏什麼（後續事項、已知缺口）。無符號、路徑或逐檔案說明。
- `## Tradeoffs`：僅審閱者會詢問的已拒絕替代方案。當沒有實際選擇時省略。
- `## Blast Radius`：1–2 個句子，說明它影響誰或什麼，以及為何安全或風險。若 main 是紅色，說明保持紅色成本。
- `## Verification`：1–3 個專案，每個是實際執行路徑及其結果。效能：一個主要數字及其單位、`before → after`，以及連結到 arena 或 swarm 目錄的其餘資料。無樣本大小方法、swarm 詳述或指標表格。

在段落之後，當影片或截圖證明宣告時附上。完整 SHA、swarm 或 arena 跑道詳述、槓桿修正文章、逐檔案檢查清單及「CLEAN」結論放在連結的附件，而非內容。

**Forge。** 在第一個 PR 操作之前決定 forge，並在建立、編輯、檢視、監看和合並時保持該選擇。GitHub CLI（`gh`）是預設。若 `command -v origin` 成功且 Origin 能解析儲存庫，優先使用 `origin pr ...`。若 Origin 不存在或無法解析儲存庫，留在 `gh` 並記錄後備方案。不要求 Graphite（`gt`）。

**內建 PR 工具。** 當執行提供內建 PR 工具，透過它建立、編輯、重新目標和標為 ready，絕不透過 forge CLI。CLI 建立的 PR 會遺漏工具追蹤的資訊。使用已決定的 forge 處理工具未涵蓋的一切，以及當執行無此工具時的所有 PR 操作。

**大小與堆疊。** 偏好五個窄 PR 而非一個大 PR。堆疊是基底分支鏈。根 PR 目標 trunk。每個子分支 rebase 到父分支的精確尖端，其 PR 目標父分支。當父分支合併時，依 `playbooks/shipping.md` 步驟 4 將其子分支移到 trunk。無內建 PR 工具時，依已決定的 forge，用 `origin pr create --status open --base <parent-branch>` 或 `gh pr create --base <parent-branch>` 建立子分支，並用 `origin pr edit <pr> --base <parent-branch>` 或 `gh pr edit <pr> --base <parent-branch>` 重新目標。僅獨立工作從 trunk 分支。在大量堆疊工作前 rebase 到 trunk。

**準備狀態。** 開啟每個 PR 為 ready，絕不作為 draft。內建 PR 工具可能預設為 draft，因此透過它每次建立呼叫設定 `draft: false`。使用 Origin，傳遞 `--status open`。使用 `gh`，省略 `--draft`。若 PR 仍開啟為 draft，透過 PR 工具標為 ready，或依已決定的 forge 執行 `origin pr ready <number>` 或 `gh pr ready <number>`。在提及 PR 狀態之前執行 `origin pr view <number>` 或 `gh pr view <number>`。

**監看。** 開啟 PR 不開始監看。貼上 URL 並繼續建構。先完成階段或堆疊。僅當使用者在整個堆疊存在後要求時，執行單獨的監看階段。每個新 PR 的監看會拖延建構並消耗檢查，後續波次會重啟。當反饋偏離意圖時，提出反對。

開啟 PR 的子代理執行 `interrogate`、`/deslop` 和 `/no-comments`，並貼上 URL。它返回父代理而不監看，除非它是 Autopilot-full 或 Autopilot-stack 所有者。該所有者簡報指派監看迴圈，並是 `playbooks/babysit.md` 等待的請求。所有者在其 code-ready 報告後開始迴圈，依其 playbook 報告 merge-ready 或 STACK-READY，並豁免於此處及 `playbooks/babysit.md` 中將監看延至整個堆疊建成的規則。
