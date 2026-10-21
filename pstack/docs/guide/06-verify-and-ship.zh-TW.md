<a id="verify-the-result-and-open-a-pr"></a>

# 驗證結果並開啟 PR

「它編譯成功」並非證據。[Prove It Works 原則](../../skills/principle-prove-it-works/SKILL.md) 要求 agent 在回報成功之前，先檢查實際產出物，而你的工作是讓「實際產出物」變得可檢查。本頁涵蓋如何陳述完成條件、審核測量數值、為你的應用程式產生驗證 skill、以正式驗證確認測試很少觸及的屬性、開啟 PR，以及推動其合併。

在大多數 agent 工作中，驗證是最慢的步驟，因為這通常是等待人類介入的環節。讓 agent 能夠執行驗證，你就不再是瓶頸。若跳過驗證，執行更多 agent 只會讓你累積更多未經檢查的工作需要審查。

![A prototype plane flies a real test course while she times it with a stopwatch and robots film and checklist the run; the terminal reads verify: pass, evidence: captured.](./images/verification.jpg)

<a id="state-the-finish-condition-up-front"></a>

## 事先陳述完成條件

在第一個提示中說明「完成」的定義，使用任何合適的措辭：

```text
/poteto-mode add json output to this command. text output stays byte-identical, the json parses, both run against the sample project. show me the evidence.
```

現在 agent 有三項可執行的檢查，而不是一種需要滿足的情緒。當回覆傳回時，應包含確切的指令與輸出。若某項檢查無法執行，良好的回覆會標註「無法確定」。沒有證據卻充滿自信的回覆是警訊。

讓檢查對應變更內容：

- CLI 變更執行實際指令。
- UI 變更在執行中的應用程式中走過變更的流程。當必須與參考畫面逐畫素比對時，[Visual parity playbook](../../skills/poteto-mode/playbooks/visual-parity.md) 會將截圖與凍結的基準進行差異比對，而非憑肉眼判斷。
- 解析器或遷移重放已儲存的輸入。
- 效能變更比較變更前後的剖析結果。
- 儲存變更讀回已寫入的值。

要求以你可自行檢視的產出物作為證明：失敗的測試與隨後透過的測試、變更前後影片、追蹤記錄、截圖。若修正已合併，請要求在 main 分支上再次執行相同的檢查。產出物勝過看似合理的解釋，因為你可以挑戰它，而無需重放整個執行過程。

對於你不完全信任的小型 diff，[`/blast-radius`](../../skills/blast-radius/SKILL.md) 會找出它可能在其他地方造成的破壞。它會找出使變更安全的那個事實，並透過執行程式碼來證明該事實，而非透過論證。

<a id="vet-a-measured-number-with-benchmark-checklist"></a>

## 使用 `/benchmark-checklist` 審核測量數值

變更前後的數值是最容易意外出錯的證據。暖快取、單側的除錯建置，或從未在計時區域內執行的工作，都可能產生令人信服的加速效果。在回報或依據數值採取行動之前，請輸入：

```text
/benchmark-checklist vet the export speedup before it goes in the pr
```

[`/benchmark-checklist`](../../skills/benchmark-checklist/SKILL.md) 會提出七個問題，並要求針對每個問題提供來自執行的證據：

1. 什麼限制了該數值，為什麼它不是雙倍？
2. 每一側的執行是否都按照生產環境的方式進行調校？
3. 結果是否違反物理限制，例如磁碟頻寬或核心數量？
4. 是否有錯誤發生或回傳錯誤輸出？
5. 在交替執行中是否可重現，並包含中位數與範圍？
6. 在端到端層面，於使用者等待的路徑上，這是否重要？
7. 工作是否實際發生在計時區域內？

判定結果會回傳為更快、更慢、無可測量差異，或無法確定，並附上執行次數、範圍與限制因素。當無法指名限制因素，或某一側未調校時，它會標註為無法確定。`/poteto-mode` 已在 Perf issue 與 Hillclimb playbook 內部執行此檢查清單，因此當你在這些範圍外測量了某些東西，或當他人的數值看起來太好時，你需自行輸入。這是 [Explain the Number 原則](../../skills/principle-explain-the-number/SKILL.md) 的實務形式。

<a id="create-a-project-verification-skill"></a>

## 建立專案驗證 skill

上述 UI 專案隱藏了一個真實需求。Agent 需要一種指令碼化的方式來驅動你的應用程式。如果你的專案已有此機制，很好。若沒有，請執行：

