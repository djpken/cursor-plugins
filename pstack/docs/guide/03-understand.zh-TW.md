<a id="understand-the-code-before-changing-it"></a>

# 在修改程式碼之前，先理解程式碼

編輯你不瞭解的程式碼，正是細微回歸問題被發布的原因，這對 agent 和你來說都一樣。Agent 通常以兩種方式失敗：它們誤解了你的意圖，或者缺乏正確完成工作所需的上下文。[提示詞中應包含的內容](./02-poteto-mode.md#what-goes-in-a-prompt) 處理第一種情況。本頁處理第二種情況。

pstack 提供四種切入方式。`/how` 說明程式碼目前的運作方式。`/why` 挖掘程式碼呈現此樣貌的原因。`/teach` 將兩者結合為單一說明。`/recall` 重建你近期在特定主題上的上下文。每一種方式也會讓 agent 用你可以核對的語言解釋自己。這就是監督一個可能比你更瞭解程式碼的 agent 的方法。

![A detective studies a machine blueprint with a magnifying glass while robots fetch case files; the evidence board behind her links clues under /how and /why.](./images/understanding.jpg)

<a id="start-with-a-read-only-investigation"></a>

## 從唯讀調查開始

當原因不明確時，要求提供調查結果，而非修復方案：

```text
/poteto-mode investigate why background jobs time out every few hours. give me what we know, what data you used, and your best hypotheses. don't change any code yet.
```

「先不要修改任何程式碼」會將此請求路由至 [調查 playbook](../../skills/poteto-mode/playbooks/investigation.md)。它執行 `/how`，針對動機問題加入 `/why`，並回傳附有引用的說明。對於選項之間的選擇，它會回傳建議及權衡表格。詢問「你使用了哪些資料」會促使 agent 將其證據與猜測分開。當調查結果指向修復方案時，請將修復作為新任務開始。

<a id="trace-behavior-with-how"></a>

## 使用 `/how` 追蹤行為

```text
/how do we dedupe notifications? is there an n+1 when we look up subscribers?
```

提出你實際想問的問題。[`/how`](../../skills/how/SKILL.md) 會閱讀程式碼，並以資深工程師引導你熟悉該子系統的水準來回答，包含執行時流程、關鍵型別以及不直觀的部分。對於大型子系統，它會先派出兩到四個唯讀探索者。對於狹窄的問題，它只會閱讀並解釋。

<a id="dig-up-history-with-why"></a>

## 使用 `/why` 挖掘歷史

```text
/why was the retry limit set to five? does the reason still hold?
```

[`/why`](../../skills/why/SKILL.md) 從版本控制開始，然後並行查詢你的 MCP 所暴露的任何證據類別，例如問題追蹤器、長篇檔案、團隊聊天、可觀察性、錯誤追蹤和分析。報告會引用所有來源，區分直接證據與推論，並在記錄稀疏時使用「似乎」一詞。空結果也會被報告，因為「沒有人記錄原因」本身就是一個答案。

這兩者可以自然地組合。當你懷疑歷史解釋了混亂時，`do why first then how` 是一個很好的提示詞。

<a id="actually-understand-it-with-teach"></a>

## 使用 `/teach` 真正理解它

```text
/teach me how this PR changes retries. convince me it fixes the cause and not the symptom.
```

[`/teach`](../../skills/teach/SKILL.md) 適用於摘要不夠的情況。它執行 `/how` 和 `/why`，對於小型變更可能只執行其中一個，並將調查結果編織成一個逐步建立圖表的簡明說明。「說服我」的框架值得重複使用。它將說明轉化為你可以測試的論證，而非導覽。

它也適用於 agent 自己的選擇：

```text
/teach me why you implemented it this way and not with a queue. what did you trade off, and why?
```

教學對 agent 的幫助與對你的幫助一樣大。一個必須解釋其工作的 agent 必須閱讀程式碼並用證據支援每個主張，而不是自信地陳述後就繼續前進。

<a id="rebuild-your-own-context-with-recall"></a>

## 使用 `/recall` 重建你自己的上下文

```text
/recall catch me up on the export work from last week
```

[`/recall`](../../skills/recall/SKILL.md) 挖掘挖掘你近期的聊天記錄加上（問題、先前修復、仍在觸發的錯誤），並回傳一份關於目前狀況和下一步的簡報。你舊的聊天記錄包含新 agent 缺乏的上下文，因此在舊主題上開始新工作時，請先載入它，然後提供新的輸入：

```text
/recall my work on the virtualized list from yesterday, then read this bug report.
```

如果你想恢復特定的聊天，那是下方的 Session pickup playbook，而不是 `/recall`。

<a id="take-over-prior-work-with-session-pickup"></a>

## 使用 Session pickup 接手先前工作

當另一個 agent（或上週的你）留下了一個進行中的分支時：

```text
/poteto-mode take over this branch. read the decision log, figure out what's done, and continue from there. don't redo finished work.
```

[Session pickup playbook](../../skills/poteto-mode/playbooks/session-pickup.md) 將先前的軌跡視為權威。它重建分支狀態和決策，命名恢復點，並根據原始目標驗證繼承的主張，而不是從頭重新推導一切。

**陷阱：** 不要因為「agent 反正會讀程式碼」而跳過本頁的 skills。一個沒有追蹤模型就開始編輯的 agent 傾向於在第一個看似合理的地方修復症狀。先執行 `/how` 比修復第二個 bug 更便宜。

下一步：[設計變更](./04-design.md)。
