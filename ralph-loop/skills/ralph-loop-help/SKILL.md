---
name: ralph-loop-help
description: "說明 Ralph Loop 外掛、其工作原理及可用技能。當使用者詢問 ralph loop 的協助、想了解此技術或需要使用範例時使用。"
---

<a id="ralph-loop-help"></a>

# Ralph Loop 協助

<a id="trigger"></a>

## 觸發條件

使用者詢問 Ralph Loop 是什麼、如何運作，或需要使用指引。

<a id="what-to-explain"></a>

## 說明內容

<a id="what-is-ralph-loop"></a>

### Ralph Loop 是什麼？

Ralph Loop 實作 Ralph Wiggum 技術，一種基於持續 AI 迴圈的迭代開發方法，由 Geoffrey Huntley 率先提出。

核心概念：相同的提示會反覆輸入給代理。"自我參考"的部分來自代理在檔案和 git 歷史中看到自己先前的工作，而非將輸出回饋作為輸入。

每個迭代：
1. 代理收到相同的提示
2. 處理任務，修改檔案
3. 嘗試退出
4. 停止攔截器攔截並再次輸入相同的提示
5. 代理在檔案中看到先前的工作
6. 迭代改善直到完成

<a id="starting-a-ralph-loop"></a>

### 啟動 Ralph Loop

告訴代理您的任務並附上選項：

```
Start a ralph loop: "Build a REST API for todos" --max-iterations 20 --completion-promise "COMPLETE"
```

選項：
- 自動停止前的最大迭代次數：`--max-iterations N`
- 表示完成的短語：`--completion-promise "TEXT"`

工作原理：
1. 建立 `.cursor/ralph/scratchpad.md` 狀態檔案
2. 代理處理任務
3. 停止攔截器攔截退出並將相同的提示回饋
4. 代理看到先前的工作並迭代
5. 持續直到偵測到承諾或達到最大迭代次數

<a id="cancelling-a-ralph-loop"></a>

### 取消 Ralph Loop

請代理取消 ralph loop。它會移除狀態檔案並報告迭代次數。

<a id="completion-promises"></a>

### 完成承諾

為了表示完成，代理輸出 `<promise>` 標記：

```
<promise>TASK COMPLETE</promise>
```

停止攔截器尋找此特定標記。若沒有它（或 `--max-iterations`），Ralph 會無限執行。

<a id="when-to-use-ralph"></a>

### 何時使用 Ralph

**適用於：**
- 明確定義的任務且有清晰的成功標準
- 需要迭代和改善的任務
- 需要自我修正的迭代開發
- 全新專案

**不適用於：**
- 需要人類判斷或設計決策的任務
- 一次性操作
- 成功標準不明確的任務

<a id="learn-more"></a>

### 瞭解更多

- 原始技術：https://ghuntley.com/ralph/
- Ralph Orchestrator：https://github.com/mikeyobrien/ralph-orchestrator

<a id="output"></a>

## 輸出

將上述資訊清晰呈現給使用者，並針對其特定問題調整內容。