```text
/create-verification-skill
```

[`/create-verification-skill`](../../skills/create-verification-skill/SKILL.md) 會訪談儲存庫，而非你。它會釐清使用者會觸碰什麼、應用程式如何在本地啟動、什麼可以驅動它（優先使用現有的 harness，否則使用 browser 與 CDP、PTY，或純 HTTP）、什麼證據能證明行為，以及兩個執行個體是否能並行執行。它只詢問程式碼無法回答的問題。

它會撰寫 `.cursor/skills/verify-<app>/`，這是面向 agent 的指令，包含確切的 Launch、Doctor、Drive、Evidence 與 Cleanup 章節，以及針對其隨附指令碼的 Helpers 章節，還有位於 `features/` 下的功能地圖，索引應用程式的功能以及證明每個功能運作的結果。該 skill 隨附一個 [worked feature-map example](../../skills/create-verification-skill/references/feature-map-example/)，包含 README 索引以及每個功能一個檔案，使用四個必需的 H2。在交付之前，產生器會端到端證明該 skill 一次：啟動、doctor 檢查、驅動一個功能、擷取證據、清理，然後確認證據在清理後仍存在。若該證明失敗，請勿使用輸出。

從那時起，「在應用程式中驗證」成為任何 agent 都能在此儲存庫中執行的步驟，無需設定對話。當你需要特定形式的證明時，在提示中指名它：

```text
/poteto-mode build the bulk-archive action. use /verify-<app> to verify your changes and show me a video and screenshots as proof.
```

```text
/poteto-mode repro this with /verify-<app>. if it repros on main, fix it and show me a video as proof.
```

一旦驗證 skill 運作正常，[`/swarm`](../../skills/swarm/SKILL.md) 可以按功能地圖專案拆分完整透過並彙總結果。一群驗證器也能在足夠大的樣本上確認效能提升，或在 PR 發布前對應用程式進行模糊測試以偵測回歸。

將驗證 skill 視為基礎設施，而非一次性產物。提交它，讓團隊中的每個人和每個 agent 都以相同方式驅動應用程式。然後 [build the lever](../../skills/principle-build-the-lever/SKILL.md)。當 agent 不斷撰寫一次性指令碼來點選應用程式時，要求一個小型控制 CLI 供 skill 呼叫。Agent 消耗的 token 較少，且每次執行都可重現。Agent 善用 CLI 的特徵包括：

- 少數可組合的指令，每個執行實際工作，而非許多細薄的指令。
- 任何破壞性操作都有 `--dry-run` 選項。
- 子指令逐步揭示功能，而非一次全部顯示。
- 錯誤訊息說明該如何做。
- 豐富的 `--help` 文字。
- 機器可讀的輸出，例如 JSON。

趁機讓開發環境設定也可重現：種子資料、測試使用者，以及一個每次都能以相同方式啟動環境的指令。

<a id="keep-the-verification-skill-honest"></a>

## 保持驗證 skill 的誠實

應用程式會變更，功能地圖會過時。至少每天執行一次，理想情況下從排程自動化執行，這樣就不需要任何人記住：

```text
/maintain-verification-skill
```

[`/maintain-verification-skill`](../../skills/maintain-verification-skill/SKILL.md) 會稽核產生的 skill：每個功能一個唯讀來源讀取器並行執行，然後一次即時透過以驅動所有對映的功能。它結束於三種結果之一。`clean` 表示完整覆蓋且無需發布。`changed` 表示一個包含已證明修正的 PR，僅限於驗證 skill 自身的目錄。`blocked` 指名阻礙因素。它絕不編輯產品程式碼。若即時透過偵測到產品回歸，它會回報該回歸，而非透過檔案編輯來隱藏它。

<a id="prove-a-property-with-a-formal-verification-skill"></a>

## 以正式驗證 skill 證明屬性

驗證技能可證明您執行的路徑。某些錯誤存在於無人執行的路徑上：兩次重試重疊、寫入期間鎖過期、狀態變更順序錯誤。測試很少涵蓋這些情況。對於此類錯誤代價高昂的程式碼，請執行：

```text
/create-formal-verification-skill
```

[`/create-formal-verification-skill`](../../skills/create-formal-verification-skill/SKILL.md) 從程式碼中選擇目標：程式碼做出的承諾、中斷會損害使用者且測試很少涵蓋該中斷情況的地方。它選擇最強的目標，而非最易驗證的目標。若要從特定領域或錯誤開始，請指定該領域或錯誤：

