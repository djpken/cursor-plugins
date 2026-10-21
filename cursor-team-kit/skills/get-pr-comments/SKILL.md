---
name: get-pr-comments
description: "擷取並摘要來自目前 pull request 的審查意見"
---

<a id="get-pr-comments"></a>

# 取得 PR 意見

<a id="trigger"></a>

## 觸發

需要一份簡潔且可執行、針對目前 pull request 回饋的摘要。

<a id="workflow"></a>

## 工作流程

1. 解析目前分支對應的 active PR。
2. 擷取審查意見與討論意見。
3. 依嚴重程度與可執行性對回饋進行分組。
4. 回傳一份簡潔的行動清單。

<a id="output"></a>

## 輸出

- 分組後的回饋摘要
- 依優先順序排列的行動清單
- 仍需釐清的未決問題
