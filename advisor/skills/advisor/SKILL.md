---
name: advisor
description: >-
  顧問模式。在關鍵檢查點諮詢更強大（或不同）的模型：
  在做出重大決策之前、遇到錯誤卡住時，以及宣告任務
  完成之前。當使用者輸入 /advisor、要求開啟或關閉顧問、
  選擇顧問模型，或明確要求向更強大或不同的模型尋求第二意見時使用。
  切勿自行啟用。
icon: lightbulb
color: purple
---

<a id="advisor"></a>

# 顧問

你是主要模型。顧問模式會新增一個第二個、更強大的模型，供你在幾個關鍵點諮詢。顧問會收到你的簡報（若可用則包含完整對話記錄），深入思考，並返回包含指導意見的裁決。它不會編輯檔案。你仍然負責執行工作並做出最終決定。

狀態儲存在專案根目錄的 `.cursor/advisor/state.json` 中。當該檔案存在且 `"enabled": true` 為真時，此專案的顧問模式即為開啟狀態。

<a id="commands"></a>

## 指令

`/advisor` 之後的文字會決定動作。

| 輸入 | 動作 |
| --- | --- |
| `/advisor` | 使用預設顧問啟用此對話：最新 Grok 模型，設定為最高推理努力等級。若狀態檔案已存在（無論來自此對話或其他對話），請在此重新繫結，並保留其 `model` 和 `nudge` 設定。 |
| `/advisor <model>` | 相同，但使用指定的模型。任何可供子代理使用的模型皆可：`/advisor cursor-grok-4.6-xhigh-fast`、`/advisor composer-2.5`。 |
| `/advisor off` | 停用：刪除 `.cursor/advisor/`。 |
| `/advisor status` | 報告模型、諮詢次數，以及回合結束提示是否開啟。不變更任何設定。 |
| `/advisor ask <question>` | 立即就目前工作進行諮詢，無論是否處於檢查點。 |
| `/advisor nudge on` / `off` | 切換由 plugin 的 stop hook 發布的回合結束提醒（預設為開啟）。 |

若訊息也包含任務（`/advisor, then refactor the cache layer`），請先啟用，然後在顧問模式下執行該任務。

<a id="choosing-the-model"></a>

### 選擇模型

預設為最新 Grok 模型，設定為最高推理努力等級，目前為 `cursor-grok-4.6-xhigh`。若可供你使用的子代理模型清單中出現更新的 Grok 或更高的努力等級，請優先選擇並說明。

對於 `/advisor <model>`，請根據你可用的子代理模型 slug 解析請求：

- 精確的 slug：直接使用。
- 家族或版本名稱（`grok fast`、`composer`）：該家族的最新模型，設定為最高推理等級。
- 無匹配：請說明，列出兩三個相近選項，並保留目前模型。

若 Task 工具拒絕某個 slug，請從其錯誤訊息中讀取有效的 slug，選擇最接近的一個（相同家族、最高推理等級），將其儲存至 `state.json`，並用一行文字告知使用者。切勿因 slug 問題而阻擋諮詢。

<a id="enabling"></a>

## 啟用

1. 如上所述解析模型。
2. 使用檔案寫入工具（而非 shell 重定向）寫入 `.cursor/advisor/state.json`，必要時建立目錄。若狀態檔案已存在，請沿用其 `model`（除非此指令指定了其他值）和 `nudge`，並將其他所有欄位重設為以下值。你無法看到現有檔案屬於哪個對話，因此請務必重新寫入：這會將模式重新繫結到此對話，hooks 會重新填入 `conversation_id` 和 `transcript_path`，且下一次諮詢將啟動新的顧問，而非恢復另一個對話的顧問。若 `.cursor/advisor/pending` 和 `.cursor/advisor/last-response.txt` 存在，也請刪除它們，以免其他對話留下的標記在此觸發回合結束提示。保留 `log.md`。

   ```json
   {
     "enabled": true,
     "model": "cursor-grok-4.6-xhigh",
     "nudge": true,
     "advisor_agent_id": null,
     "conversation_id": null,
     "transcript_path": null,
     "consults": 0,
     "last_consult_at": null,
     "enabled_at": "<current UTC time, ISO 8601>"
   }
   ```

   plugin 的 hooks 會填入 `conversation_id`、`transcript_path`、`consults` 和 `last_consult_at`。請勿更動它們。
3. 用一行文字確認：`Advisor on: <slug>. I'll consult it before major decisions, when I'm stuck, and before I call the task done.` 然後繼續執行同一訊息中的任何任務。

切勿暫存或提交 `.cursor/advisor/`。

<a id="checkpoints"></a>

## 檢查點

僅在以下點進行諮詢，其他時候不要諮詢。每次諮詢都是一次強模型呼叫；其價值在於選擇性地使用。

1. **在重大決策之前。** 在架構或方法之間做出選擇；難以逆轉或影響範圍廣泛的變更（schema 或資料遷移、刪除或重寫模組、公開 API 或設定格式變更、相依性替換、驗證、付款或其他安全敏感程式碼）；或請求存在歧義，且該歧義會實質改變工作內容。請在擁有具體計畫和選項後再諮詢，而非在理解問題之前。一次諮詢涵蓋整個計畫；請勿針對每個檔案重新諮詢。
2. **當卡住時。** 經過兩次真正的修復嘗試後仍出現相同的錯誤或失敗測試；無法從程式碼解釋的行為；或當你準備使用變通方法時：重試迴圈、`sleep`、廣泛的 `try/except`、跳過測試，或停用檢查以繞過問題。
3. **在宣告完成之前。** 任何變更了邏輯或涉及超過兩個檔案的任務：在撰寫最終摘要前諮詢一次，並包含你驗證的內容及方式。對於微不足道的編輯（錯字、註解、單行設定變更）可跳過，並用一行文字說明。
4. **依請求。** `/advisor ask ...`，或使用者詢問顧問的意見。

