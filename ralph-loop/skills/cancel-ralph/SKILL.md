---
name: cancel-ralph
description: "取消正在執行中的 Ralph 迴圈。當使用者想要停止、取消或中止正在執行的 ralph 迴圈時使用。"
---

<a id="cancel-ralph"></a>

# 取消 Ralph

<a id="trigger"></a>

## 觸發

使用者想要取消或停止正在執行中的 Ralph 迴圈。

<a id="workflow"></a>

## 工作流程

1. 檢查是否存在 `.cursor/ralph/scratchpad.md`。

2. **如果不存在**：告訴使用者"No active Ralph loop found."

3. **如果存在**：
   - 讀取 `.cursor/ralph/scratchpad.md` 以從 `iteration:` 欄位取得目前的迭代數。
   - 移除狀態檔案及任何完成標記：
     ```bash
     rm -rf .cursor/ralph
     ```
   - 報告："Cancelled Ralph loop (was at iteration N)."

<a id="output"></a>

## 輸出

包含迭代數的簡短確認訊息，或告知沒有正在執行中的迴圈。
