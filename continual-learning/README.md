<a id="continual-learning"></a>

# 持續學習

自動且增量地根據 transcript 的變更，讓 `AGENTS.md` 保持最新狀態。

此 plugin 結合了以下元件：

- 一個 `stop` hook，用於決定何時觸發學習。
- 一個 `continual-learning` skill，用於協調學習流程。
- 一個 `agents-memory-updater` subagent，用於挖掘新增或變更的 transcript，並更新 `AGENTS.md`。

它透過以下方式設計，以避免雜訊過多的重寫：

- 先讀取現有的 `AGENTS.md`，並就地更新相符的專案。
- 僅處理新增或變更的 transcript 檔案。
- 僅寫入純文字專案（不包含證據或信心度 metadata）。

<a id="installation"></a>

## 安裝

```bash
/add-plugin continual-learning
```

<a id="how-it-works"></a>

## 運作方式

在符合條件的 `stop` 事件中，hook 可能會發出 `followup_message`，要求 agent 執行 `continual-learning` skill。

此 skill 被標記為 `disable-model-invocation: true`，因此在正常的模型呼叫期間不會被自動選取。當它執行時，會將完整的 memory 更新流程委派給 `agents-memory-updater`。

hook 在以下位置保留本機 runtime 狀態：

- `.cursor/hooks/state/continual-learning.json`（cadence 狀態）

updater 使用位於以下位置的增量 transcript 索引：

- `.cursor/hooks/state/continual-learning-index.json`

<a id="trigger-cadence"></a>

## 觸發頻率

預設頻率：

- 最少 10 個已完成的回合
- 距離上次執行至少 120 分鐘
- transcript 的 mtime 必須自上次執行以來有所推進

試行模式預設值（在此 plugin hook 設定中啟用）：

- 最少 3 個已完成的回合
- 最少 15 分鐘
- 24 小時後自動過期，然後回退至預設頻率

<a id="optional-env-overrides"></a>

## 選用的環境變數覆寫

- `CONTINUAL_LEARNING_MIN_TURNS`（或舊版 `CONTINUOUS_LEARNING_MIN_TURNS`）
- `CONTINUAL_LEARNING_MIN_MINUTES`（或舊版 `CONTINUOUS_LEARNING_MIN_MINUTES`）
- `CONTINUAL_LEARNING_TRIAL_MODE`（或舊版 `CONTINUOUS_LEARNING_TRIAL_MODE`）
- `CONTINUAL_LEARNING_TRIAL_MIN_TURNS`（或舊版 `CONTINUOUS_LEARNING_TRIAL_MIN_TURNS`）
- `CONTINUAL_LEARNING_TRIAL_MIN_MINUTES`（或舊版 `CONTINUOUS_LEARNING_TRIAL_MIN_MINUTES`）
- `CONTINUAL_LEARNING_TRIAL_DURATION_MINUTES`（或舊版 `CONTINUOUS_LEARNING_TRIAL_DURATION_MINUTES`）

<a id="output-format-in-agentsmd"></a>

## AGENTS.md 中的輸出格式

memory updater 僅寫入：

- `## Learned User Preferences`
- `## Learned Workspace Facts`

每個專案都是一個純文字專案。

<a id="license"></a>

## 授權條款

MIT
