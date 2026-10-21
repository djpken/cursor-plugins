---
name: ralph-loop
description: "啟動 Ralph 迴圈進行迭代自我參考開發。當使用者要求執行 ralph 迴圈、開始迭代迴圈，或希望在任務完成前進行多次自主迭代時使用。"
---

<a id="ralph-loop"></a>

# Ralph 迴圈

<a id="trigger"></a>

## 觸發條件

使用者想要啟動 Ralph 迴圈。一種迭代開發迴圈，每次回合後將相同的提示回饋，且代理在每次迭代中能看到自己的先前工作。

<a id="workflow"></a>

## 工作流程

1. 收集使用者任務提示和可選引數：
   - `max_iterations`（數字，預設為 0 表示無限制）
   - `completion_promise`（文字，若未設定則為 "null"）

2. 如果目錄 `.cursor/ralph/` 不存在，則建立它，然後在 `.cursor/ralph/scratchpad.md` 寫入狀態檔案，格式如下：

   ```markdown
   ---
   iteration: 1
   max_iterations: <N or 0>
   completion_promise: "<TEXT>" or null
   ---

   <the user's task prompt goes here>
   ```

   示例：
   ```markdown
   ---
   iteration: 1
   max_iterations: 20
   completion_promise: "COMPLETE"
   ---

   Build a REST API for todos with CRUD operations, input validation, and tests.
   ```

3. 向使用者確認 Ralph 迴圈已啟動，然後開始處理任務。

4. 停止鉤子自動攔截每個回合結束，並將相同的提示作為後續訊息回饋。您會看到它以 `[Ralph loop iteration N.]` 字首標示。

<a id="guardrails"></a>

## 防護措施

- 如果設定了完成承諾，您只能在該陳述完全且真實時輸出 `<promise>TEXT</promise>`。
- 不要輸出虛假承諾來逃避迴圈。
- 始終建議設定 `max_iterations` 作為安全網。
- 如果 `completion_promise` 值包含特殊字元，請在 YAML frontmatter 中引用該值。

<a id="output"></a>

## 輸出

確認迴圈已啟動（提示、迭代限制、承諾若設定），然後立即開始處理任務。
