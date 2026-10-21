---
name: swarm
description: "展開 N 個平行工作者，收集它們的結果，並返回一份報告。適用於 /swarm、'swarm this'、平行覆蓋、競賽、挑戰賽與探索。"
disable-model-invocation: true
---

# Swarm

In Claude Code, read [the Claude Code adapter](../../docs/harness/claude-code.md) once per session before you follow this skill. It maps the Cursor names in pstack skills to Claude Code.

<a id="start"></a>

## 開始

在啟動任何動作之前，開啟一個待辦事項列表，每個階段一個專案。

1. 框架
2. 展開
3. 彙整
4. 報告

<a id="phase-a-frame"></a>

## 階段 A：框架

1. 明確說明完成條件，以及 swarm 必須返回的成品或報告。
2. 選擇形式。分割成切片、讓 N 個工作者在相同簡報上競賽，或混合兩種方式。對於競賽或混合形式，在啟動前宣告 `first pass`、`rank all` 或 `best-of`。
4. 從 `~/.cursor/rules/pstack-models.mdc` 中的 `swarm workers` 行選擇工作者模型。如果規則或該行缺失，使用 `grok-4.7-xhigh-fast`。對於 `auto` 或 `inherit-parent`，省略 `model`，讓工作者在父模型上執行。如果 Task 工具拒絕一個 slug，使用預設並說明。如果它拒絕預設，從其錯誤訊息中選擇同家族中最接近的有效 slug。對於模型競賽，事先命名每個分支的模型。根據 **poteto-mode** 技能的 Subagents 部分，選擇每個子代理的推理等級和快速或標準速度。
5. 當工作者寫入時，給每個工作者自己的可寫輸出。當工作者驗證或測量 commits，每個簡報指定確切的 SHAs。測量簡報也指定方法（樣本數量、一個樣本的定義、順序）。工作者在結果中記錄這兩項。

<a id="phase-b-fan-out"></a>

## 階段 B：展開

在一個訊息中啟動所有 N 個工作者，使用 `subagent_type: generalPurpose`、`environment: "cloud"`、`run_in_background: true` 和步驟 4 的模型，對於 `auto` 或 `inherit-parent` 保持未設定。僅當工作者需要存取使用者電腦上的某些東西時，使用 `environment: "local"`。

當工作者必須從非預設的推送分支開始，傳遞 `cloud_base_branch`。

每個簡報獨立完整。包含目標、範圍、確切切片或競賽分支、如何驗證，以及要報告什麼。報告使用 `PASS`、`ISSUES` 或 `BLOCKED` 並附上證據。能證明缺陷的工作者報告 `ISSUES`，並列出它能證明每個問題，不只是第一個。

如果工作者退出，繼續使用 N-1 並記錄此事。

<a id="phase-c-aggregate"></a>

## 階段 C：彙整

讀取終端結果。如果結果未記錄其簡報指定的 SHAs 和方法，則丟棄該結果並重新啟動該工作者一次。第二次失敗後，記錄一個缺口。缺口不算為透過。對於覆蓋，每個必要的切片都需要一個結果。對於競賽，應用事先宣告的選擇規則。使用 first pass、rank all 或 best-of。不要貼上原始工作者輸出。

<a id="phase-d-report"></a>

## 階段 D：報告

返回一份整合的聊天報告，包含表格、問題摘要、缺口或退出，以及使用的競賽規則。
