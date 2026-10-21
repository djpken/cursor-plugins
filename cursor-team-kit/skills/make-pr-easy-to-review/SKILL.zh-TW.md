---
name: make-pr-easy-to-review
description: "透過清理雜亂的歷史記錄、改善 PR 描述，以及新增審查者指引來準備 PR 以供審查，且不變更程式碼行為。適用於「讓這個 PR 更容易審查」、「整理這個 PR」、「清理 commits」或「為 diff 新增註解」等情境。"
---

<a id="make-pr-easy-to-review"></a>

# 讓 PR 更容易審查

準備 PR，讓審查者能快速理解意圖、重要檔案與風險。預設目標是在不變更行為的前提下提升可審查性。

<a id="workflow"></a>

## 工作流程

1. 從使用者提供的 URL 或目前分支解析目標 PR。
2. 檢查 commits、diff 大小、變更路徑、產生的檔案以及 PR 描述。
3. 識別可審查性問題：雜亂的 commits、過時的描述、無關的變更、混合機械式與邏輯變更、缺少測試，或審查者入口不明確。
4. 在重寫歷史記錄或強制推送之前，先提出計畫。
5. 套用安全的改進措施，然後驗證樹狀結構或 diff 仍與預期的程式碼相符。

<a id="history-cleanup"></a>

## 歷史記錄清理

僅在使用者要求或同意計畫時才重寫歷史記錄。重寫前：

```bash
gh pr view <PR> --json title,headRefName,baseRefName,state,commits
git fetch origin <headRefName> <baseRefName>
ORIGINAL_TREE=$(git rev-parse origin/<headRefName>^{tree})
```

良好的 commit 分組通常遵循相依性順序：

1. Schema/儲存或產生的 API 定義。
2. 核心邏輯。
3. 接線與整合。
4. UI 或表面行為。
5. 測試。

重寫後，驗證內容一致性：

```bash
echo "Original tree: $ORIGINAL_TREE"
echo "Current tree:  $(git rev-parse HEAD^{tree})"
git diff origin/<headRefName> --stat
```

如果樹狀結構意外變更，請勿推送。

<a id="reviewer-guidance"></a>

## 審查者指引

當程式碼行為應保持不變時，優先使用 PR 描述與審查筆記：

- 新增與實際 diff 相符的 TL;DR。
- 將核心檔案與產生或機械式檔案分開。
- 指出有風險的行為變更、遷移順序、推出計畫以及測試覆蓋率。
- 當問題追蹤器、儀錶板或設計檔案能解釋意圖時，請連結它們。

<a id="guardrails"></a>

## 防護措施

- 切勿在「清理」中隱藏有意義的行為變更。
- 除非使用者明確要求，否則請勿略過 hooks。
- 如果 PR 過大，無法透過筆記使其可審查，建議拆分而非繞過問題進行修飾。
