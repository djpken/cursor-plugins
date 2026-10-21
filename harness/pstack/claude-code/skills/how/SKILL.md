---
name: how
description: "適用於「X 是如何運作的」這類問題、在修改程式碼前的程式碼導覽，以及放置位置、所有權或分層問題（「這應該放在哪裡」、「哪個套件擁有這個」、「這是正確的分層嗎」）。說明子系統架構、執行時流程以及入門心智模型。使用 why 來說明動機。"
disable-model-invocation: true
---

# How

In Claude Code, read [the Claude Code adapter](../../docs/harness/claude-code.md) once per session before you follow this skill. It maps the Cursor names in pstack skills to Claude Code.

探索程式碼庫以回答「X 是如何運作的？」這類問題。產出架構說明，深度應相當於資深工程師進入子系統時的水平，足以建立運作的心智模型，但不必多到像加註釋的原始碼。

每次啟動都是一個具有 `subagent_type: generalPurpose` 和 `readonly: true` 的 Task 子代理。探索者使用 `pstack-models.mdc` 規則中的 `how explorer` 行（預設為 `grok-4.7-xhigh-fast`），而每個說明者使用 `how explainer` 行（預設為 `claude-opus-5-5-xhigh`）。將 `model` 設定為該行的值，若規則或行不存在則設定為預設值。當值為 `auto` 或 `inherit-parent` 時，保持 `model` 未設定。如果 Task 工具拒絕某個 slug，請使用預設值並說明。如果它拒絕預設值，請使用其錯誤訊息中同一族最接近的有效 slug。根據 **poteto-mode** skill 的 Subagents 章節，為每個子代理選擇推理層級以及快速或標準速度。

<a id="1-assess-complexity"></a>

## 1. 評估複雜度

如果範圍不明確，請陳述您的解讀並進行探索。使用者可以重新導向。

- **簡單**（單一模組、小型工具、狹窄的問題，例如「函式 X 是如何運作的」）：不需要探索者。啟動一個說明者，在單次行程中進行探索和說明，該說明者由 `references/explainer-prompt.md` 建構，但不包含探索者發現章節。前往步驟 4。
- **複雜**（跨越多個檔案或服務的子系統、橫切功能、完整的架構概覽）：前往步驟 2。

當猶豫不決時，選擇簡單的路徑。

<a id="2-explore-complex-only"></a>

## 2. 探索（僅限複雜情況）

將問題分解為 2 到 4 個角度，每個角度都是子系統的不同切面。在單則訊息中啟動所有探索者，每個都填入 `references/explorer-prompt.md` 及其角度。

<a id="3-synthesize-complex-only"></a>

## 3. 綜合（僅限複雜情況）

當所有探索者都返回後，啟動一個說明者，填入 `references/explainer-prompt.md` 以及每個探索者的發現。

<a id="4-present"></a>

## 4. 呈現

呈現說明者的輸出。為了清晰度或對話脈絡而進行輕微編輯是可以的。不要大幅重寫。

<a id="output-format"></a>

## 輸出格式

說明使用 `references/explainer-prompt.md` 中定義的章節，並移除任何不適用於此的章節：概覽、關鍵概念、運作方式、位置分佈、注意事項。
