---
name: fix-ci
description: "找出失敗的 PR 檢查，檢視記錄或外部檢查連結，並套用針對性的修正"
---

<a id="fix-ci"></a>

# 修正 CI

<a id="trigger"></a>

## 觸發

分支或 PR 的 CI 失敗，需要快速且反覆的途徑來讓檢查變為綠色。

<a id="workflow"></a>

## 工作流程

1. 解析目前的 PR 並檢視 `gh pr checks --json name,bucket,state,workflow,link`。
2. 檢視失敗的工作並提取第一個可採取行動的錯誤。若可用，請使用 GitHub Actions 記錄；否則使用檢查連結來識別失敗的指令或服務。
3. 套用最小且安全的修正。
4. 推送，重新檢查 PR 檢查集，並重複執行直到變為綠色。

<a id="guardrails"></a>

## 防護措施

- 一次只修正一個可採取行動的失敗。
- 在進行更廣泛的重構之前，優先選擇最小且低風險的變更。
- 將 `gh pr checks` 保留為整體 PR CI 狀態的單一事實來源。

<a id="output"></a>

## 輸出

- 主要失敗的工作與根本錯誤
- 依迭代順序套用的修正
- 目前的 CI 狀態與下一步行動