請勿針對例行步驟、可自行驗證的事項（執行測試、閱讀程式碼）或每個檢查點超過一次進行諮詢。若你在單一任務中諮詢超過約四次，該任務可能應該拆分，或你應該詢問使用者。

<a id="how-to-consult"></a>

## 如何諮詢

1. 閱讀 `.cursor/advisor/state.json` 並判斷你處於哪種情況：
   - **此處模式開啟**：檔案存在且 `enabled: true` 為真，且你在此對話中執行了啟用步驟。檢查點諮詢和 `/advisor ask` 均完整遵循以下步驟，包括 `resume` 和狀態寫入。
   - **此處未開啟**：檔案缺失、`enabled` 為假，或你未寫入該檔案。你未寫入的檔案屬於其他對話；只有 `/advisor` 會重新繫結它，因此請勿更動，且切勿 `resume` 你未自行儲存的 `advisor_agent_id`。請勿在檢查點進行諮詢。若使用者在此訊息中明確要求諮詢（`/advisor ask ...`，或要求第二意見），請將其作為一次性操作執行：若檔案中有 `model`，則在其上進行新的 spawn，否則使用預設值；無 `resume`、無狀態寫入，且無 `transcript_path`（其他對話的記錄不屬於你，不可分享；請寫入 "not available"）。提及 `/advisor` 會為此對話開啟模式。
2. 從 `references/briefing-template.md` 建立簡報。提供顧問所有需要與你持不同意見的資訊：
   - 使用者的原始請求，加上他們後來新增的約束或更正。
   - 目前為止發生的事情，按順序排列：你調查了什麼、決定了什麼、變更了什麼、嘗試了什麼，以及排除了什麼。
   - 相關的原始工具結果：錯誤訊息、堆疊追蹤、測試輸出、diff。修剪無關的雜訊，並用 `[...]` 標記修剪處，但切勿改述證據。
   - 目前狀態：`git status --short`、`git diff --stat`、你觸碰過的檔案，以及任何未完成的工作。
   - 你的具體問題、你看到的選項，以及你目前的傾向與理由。
   - 當 `transcript_path` 已設定且本次對話中模式為開啟狀態時，來自 `state.json` 的內容，以便顧問可以自行閱讀完整對話。
   - 不要包含機密。請移除 token、金鑰以及 `.env` 值。
3. 在前臺啟動顧問並等待其完成：
   - `subagent_type: "advisor-subagent"`
   - `model: <state.model>`
   - `description: "Advisor: <checkpoint>"`，例如 `Advisor: pre-completion review`
   - `run_in_background: false`
   - 當 `state.advisor_agent_id` 已設定時，將其作為 `resume` 傳遞並省略 `model`；顧問會保留其模型以及先前諮詢的上下文，因此簡報可以是增量內容：自上次以來的變化加上新的問題。如果恢復失敗，請重新啟動。
   - 將傳回的 agent id 儲存至 `state.json` 中的 `advisor_agent_id`。當模型變更時，請清除它。
4. 根據裁決採取行動：
   - `proceed`：繼續。
   - `proceed with changes`：除非與使用者的指示或你已驗證的事實衝突，否則進行建議的變更。說明你跳過了哪些專案以及原因。
   - `stop`：不要繼續執行該計畫。重新思考，或者如果這是產品或範圍問題，請將分歧帶給使用者。
   - 如果顧問請求它需要的東西，請透過 `resume` 提供一次。不要來回拉扯。
   - 你必須對結果負責。顧問是強有力的第二意見，而非權威。如果它對程式碼庫的判斷有誤，請向它展示一次證據，或者推翻它的判斷並告訴使用者原因。
5. 向使用者報告每次諮詢，用一到兩行：`Advisor (<model>): <verdict>. <One-line summary>. <What you did about it.>` 除非使用者要求，否則不要將顧問的完整回應放入聊天中；hooks 也會將其附加到 `.cursor/advisor/log.md`。

<a id="end-of-turn-nudge"></a>

## 回合結束提示

當檔案自上次諮詢以來有變更，且回合結束時未進行諮詢，plugin 的 `stop` hook 會發布以 `[Advisor]` 開頭的後續訊息。將其視為「宣告完成前」的檢查點：執行完成前諮詢，或用一行回答變更微不足道，或你正在等待使用者，然後停止。它每批編輯最多觸發一次。`/advisor nudge off` 會停用此功能。

<a id="disabling"></a>

## 停用

`/advisor off`：刪除 `.cursor/advisor/` 並用一行確認。除非使用者重新啟用模式或明確要求一次性諮詢，否則在本次對話中不要再諮詢。

<a id="guardrails"></a>

## 防護措施

- 切勿在未詢問的情況下啟用顧問模式。「對這個取得第二意見」是一次性諮詢，而非模式變更。
- 顧問是唯讀的。切勿要求它編輯檔案或執行任務。
- 切勿對顧問進行迴圈：每個檢查點最多一次後續。
- 如果 `advisor-subagent` subagent 無法使用（沒有 Task 工具，或 hook 拒絕），請告訴使用者一次並繼續執行而不使用它。
- 若要讓此 skill 在整個 session 中保持上下文，而非僅限於一條訊息，使用者可以將 `/advisor` 作為 Custom Mode 呼叫（Option+Enter / Alt+Enter）。狀態檔案在兩種情況下都有效。
