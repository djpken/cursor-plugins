---
name: orchestrate
description: "僅在使用者明確輸入 `/orchestrate <goal>` 時使用，以分解大型任務，透過 Cursor SDK 啟動平行雲端 agent 工作者、子規劃器與驗證器的樹狀結構，並收集結構化交接；請勿自主呼叫。"
disable-model-invocation: true
---

<a id="orchestrate"></a>

# 協調

明確的 `/orchestrate <goal>` 會將大型任務分散至平行的 Cursor 雲端 agent。工作者之間不直接溝通；它們透過結構化交接向上層匯報。啟動、等待與交接迴圈位於 `scripts/cli.ts`。規劃器撰寫 `plan.json`，指令碼執行它，規劃器則讀取交接以決定下一步。長期執行的 agent 迴圈容易偏離軌道；使用 JSON 狀態檔案的指令碼能保持穩定。

**必讀：`cursor-sdk` 技能（[cursor/plugins/cursor-sdk](https://github.com/cursor/plugins/tree/main/cursor-sdk)）。啟動、驗證與錯誤分類法則都在那裡。不要重新實作該技能已記錄的內容。

<a id="setup"></a>

## 設定

- `CURSOR_API_KEY` 必須是個人或使用者金鑰。請從 [Cursor Dashboard > Integrations](https://cursor.com/dashboard/integrations) 建立，然後在使用前閱讀 `cursor-sdk` 驗證。
- `SLACK_BOT_TOKEN` 為選填。若已設定，請將 `--slack-channel <id>` 傳入 `kickoff` 或第一個 `run --root`，或設定 `SLACK_CHANNEL_ID`。指令碼會將頻道儲存在 `plan.slackChannel`，在那裡發布啟動執行緒，同步任務狀態，並讀取 Andon 反應。若未設定金鑰，指令碼會記錄一次紀錄並在無 Slack 可見性的情況下執行；正確性不會改變。

<a id="core-principles"></a>

## 核心原則

這些規則使樹狀結構能在無全域協調的情況下自我收斂。

1. **規劃器擁有範圍並發布任務。它們不進行編碼。** 撰寫 `plan.json`、讀取交接以及決定下一步是規劃器的工作。編輯檔案、執行 `git merge` 以及就地修復衝突則不是。如果規劃器有編碼的衝動，它應改為發布任務給工作者。
2. **規劃器不知道誰會接手其任務。** 指令碼將每個任務路由至雲端 agent。規劃器的心智模型保持在任務層級。
3. **工作者是隔離的。** 一個任務，一個儲存庫的克隆，與其他任何 agent 無通道。完成時進行一次交接。
4. **子規劃器是遞迴規劃器。** 規劃器發布一個「子規劃此切片」的任務；子規劃器完全擁有該切片，並回傳一個聚合的交接。
5. **透過交接實現持續動作。** 認為已完成的規劃器仍可收到延遲的交接並重新規劃。在規劃器決定停止發布之前，沒有「完成」狀態。
6. **傳播，而非同步。** 兄弟節點之間無交叉通訊。層級之間無共享狀態。每個層級僅能看到其子節點的交接。

<a id="node-types"></a>

## 節點型別

| 節點           | 執行迴圈？ | 範圍                            | 輸出                                  |
| -------------- | -------------- | -------------------------------- | --------------------------------------- |
| 規劃器        | 是            | 整個使用者目標                 | 面向使用者的訊息 + 選填 PR       |
| 子規劃器 (↻) | 是            | 父級範圍的一個切片      | 給父級的交接                       |
| 工作者         | 否             | 一個具體任務                | 給啟動規劃器的交接             |
| 驗證器       | 否             | 一個目標的驗收標準 | 給啟動規劃器的裁決交接     |
| Git            | 不適用            | 共享媒介                    | 分支（程式碼）+ handoffs/（意義）   |

<a id="role"></a>

## 角色

兩種角色，一個技能。請閱讀您角色的參考檔案，並跳過另一個。

**排程器。** 您處於本地 IDE 會話中，且使用者輸入 `/orchestrate <goal>`。您的工作是啟動雲端根規劃器並回傳其 URL。請參閱 `references/dispatcher.md`。一次性操作；您不是規劃器。

**規劃器（根或子）。** 您是被啟動的，其結構化提示以「You are the root planner for:」或「You are a subplanner for:」開頭。或者使用者選擇在本地執行規劃迴圈。您擁有一個範圍，發布任務，讀取交接，並決定下一步。請參閱 `references/planner.md`。

`disable-model-invocation: true` 表示此技能僅在明確呼叫時載入。
