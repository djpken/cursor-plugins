---
name: continual-learning
description: "透過將對話記錄挖掘與 AGENTS.md 更新委派給 `agents-memory-updater`，來協調持續學習。"
disable-model-invocation: true
---

<a id="continual-learning"></a>

# 持續學習

透過將記憶更新流程委派給一個子代理，來保持 `AGENTS.md` 為最新狀態。

<a id="trigger"></a>

## 觸發條件

當使用者要求挖掘先前對話、維護 `AGENTS.md`，或執行持續學習迴圈時使用。

<a id="workflow"></a>

## 工作流程

1. 呼叫 `agents-memory-updater`。
2. 回傳更新器的結果。

<a id="guardrails"></a>

## 防護措施

- 保持父級 skill 僅負責協調。
- 不要在父級流程中挖掘對話記錄或編輯檔案。
- 不要繞過子代理。
