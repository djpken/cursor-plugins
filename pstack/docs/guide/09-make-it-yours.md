<a id="make-it-yours"></a>

# 打造屬於你的風格

poteto-mode 是某個人的風格。其背後的 playbooks、routing 和 model roles 同樣適用於你的風格。本頁涵蓋如何產生個人模式、從 session 中捕捉經驗教訓、修正 repo 以讓 agents 停止重複犯錯、撰寫專注的 skill，以及在信任 skill 變更之前進行測試。

從比你想像中更小的規模開始。第一天不需要很多 skills，甚至不需要整個 plugin。用直白的方式下 prompt，觀察 agents 在哪裡失敗，當同一個失敗出現兩次時，再新增一個 skill 或檢查。

<a id="generate-your-own-mode-with-automate-me"></a>

## 使用 `/automate-me` 產生你自己的模式

```text
/automate-me
```

你不需要描述你的風格，因為 [`/automate-me`](../../skills/automate-me/SKILL.md) 會從你的歷史記錄中讀取。它會挖掘 active workspace 中近期的 transcripts，找出重複的偏好，包括你喜歡的回覆方式、delegation、verification、code、prose 和 process，然後詢問你哪些模式真正代表你。它透過 Cursor 內建的 `create-skill` 流程起草 `.cursor/skills/<your-name>-mode/SKILL.md`，將草稿透過 [`/unslop`](../../skills/unslop/SKILL.md) 執行，並從 worktree 開啟一個 PR，讓你能像審查其他變更一樣審查它。

當你的習慣發生變化時，隨時再次執行：

```text
/automate-me update my mode skill with everything since its last edit
```

Update mode 只會挖掘自 skill 上次變更以來的歷史記錄。它會保留你未反駁的規則，修訂有新證據的規則，並僅為新模式新增章節。

<a id="capture-a-sessions-lessons-with-reflect"></a>

## 使用 `/reflect` 捕捉 session 的經驗教訓

在一個讓你學到東西的任務結束後，立即執行：

```text
/reflect that took way too long. capture what we learned so the next run doesn't repeat it.
```

[`/reflect`](../../skills/reflect/SKILL.md) 會將 transcript 傳送給三個並行的 reviewers，然後由 synthesizer 將建議分類為 `Accepted`、`Rejected` 和 `Backlog`，並在任何 skill 變更前等待你的批准。只有當建議會改變未來的決策時，才批准該建議。一個奇怪的 session 只是軼事，不是規則。

<a id="fix-the-environment-with-correct"></a>

## 使用 `/correct` 修正環境

當你不斷糾正 agents 犯同樣的錯誤時，修正應該在 repo 中，而不是在你的下一個 prompt 中。根據其持續效果對選項進行排名：

1. 透過架構或更好的資料結構讓錯誤變得不可能。
2. 透過 types 阻擋它，或使用一個錯誤訊息能指出修正方法的 lint 或 CI 檢查。
3. 透過測試捕捉它。
4. 將其記錄為檔案或 agent 規則。當 agent 跳過規則時不會有任何失敗，所以這是最後的手段。

人工審查不在清單上。必須在每個 PR 中捕捉同一個錯誤的 reviewer 正是此問題要解決的。[`/correct`](../../skills/correct/SKILL.md) 執行此工作：

```text
/correct agents keep calling the database client directly instead of going through the repository layer
```

它讀取近期的 commits、reverts、review comments 以及解釋 workaround 的 comments，然後將錯誤分組為類別。一個類別在發生兩次後才算數。它會逐一 commit 修正最頻繁的類別，在可行的最高層級進行修正，並證明每個新檢查在真實的過去錯誤上會失敗。它還在 agent instruction file 中維護一個表格，將每條規則與其執行機制配對，因此沒有執行機制的規則會顯示為重複問題。回覆會列出每個類別及其證據、選擇的層級，以及為什麼更高層級不起作用。