```text
/create-formal-verification-skill the job lease and retry logic. a job must never run twice at once.
```

問題決定工具。事件是否可能以錯誤順序發生，適合對設計的小型模型使用模型檢查器，TLA+ 是任何語言中此用途的預設選項。程式碼是否對所有輸入都表現正確，適合在程式碼上執行的驗證器。若沒有驗證器支援您的語言，代理會說明此情況，並使用產生的輸入測試實際程式碼，而非手寫的函式模型。必須在所有規模下成立的屬性可能需要證明。代理會告知您選擇了哪種工具以及原因。

它驗證單一屬性，並展示該屬性在程式碼的損毀變體上失敗，因為從未見過的失敗檢查證明不了任何事。若目前程式碼已違反該屬性，您會獲得以程式碼術語表示的短步驟序列作為反例。屬性保持原樣陳述，因此發現結果保持為發現結果。除非您要求，否則產品程式碼不會變更。

它撰寫專案本機技能，告知下一個代理變更是否觸及已驗證的程式碼、如何用單一命令執行檢查，以及如何區分真實錯誤與建模錯誤。在移交之前，產生器會針對一個違反屬性的草稿變更執行一次該技能，且檢查必須捕捉到它。移交說明驗證了什麼、使用哪種工具與邊界、檢查發現了什麼，以及它不涵蓋什麼。透過僅涵蓋模型或邊界，而非整個系統。

該技能還記錄每個檢查涵蓋的程式碼，以及檢查最後與之匹配的 commit。跳過技能的合併與重構可能導致檢查在已消失程式碼的描述上透過。在此之後，請執行 [`/maintain-formal-verification-skill`](../../skills/maintain-formal-verification-skill/SKILL.md)。它從程式碼更新驗證、重新執行，展示每個更新的檢查仍可能失敗，並告知您每個失敗是驗證中的漂移還是程式碼中的錯誤。

<a id="open-the-pr"></a>

## 開啟 PR

```text
/poteto-mode open the pr. small ordered commits, evidence in the description.
```

[開啟 PR 手冊](../../skills/poteto-mode/playbooks/opening-a-pr.md) 從 worktree 運作，將工作 rebase 為小型有序 commit，清理 diff，潤飾文字，並返回 PR 連結。五個狹窄的 PR 勝過一個龐大的 PR，堆疊的後續 PR 勝過不斷增長的分支。

<a id="drive-the-pr-to-merge-ready-with-babysit"></a>

## 使用 Babysit 將 PR 推進至可合併狀態

開啟的 PR 會立即開始累積阻礙因素。檢查失敗、審查者留言、主幹移動。將這些變動交給 [Babysit 手冊](../../skills/poteto-mode/playbooks/babysit.md)：

```text
/poteto-mode babysit this pr. get it green.
```

Babysit 監控 PR，在 GitHub 或 Origin CLI 可用時使用內建監控器，並依序處理阻礙因素：衝突、然後是審查執行緒、然後是 CI。所有已知修復批次為一次推送，因此檢查只需重新啟動一次，而非每次修復後都重新啟動。留言分類持懷疑態度，因為人類和機器人將真實發現與噪音列在同一清單中。真實發現會獲得修復，噪音則被駁回，並線上程上發布反證。若您只需要狀態，請提出較小的請求，Babysit 會回答而不啟動迴圈：

```text
/poteto-mode check on pr 123. anything outstanding?
```

Babysit 在可合併狀態時停止。它從不合併，即使一切皆綠，因為合併是不同的決策。

<a id="land-the-stack-with-shipping"></a>

## 使用 Shipping 落地堆疊

綠色不等於安全。當您準備落地時，請說明：

```text
/poteto-mode land the stack.
```

[Shipping 手冊](../../skills/poteto-mode/playbooks/shipping.md) 在啟用任何操作前獨立驗證每個 PR。每個 PR 由一個全新代理證明行為，判斷變更的代理絕非撰寫該變更的代理。然後 Shipping 僅從底部落地連續的已驗證執行序列，預設透過 GitHub 一次一個 PR，或在 Origin CLI 可用時透過 Origin，並報告中斷鏈條的第一個 PR。位於未驗證 PR 上方的已驗證 PR 會等待，因為合併它會將下方的缺口拉入。

下一步：[在您睡眠時執行工作](./07-overnight.md)。
