---
name: loop-on-ci
description: "監控 PR 檢查並修復失敗專案，直到全部透過。使用 gh pr checks 作為 PR 相關檢查的資訊來源。"
---

<a id="loop-on-ci"></a>

# CI 迴圈

<a id="trigger"></a>

## 觸發條件

需要監控某個分支或 pull request，並針對 CI 失敗進行迭代，直到所有必要檢查都透過。

使用 `gh pr checks` 作為資訊來源。它包含所有 PR 相關檢查，而 `gh run list` 僅涵蓋 GitHub Actions。

<a id="workflow"></a>

## 工作流程

1. 解析目前分支對應的 PR。
2. 在等待之前，先檢查目前的 PR 檢查狀態。
3. 如果檢查已經失敗，請先診斷這些失敗專案。
4. 如果檢查仍在進行中，請使用 `gh pr checks --watch --fail-fast` 進行監控。
5. 每次推送後，重新檢查完整的 PR 檢查集，並重複此過程直到全部透過。

<a id="commands"></a>

## 指令

```bash
# Resolve the active PR
gh pr view --json number,url,headRefName

# Inspect all attached checks
gh pr checks --json name,bucket,state,workflow,link

# Watch pending checks and fail fast
gh pr checks --watch --fail-fast

# GitHub Actions logs, when the failing check links to a GHA run
gh run view <run-id> --log-failed
```

<a id="guardrails"></a>

## 限制條件

- 在可能的情況下，將每次修復範圍限定在單一失敗原因。
- 不要繞過 hooks (`--no-verify`) 來強制推進進度。
- 如果失敗明顯與 PR 無關，且在 main 分支上已修復，請合併最新的 main 分支，而不是用無關的修復來膨脹 PR。
- 如果失敗是不穩定的，請重試一次並報告不穩定證據。
- 每次推送後重新執行 `gh pr checks --json name,bucket,state,workflow,link`；檢查集可能會改變。

<a id="output"></a>

## 輸出

- 目前 CI 狀態
- 失敗摘要與已套用的修復
- 檢查透過後的 PR URL
