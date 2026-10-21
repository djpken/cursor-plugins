<a id="recipes-and-pitfalls"></a>

# 配方與常見陷阱

值得複製的提示詞，以及每個人只犯一次的錯誤。請替換成您自己的路徑與完成條件。這些配方刻意採用非正式語氣。這正是實際輸入時的樣貌，且 skill 能很好地理解意圖。

![She tastes a finished dish while robots cook from a recipe box, with pinned cards reading /how, /tdd, and /loop above the counter.](./images/recipes.jpg)

<a id="understand-an-unfamiliar-subsystem"></a>

## 理解陌生的子系統

```text
use /how first to understand how this initialization works. then use /why to figure out why it broke recently.
```

先了解機制，再瞭解歷史。每個 skill 的報告會告訴您它搜尋了哪些來源，因此您知道答案的依據是什麼。

<a id="restate-a-noisy-report-before-touching-code"></a>

## 在修改程式碼前，重述一份雜訊多的報告

```text
/poteto-mode read this thread. restate the underlying issue in your own words, in plain english. don't change any code yet.
```

誤解會出現在重述中，此時只需一條訊息即可修正。在 agent 陳述其理論之前，請先保留您自己的看法。`/in-your-own-words` 僅要求重述，不包含 playbook。

<a id="sharpen-a-prompt-before-you-send-it"></a>

## 在傳送前磨練提示詞

```text
/sharpen write the onboarding doc for the new billing service.
```

Agent 會指出專家會做但您未要求的事項、他們會使用的原始素材、方法與工具，以及驗證結果正確性的外部檢查。它會回傳重寫後的提示詞以要求這些內容，但不執行任務。當重寫內容讀起來正確時，再傳送該重寫版本。

<a id="prototype-before-you-pick"></a>

## 在選擇前先製作原型

```text
/poteto-mode prototype a few options for the settings layout. put them behind a switcher and send me screenshots of each.
```

您從可執行的專案中選擇，而非從描述中選擇。Agent 會在此過程中自行回答關於佈局與時序的問題。

<a id="turn-a-settled-design-into-a-plan"></a>

## 將確定的設計轉化為計畫

```text
/poteto-mode turn this design into a plan. small verifiable PRs, each with its own proof.
```

僅在設計確定後才詢問。計畫是交付物，且會指名執行該計畫的 playbook。

<a id="get-a-second-opinion-on-a-design"></a>

## 對設計尋求第二意見

```text
ask /arena for a second opinion on this thread and our approach
```

您目前的設計成為多個候選方案之一，綜合評估會告訴您小組是否找到了更好的方案，或確認了您原有的設計。相對於昂貴且難以反轉的設計，這代價很小。

<a id="check-independent-slices-in-parallel"></a>

## 並行檢查獨立的切片

```text
/swarm check every package under packages/ against its check.sh. one worker per package. one report.
```

每個 worker 負責一個套件。父級等待所有切片完成，並回傳一份 `PASS`、`ISSUES` 或 `BLOCKED` 報告，而非原始的 worker 輸出。

<a id="review-a-branch-skeptically"></a>

## 以懷疑的態度審查分支

```text
/interrogate the whole branch, but skeptically. don't change anything yet. no nitpicks unless it's an actual bug or regression in behavior.
```

限定詞發揮實際作用。「先不要更改任何內容」使其保持唯讀狀態，而挑剔規則會預先過濾雜訊，使 `Act on` 的發現值得您花時間檢視。

<a id="fix-a-bug-through-a-failing-test"></a>

## 透過失敗的測試修復錯誤

```text
/poteto-mode repro the duplicate write first. if there's a cheap test path, /tdd it. then fix and rerun.
```

「如果有廉價的測試路徑」這一點很重要。強制透過脆弱的 mock 執行測試，其證明力不如執行真實命令，且 playbook 允許如此說明。

<a id="repro-and-fix-a-report-with-proof"></a>

## 重現並修復報告，並附上證明

```text
/poteto-mode repro this with /verify-<app>. if it repros on main, fix it and show me a video as proof.
```

「如果在 main 上能重現」允許執行在錯誤已消失時提前停止。影片讓您在閱讀 diff 之前即可檢查修復結果。

<a id="prove-a-property-tests-rarely-reach"></a>

## 證明屬性測試很少覆蓋的特性

```text
/create-formal-verification-skill the retry queue. a message must never be delivered twice.
```

您會得到在指定範圍內所有案例中檢查過的一個屬性、如果程式碼已違反該屬性則以您程式碼的術語呈現的反例，以及一個專案 skill，供下一個 agent 在該程式碼有任何變更時執行。

<a id="vet-a-number-before-you-post-it"></a>

## 在發布前審核數字

```text
/benchmark-checklist vet this 40% speedup before it goes in the pr description
```

您會得到更快、更慢、無可測量差異或結論不明，並附上執行次數、範圍以及限制該數字的因素。

