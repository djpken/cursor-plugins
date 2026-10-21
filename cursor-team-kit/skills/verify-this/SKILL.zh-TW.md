---
name: verify-this
description: "使用新的本地證據驗證宣告：以可證偽的方式重述宣告，擷取基準與處理結果，比較產出物，並回傳 VERIFIED、NOT VERIFIED 或 INCONCLUSIVE。"
---

<a id="verify-this"></a>

# 驗證此項

驗證並非摘要。它使用可重複的證據來證明或反證特定宣告。

<a id="when-to-use"></a>

## 使用時機

- 當使用者詢問「驗證這個」、「證明它有效」、「這是否修復了問題」或「給我證據」時。
- 錯誤修復需要修復前後的再現步驟。
- UI、CLI、API、效能或記憶體宣告需要測量。
- 測試透過，但使用者可見的行為仍需確認。

不要將此用於模糊宣告，例如「程式碼更乾淨」。請先要求可測量的宣告。

<a id="workflow"></a>

## 工作流程

1. 以可證偽的形式重述宣告：條件、指標與閾值。
2. 選擇能反證該宣告的最小本地範圍。
3. 從舊狀態擷取基準：合併基準、父 commit、失敗的分支或目前損壞的再現步驟。
4. 使用相同的命令、資料、預熱與環境，從變更後的狀態擷取處理結果。
5. 比較原始產出物：數字、螢幕截圖、終端機記錄、HTTP 回應、效能分析、堆積快照或測試輸出。
6. 回傳確切的一個判定：`VERIFIED`、`NOT VERIFIED` 或 `INCONCLUSIVE`。

<a id="local-surfaces"></a>

## 本地範圍

- 程式碼行為：專注的單元/整合測試或最小再現指令碼。
- CLI/TUI 行為：`control-cli`、終端機記錄或示範錄影。
- UI 行為：`control-ui`、螢幕截圖、無障礙快照或瀏覽器追蹤。
- API 行為：本地 HTTP/RPC 請求與回應差異。
- 效能：同一臺機器上的基準/處理結果計時或 CPU 效能分析。
- 記憶體：疑似操作前後堆積快照。

<a id="artifact-layout"></a>

## 產出物佈局

當可以安全地寫入產出物時：

```text
/tmp/verify-this/<claim-slug>/
├── claim.md
├── timeline.md
├── baseline/
├── treatment/
├── diff/
└── verdict.md
```

如果產出物可能包含敏感程式碼、提示詞、螢幕截圖、HTTP 主體或堆積資料，除非使用者同意儲存到磁碟，否則僅保留最小化的行內證據。

<a id="verdict-rules"></a>

## 判定規則

- `VERIFIED`：基準與處理結果在預測方向上存在差異，達到宣告的閾值，且無明顯混淆因素。
- `NOT VERIFIED`：行為未改變、方向錯誤或未達到閾值。
- `INCONCLUSIVE`：無有效基準、訊號雜訊、測量失敗或環境差異使比較無效。

<a id="output"></a>

## 輸出

使用此格式：

```text
VERIFIED | NOT VERIFIED | INCONCLUSIVE
Claim: <falsifiable claim>

Evidence:
<metric/artifact>: baseline=<...>, treatment=<...>, delta=<...>, threshold=<...>

Reasoning:
<one tight paragraph naming the evidence and any confounds>
```

不要淡化負面結果。明確的 `NOT VERIFIED` 是有用的。
