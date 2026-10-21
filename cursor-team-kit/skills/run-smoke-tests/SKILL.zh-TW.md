---
name: run-smoke-tests
description: "執行 Playwright 冒煙測試、偵錯失敗專案並驗證修正"
---

<a id="run-smoke-tests"></a>

# 執行冒煙測試

<a id="trigger"></a>

## 觸發

在變更前後需要端到端冒煙驗證。

<a id="workflow"></a>

## 工作流程

1. 建置目標應用程式的前置條件。
2. 執行相關的冒煙測試套件或特定的測試檔案。
3. 如果失敗，檢查追蹤記錄或紀錄並隔離根本原因。
4. 套用最小修正並重新執行，直到穩定為止。

<a id="example-commands"></a>

## 範例指令

```bash
# Run full smoke suite
npm run smoketest

# Run a specific smoke test file
npm run smoketest -- path/to/test.spec.ts

# Faster iteration when build artifacts are ready
npm run smoketest-no-compile -- path/to/test.spec.ts
```

<a id="guardrails"></a>

## 防護措施

- 優先使用確定性的等待和斷言，而非脆弱的逾時設定。
- 重新執行已透過的修正，以降低不穩定測試造成的誤報。
- 僅在明確要求並記錄在案時，才將測試隔離。

<a id="output"></a>

## 輸出

- 測試結果摘要
- 根本原因與修正
- 剩餘的不穩定風險（如有）