<a id="stop-correcting-the-same-mistake"></a>

## 停止糾正同一個錯誤

```text
/correct agents keep adding new config flags without registering them in the schema
```

修復會以架構、型別、lint 或測試的形式落入儲存庫，使下一個 agent 無法再犯此錯誤。

<a id="ask-how-without-starting-the-work"></a>

## 詢問方法但不開始工作

```text
/poteto-help how do i get poteto-mode to stay on every turn?
```

您會得到答案、要傳送的提示詞，以及來源連結。在您傳送該提示詞之前，不會執行任何內容。

<a id="keep-a-run-honest-while-youre-away"></a>

## 在您離開時保持執行誠實

```text
im going to bed, keep going autonomously until every fixture passes. do not stop. keep a decision log i can audit in the morning.
```

完整合約在 [overnight page](./07-overnight.md) 上。一旦任務與完成條件已在對話中，簡短形式即可運作。

<a id="redirect-a-drifting-run"></a>

## 重新導向偏離的執行

引導提示詞只需一行：

```text
i said the goal is to repro. i did not ask for a fix yet.
```

```text
apply prove it works. show me the real output, not the build log.
```

```text
/unslop that, no emdashes
```

您很少需要更多字詞。您需要正確的名稱，而 [the principles page](./08-principles.md) 就是詞彙表。

<a id="get-the-reply-in-plain-words"></a>

## 用平實語言獲得回覆

```text
/bro
```

這就是完整的提示詞。[`/bro`](../../skills/bro/SKILL.md) 像兩個人對話一樣重述最後一條訊息，沒有術語，更簡短。當回覆技術上很詳盡但您仍不知道它說了什麼時，請使用它。

<a id="catch-up-on-a-chat-you-left-running"></a>

## 追趕您離開時仍在執行的對話

```text
/ketchup
```

[`/ketchup`](../../skills/ketchup/SKILL.md) 用平實語言告訴您自您上條訊息以來有何變更，然後列出每個等待您處理的呼叫，並附上其選擇、證據與風險。它在報告期間不會更改任何內容。

<a id="the-pitfalls"></a>

## 常見陷阱

- **在提示詞中列舉 skills。**「使用 /how 然後 /architect 然後 /arena」會重新排序 playbook 已排序的步驟。請陳述目標與限制。僅在要覆蓋預設值時才指名 skill。
- **模糊的完成條件。**「讓它變好」讓 `/loop` 無從檢查。請提供一個可以透過或失敗的命令或產物。
- **先提出你對原因的理論。** agent 會在你指定的地方搜尋。請先要求它重述問題，然後再分享你的直覺。
- **採用第一個設計。** 一次嘗試會鎖定模型想到的第一個形狀。請要求原型或 `/architect`，並從證據中選擇。
- **打磨抽象計畫。** 對沒有程式碼背後的計畫進行對抗性審查，會發明永遠不會發生的風險。請用原型解決未決問題，然後審查已建置的內容。
- **在單一 worktree 中並行執行多個 agent。** 它們會互相覆寫，而且沒有人能分辨哪個 agent 做了什麼變更。請將它們作為雲端 agent 執行，或指定「每次嘗試使用獨立的 worktree」。
- **在信任迴圈前先執行迴圈。** 無法驗證自身工作的迴圈只會讓未檢查的工作變快。請先讓驗證 skill 運作。
- **信任未審核的數字。** 溫暖的快取或跳過的程式碼路徑可能偽造加速效果。在數字發布前，請執行 `/benchmark-checklist`。
- **手動修正同一個錯誤。** 在聊天中的修正只對當次執行有效。`/correct` 會修正儲存庫，確保後續執行不會重複該錯誤。
- **使用 `/arena` 進行覆蓋。** `/arena` 重複一個設計或程式碼簡報，然後選擇基礎並嫁接最佳部分。`/swarm` 分割切片或宣告的競態臂，並彙總一份報告。
- **接受所有審查意見。** 機器人與人類都在同一個清單中提交真實發現與雜訊。`/interrogate` 將發現分類為「採取行動」與「駁回」的桶，並附上理由，您可以任意覆蓋。
- **將 `auto` 視為 model slug。** `auto` 和 `inherit-parent` 表示「省略 model 欄位，讓 subagent 繼承父級聊天 model」。[Setup](./01-setup.md) 涵蓋了相關角色。
- **根據綠色建置報告成功。** 建置證明它能編譯。請要求真實命令、流程、儲存值或 profile，並預期在回覆中提供證據。
- **手動撰寫 `SKILL.md`。** 請透過 [Authoring or modifying a skill playbook](../../skills/poteto-mode/playbooks/authoring-a-skill.md) 來處理，以確保驗證與審查流程得以執行。

這就是指南。如果您跳過前面內容，請回到 [setup](./01-setup.md) 並執行一個真實任務。習慣來自使用，而非閱讀。

返回 [guide index](./README.md)。
