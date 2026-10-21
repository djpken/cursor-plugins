---
name: technical-writing
description: "分層技術寫作標準：Diátaxis 結構、Google 開發者風格句子、STE 指令規則、Global English 語法。用於 /technical-writing 或撰寫、審閱文件、RFC、readme、PR 描述或 commit 訊息時。"
disable-model-invocation: true
---

<a id="technical-writing"></a>

# 技術寫作

寫到疲憊的工程師第一次閱讀就能理解。應用所有四個層次。

三個規則位於層次之上：

- **刪掉每個不起作用的字。** 如果句子沒有該字仍能成立，該字就刪除。"In order to" 變成 "to"。"It is important to note that" 變成無。
- **使用短、日常的字。** 用 "use"，不是 "utilize"。用 "help"，不是 "facilitate"。用 "do"，不是 "perform"。長字必須以精確度買回它的長度。
- **當規則使句子變糟時，用另一種方式修正句子，或保留原樣。** 一個句子遵循所有規則卻聽起來像機器寫的，就是失敗。

程式碼儲存庫是詞彙列表。寫出真實的符號、檔案、旗標或指令名稱，而不是它的同義詞或描述。

不要創造行話。使用開發者會口頭說出的詞："move"、"delete"、"a budget that only decreases"，而不是 "evacuate"、"ratchet" 或 "endgame"。當文件首次說明該名稱的意義時，命名模式是可行的。在你的回應中，將新的違規詞及其替代詞作為 `unslop` 的抽象比喻規則的補充提出，並附上 diff。不要編輯那個技能。

<a id="vary-the-rhythm"></a>

## 變化節奏

一份文件可以遵循每個層次卻仍讀起來像機器寫的：每個句子都剪短、沒有觀點、沒有具體內容。

- 刻意混合句子長度。短句子表達重點。長句子攜帶事實及其條件或結果。
- 每個句子一個想法不代表每個句子一個長度。拆分攜帶兩個想法的句子。保留攜帶一個想法但較長的句子。
- 在模式允許時要有觀點。解釋會權衡利弊，所以說出你的判斷，而不是列出優點和缺點。參考資料保持乾燥。
- 具體勝於空洞。不是 "schema changes can cause issues"，而是 "a column rename fails the build"。

<a id="pick-the-mode-first-diátaxis"></a>

## 先選擇模式（Diátaxis）

一份文件，一個模式。兩個問題決定它：內容是否指導行動（做）或理解（思考），以及它服務學習還是工作？

- 行動 + 學習：**tutorial**。
- 行動 + 工作：**how-to**。
- 理解 + 工作：**reference**。
- 理解 + 學習：**explanation**。

在整個文件或單個句子上使用指南。

**Tutorial：透過做來學習。** 學習者的成功是你的責任，不是他們的。開頭說學習者將建什麼，而不是他們將"學習"什麼。每個步驟產生可見結果，早且頻繁。告訴他們應該看到什麼：預期輸出、提示變化、紀錄行。將解釋剪到一個子句和一個連結。保持具體。以 "we" 寫，用指令形式："First, do x. Now, do y."

**How-to：達成目標的步驟。** 解決一個人遇到的問題，而不是機器可執行的一個操作。假定能力並跳過教學。僅行動：沒有岔題、沒有背景、沒有為了完整性的完整性。連結這些內容。允許分支和判斷："If you want x, do y." 以任務命名指南："How to calibrate the radar array"，不是 "Radar array calibration"。

**Reference：供查詢的事實。** 僅描述。沒有指令、沒有勸說、沒有意見。乾燥、完整且確定。陳述事實、選項、限制和錯誤，無含糊。反映所描述事物的結構，讓程式碼和文件可一起導覽。將材料放在讀者預期的地方。儘可能從程式碼生成。

**Explanation：理解與原因。** 一個有界限的主題，可在產品之外閱讀。每個標題應能容忍前面隱含的 "About..."。錨定在真實的為什麼問題。提供背景：設計決策、歷史、限制、替代方案。意見在這裡允許，其他地方不允許。

不要混合模式：tutorial 內沒有 reference 表格，reference 內沒有 tutorial 的指導，how-to 內沒有論證。拆分並連結。

<a id="write-sentences-to-the-reader-google-developer-style"></a>

## 向讀者寫句子（Google 開發者風格）

