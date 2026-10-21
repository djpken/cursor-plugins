---
name: review-and-ship
description: "檢視目前分支的錯誤、是否符合意圖以及測試覆蓋率；執行或撰寫測試；提交專注的工作；開啟或更新 PR。"
---

<a id="review-and-ship"></a>

# 檢視並發布

<a id="trigger"></a>

## 觸發

在發布前檢視變更。解決關鍵問題，驗證行為，並開啟或更新 PR。

<a id="workflow"></a>

## 工作流程

1. 收集背景資訊：與基礎分支的差異、未提交的變更、近期 commit、已變更的檔案，以及如有幫助，從近期相關對話中取得使用者意圖。
2. 針對已變更的行為執行特定測試。如果沒有專注的測試，決定是否要新增測試或記錄此缺口。
3. 檢視正確性、回歸問題、安全性以及是否符合意圖。對於較大的差異，使用並行 subagent。
4. 在最終確定前修復關鍵問題，並重新執行受影響的測試。
5. 提交選定的檔案，並附上簡潔的訊息。
6. 推送分支並開啟或更新 PR。

<a id="suggested-checks"></a>

## 建議檢查專案

```bash
git fetch origin main
git diff origin/main...HEAD
git status
gh pr checks --json name,bucket,state,workflow,link
```

<a id="guardrails"></a>

## 防護措施

- 優先處理正確性、安全性和回歸問題，而非僅限於風格的註解。
- 保持 commit 專注，避免變更無關的檔案。
- 如果 pre-commit 檢查失敗，請修復問題，而不是繞過 hooks。
- 在判斷 PR 就緒狀態時，使用 `gh pr checks` 而非僅限於 GitHub Actions 的命令。

<a id="output"></a>

## 輸出

- 發現摘要（關鍵、警告、備註）
- 已執行的測試及其結果
- PR URL
