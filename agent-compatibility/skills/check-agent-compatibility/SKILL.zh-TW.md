---
name: check-agent-compatibility
description: "執行完整的儲存庫相容性檢查：掃描器評分、啟動路徑、驗證迴圈，以及檔案可靠性。"
---

<a id="check-agent-compatibility"></a>

# 檢查 agent 相容性

<a id="trigger"></a>

## 觸發條件

當使用者希望對儲存庫執行完整的相容性檢查時使用。

<a id="workflow"></a>

## 工作流程

1. 啟動 `compatibility-scan-review` 以執行 CLI，並擷取原始儲存庫評分與主要問題。
2. 啟動 `startup-review` 以驗證該儲存庫是否真的能被 agent 啟動。
3. 啟動 `validation-review` 以檢查 agent 是否能在沒有不必要繁重迴圈的情況下驗證小型變更。
4. 啟動 `docs-reliability-review` 以檢視檔案中的設定與執行路徑是否可靠地符合實際情況。
5. 每個任務使用一個 subagent。不要將這些檢查合併到一個 agent 提示中。
6. 計算內部工作流程評分，取以下數值的四捨五入平均值：
   - `Startup Compatibility Score`
   - `Validation Loop Score`
   - `Docs Reliability Score`
7. 計算 `Agent Compatibility Score` 的方式如下：
   - `round((deterministic_score * 0.7) + (workflow_score * 0.3))`
8. 將結果綜合為一個最終回應。

在內部評分時，針對行為檢查使用具體的非整數工作流程評分，而非粗略的整數區間。如果啟動、驗證或檔案大致運作正常，應視為「良好但有摩擦」，而非預設為 60 分中段。不要僅因為紀錄雜訊多或錯誤文字粗糙就給出低工作流程評分。

<a id="output"></a>

## 輸出

以 markdown 格式回應，但保持精簡。不要使用圍欄程式碼區塊。

僅顯示一個評分，作為二級標題：`## Agent Compatibility Score: N/100`。除非使用者明確要求詳細說明，否則不要顯示其計算方式，包括權重、公式、確定性評分、工作流程評分、各檢查項評分或算術過程。

接著是一個扁平的優先順序清單，標題為 `Top fixes`，每行一個問題，每行以 `- ` 開頭。

如果確定性掃描器因工具環境問題無法執行，請單獨說明，不要將其視為儲存庫缺陷或對儲存庫進行扣分。將確定性與行為發現合併到該單一清單中，而非分成不同章節。專注於最能改善實際 agent 工作流程的修復。除非使用者要求更多細節，否則不要包含單獨的摘要。

範例格式：

<a id="agent-compatibility-score-72100"></a>

## Agent 相容性評分：72/100

首要修復專案
- 第一個問題
- 第二個問題
- 第三個問題