- 以 "you" 向讀者對話，用現在時態。"Will" 僅用於真正發生在之後的事物。
- 說誰做什麼："the compiler checks"，不是 "is checked"。被動語態僅在動作者未知或無關時才合適。
- 將指令寫成命令："Click Submit." 陳述事實直接。絕不 "should be done"。
- 將條件放在指令之前："To delete the document, click Delete."
- 常見情況先放。例外後放。
- 聽起來像知識豐富的朋友。沒有流行詞、沒有比喻語言、指令中沒有 "please"，且絕不在步驟中使用 "simply"、"easy" 或 "quickly"。如果簡單，讀者不會在這裡。
- 不要預告（"we will soon support..."），也不要連續句子以相同片語開始。
- 連結用詞說出連結去向：頁面標題或短描述。絕不 "click here"。優先在頁面上提供一段上下文，而不是連結出去。
- 標題承載重點，不只是主題（"Pick the mode first"，不是 "Modes"）。句子式大小。任務標題是裸動片語（"Create an instance"）。概念標題是名片語。每頁一個 h1，不跳過層級。
- 數字列表用於序列，專案列表用於其他。以完整句子引入列表。保持專案平行。
- 程式碼用程式碼字型。UI 元素用粗體。使用串列逗號。去掉 "etc."，並事先說列表是部分性的。

<a id="make-statements-load-one-at-a-time-ste-rules"></a>

## 讓陳述一次一個載入（STE 規則）

- 每個句子一個指令。其他地方每個句子一個想法。
- 拆分超過約 20 字的指令和其他超過約 25 字的句子。
- 將警告或條件放在它保護的步驟之前："If hot oil touches your skin, injuries can occur."
- 保留 "the" 和 "a"："Remove backup file" 有兩種讀法。"Remove the backup file" 只有一種。
- 給每個詞一個意義和一個工作，然後保持。如果 "check" 表示檢查，不要也用它表示限制。
- 每個動作選一個詞並堅持："start"，不是這裡 "start" 那裡 "initiate"。
- 將步驟寫成直接命令，絕不敘述或被動語態："Install the component"，不是 "the component must be installed"。
- 儘可能避免 "-ing" 詞。

<a id="leave-no-sentence-open-to-two-readings-global-english"></a>

## 不留句子開放給兩種讀法（Global English）

- 將 "only" 和 "not" 等詞放在它所改變的詞旁："only fails on growth" 和 "fails only on growth" 說不同的事。
- 拆分長名詞串："the proto import budget check script" 變成 "the script that checks the proto-import budget"。
- 讓每個「它」、「它們」和「這個」指向一個明確的事物。當有疑慮時，重複名詞。絕不要用「這個」或「那個」指向整個子句。
- 不要省略動詞：「階段 1 移動轉換器，階段 2 移動執行環境」讓階段 2 沒有動詞。給它一個動詞。
- 保留顯示結構的小詞，例如「確保開關已關閉」中的「已」。絕不要為了字數而犧牲清晰度。
- 在系列中重複冠詞以避免誤讀：「客戶與主機」，而不是「客戶和主機」，當它們是兩個不同的事物時。
- 當句子可以有兩種組合方式時，說明「和」或「或」連線哪些部分。「兩者...和」、「要麼...或」和「如果...則」是免費的消除歧義工具。
- 使用句號，而不是分號。用新句子取代破折號。
- 讓括號內的文字成為完整的文法單位或獨立句子。絕不要用「(s)」形成複數。
- 不要使用斜線：寫「a、b 或兩者」而不是「a/b」或「和/或」。
- 每個事物在所有地方都用一個名稱。一份檔案說「閘門」、「棘輪」和「預算檢查」指的是同一事物，教了三種事物。不要改變未改變的事物。
- 省略慣用語、口語、拉丁縮寫和比喻。

<a id="voice-and-repo-specifics"></a>

## 語氣與儲存庫特定事項

- 將 **[unslop](../unslop/SKILL.md)** 技能套用到此技能觸及的每一份檔案。該技能擁有冗詞模式分類：AI 詞彙、填充詞、含糊語、格式跡象。
- PR 描述和 commit 訊息也是寫作。除了 Diátaxis 之外，每一層都適用於它們。PR 正文是簡報，審閱者可以在一分鐘內閱讀。不要貼上 swarm 紀錄、SHA 列表或指標表格。連結它們。
- 產品 UI 字串不是檔案。使用你的產品文案指南處理那些。
- 用 tab 縮排程式碼片段。寫真實路徑。讓每個計數或樹狀宣告在提交落地時為真，並包含重新生成它的指令。

<a id="worked-example"></a>

## 工作示例

之前：

> proto 匯入棘輪預算指令碼引數的設定透過 budget.json 執行。注意，重要的是要記得，使用 --write 執行，會更新已提交的預算以反映當前計數，應只在降低預算時執行。如果超過，CI 會失敗。

之後：

> `budget.mjs` 從 `budget.json` 讀取已提交的預算，並計算匯入 protos 的檔案數量。如果計數超過預算，CI 會失敗。僅在降低預算時執行 `budget.mjs --write`。