無引數執行時，它會自行從歷史記錄中找出類別。`/reflect` 和 `/correct` 分工合作。`/reflect` 從單一 session 改進 skills。`/correct` 變更 repo 以確保錯誤類別不會再次出現。當修正是新的邊界時，將其與 `/architect` 配對。[Run many projects in parallel](./07-overnight.md#run-many-projects-in-parallel) 有一個 prompt 可以跨整個 repo 執行這兩者。

<a id="author-a-focused-skill"></a>

## 撰寫專注的 skill

當你已經知道想要捕捉的工作流程時：

```text
/poteto-mode write a skill for verifying database migrations in this repo
```

撰寫 skill 符合 [Authoring or modifying a skill playbook](../../skills/poteto-mode/playbooks/authoring-a-skill.md)，它透過 Cursor 內建的 `create-skill` 路由，驗證 frontmatter 和連結，並透過 Opening a PR playbook 交付結果。面向 agent 的散文標準高於面向人的散文，因為一句無用的句子會變成某個未來 agent 遵循的指令。讓 playbook 維持這個標準，而不是手動撰寫 `SKILL.md`。

兩種特殊情況有各自的 generators。一個必須驅動你的 app 並證明行為的 skill 是 verification skill，因此請使用 [`/create-verification-skill`](../../skills/create-verification-skill/SKILL.md) 和 [`/maintain-verification-skill`](../../skills/maintain-verification-skill/SKILL.md)。一個使用 model checker、proof 或其他 verifier 檢查你的 code 屬性的 skill 是 formal verification skill，因此請使用 [`/create-formal-verification-skill`](../../skills/create-formal-verification-skill/SKILL.md) 和 [`/maintain-formal-verification-skill`](../../skills/maintain-formal-verification-skill/SKILL.md)。[Verify and ship](./06-verify-and-ship.md#prove-a-property-with-a-formal-verification-skill) 涵蓋這兩種型別。

<a id="write-docs-to-a-standard-with-technical-writing"></a>

## 使用 `/technical-writing` 撰寫符合標準的檔案

Skills 不是唯一交付的散文。對於檔案、RFCs、readmes、PR descriptions 和 commit messages：

```text
/technical-writing review the readme changes
```

[`/technical-writing`](../../skills/technical-writing/SKILL.md) 應用分層標準。其目標是讓疲憊的工程師在第一次閱讀時就能理解。它首先選擇檔案的模式（tutorial、how-to、reference 或 explanation），然後逐句處理：誰做什麼，每句一個想法，沒有兩種讀法。使用它來審查你或 agent 剛寫的內容，或在請求檔案時提前指定它。

<a id="test-a-skill-change-blind"></a>

## 盲測 skill 變更

Skill 編輯會影響每個未來的 session，因此像實驗一樣測試它。採用別人的 skill 也是如此。在保留它之前，檢查它是否讓 agent 在你的工作上表現更好。

```text
/poteto-mode run the eval playbook on this skill change. same task for both variants, candidates stay blind.
```

當一個 skill 持續未命中且你知道它應該做什麼時，在一個任務中變更並測試它：

```text
/poteto-mode update the review skill so it flags missing migrations, and eval the change.
```

提前要求 eval 能保持編輯的誠實。從一個糟糕的 session 寫出的修正傾向於過度擬合該 session，經過多次編輯後 skill 會漂移。Eval 會在交付前捕捉漂移。

[Eval playbook](../../skills/poteto-mode/playbooks/eval.md) 圍繞一個失敗模式建立，即觀察者效應。知道自己在被評估的 agent 行為會不同。因此候選 agents 獲得看似自然的任務，在 sanitized directories 中，絕不使用 "eval" 或 "candidate" 這些詞，也絕不知道彼此的存在。一個 judge 在中立標籤下為所有輸出評分，chain-following 的評分基於每個候選實際讀取了哪些檔案，而不是基於其聲稱的內容。

在接受裁決前，親自閱讀每個輸出。如果你不同意 judge，先懷疑 rubric，再懷疑你的判斷。

<a id="build-a-bot-ui-with-make-bot-ui"></a>

## 使用 `/make-bot-ui` 建立 bot UI

一個情境 skill 是為 Grok Bot 使用者設計的。[`/make-bot-ui`](../../skills/make-bot-ui/SKILL.md) 建立一個小頁面，其按鈕透過 webhook routine 喚醒 bot。例如，你可以滑動瀏覽 review queue，並讓每次滑動要求 bot 對該專案採取行動。你機器上的伺服器持有 webhook 的 sender key，因此 key 永遠不會到達 browser 或 chat。該 skill 也涵蓋在 Tailscale 上公開頁面。

**陷阱：** 不要在任務進行中因為 skill 行為異常而編輯它。請在獨立的 PR 中修正它，並讓任務繼續推進。與功能開發混在一起的 skill 修改對審查而言是隱形的，且無法評估。

下一步：[Recipes and pitfalls](./10-recipes-and-pitfalls.md).
