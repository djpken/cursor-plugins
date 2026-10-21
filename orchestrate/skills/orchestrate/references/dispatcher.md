排程員的操作手冊。如果您是以以「You are the root planner for:」或「You are a subplanner for:」開頭的提示詞啟動的，請改讀 `planner.md`。

<a id="dispatcher"></a>

# 排程員

排程員為一次性執行。取得使用者的目標，透過 CLI 啟動雲端根規劃器，回傳 URL，然後停止。

<a id="the-job"></a>

## 任務

1. 取得使用者的目標。僅當目標缺失或含糊不清時才要求澄清。使用者是刻意選擇並行雲端編排的；只有當任務確實微不足道時才予以反對。
2. 使用該目標以及任何使用者指定的限制條件（模型覆寫、儲存庫覆寫）執行啟動 CLI。
3. 將 CLI 輸出中的 URL 回傳給使用者。停止。規劃器會自行驅動。

一次性設定：如果 `node_modules/` 缺失，請在此 skill 的 `scripts/` 目錄內執行 `bun install`。這些指令碼刻意放置在主機儲存庫的套件管理器工作區之外。

```bash
bun cli.ts kickoff "<goal>" [--repo <url>] [--ref main] [--model claude-opus-4-8] [--slack-channel C123] [--dispatcher-name "Alex"]
```

CLI 會讀取 `CURSOR_API_KEY`，從 `git config --get remote.origin.url` 自動偵測儲存庫，建立啟動提示詞，透過 `cursor-sdk` 啟動，並列印 `{ agentId, runId, status, url, dispatcherFirstName }` JSON。Slack 為選配。如果 `SLACK_BOT_TOKEN` 已設定，也請傳遞 `--slack-channel <id>` 或設定 `SLACK_CHANNEL_ID`；否則啟動會在啟動前失敗。如果 token 未設定，Slack 將保持停用。

<a id="dispatcher-identity"></a>

## 排程員身分

啟動 bot 的使用者名稱在第一個名稱解析成功時為 `<firstName>'s bot`，否則為 `orchestrate`。解析順序如下：

1. `--dispatcher-name "Alex"` 旗標。
2. 將 Slack `users.lookupByEmail` 與 `git config user.email` 進行比對（盡力而為；若缺少許可權或無匹配專案，則保持未設定）。

CLI 會透過啟動提示詞將解析後的名稱傳遞給根規劃器；規劃器會將 `plan.dispatcher = { firstName: "<name>" }` 寫入 `plan.json`。子任務會保留其自身的任務名稱作為 bot 身分。

<a id="run-summary"></a>

## 執行摘要

根規劃器會將 `plan.summary` 寫為 Slack 討論串中供人類參考的一行導向說明。啟動時會發布 `<rootSlug>: <summary> <agent-link>`；若沒有 `summary`，則會將 `goal` 截斷為約 200 個字元。`summary` 是供人類使用的；`goal` 則保留為面向 agent 的完整文字。

<a id="minimal-goal-discipline"></a>

## 最小目標紀律

原封不動地傳遞使用者的目標，不要加以擴充。不要新增規劃啟發式規則、子規劃器數量或結構性規定。規劃器會讀取 orchestrate skill 並自行決定分解方式。過度規定會將排程員的上下文洩漏到規劃器的視窗中，並使該 skill 無法作為規劃器判斷力的現實測試。

<a id="auth"></a>

## 驗證

`CURSOR_API_KEY` 必須是使用者 API 金鑰，而非團隊金鑰。驗證來源的優先順序記錄在 `cursor-sdk` skill 中（https://github.com/cursor/plugins/tree/main/cursor-sdk). 不要將金鑰串查詢烘焙到啟動 CLI 本身中；雲端 agent VM 沒有金鑰串。

<a id="observability-after-kickoff"></a>

## 啟動後的觀察性

排程後可觀察進度：

- 使用 `bun cli.ts crawl <repo-path> <branch> <root-slug>` 檢視深度樹狀檢視。
- 使用 `bun cli.ts status` 檢視頂層狀態。
- 當 `SLACK_BOT_TOKEN` 已設定時，使用 `plan.slackChannel` 中的 Slack 啟動討論串。

`syncStateToGit` 預設為 true。如果目標或交接不應被提交，請在根計畫上設定 `syncStateToGit: false`。
