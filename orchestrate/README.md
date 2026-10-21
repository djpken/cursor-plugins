<a id="orchestrate"></a>

# 協調

透過 Cursor SDK 將大型任務分散至並行的 Cursor 雲端 agent。Planner 發布任務，worker 將結果回傳，指令碼則從磁碟與 git 協調樹狀結構，使 spawn / wait / handoff 迴圈持續收斂，而無需長期執行的 agent 狀態。

該 skill 本身位於 [`skills/orchestrate/SKILL.md`](./skills/orchestrate/SKILL.md)。請閱讀該檔案以取得完整的操作手冊；此 README 僅涵蓋在呼叫它之前需要設定的內容。

<a id="prerequisites"></a>

## 先決條件

- PATH 中需有 `bun`。
- `CURSOR_API_KEY` 中需有 Cursor API 金鑰。
- 若希望有 Slack 執行緒來映象執行過程，則需要選配的 Slack 應用程式與 bot token。

<a id="cursor-api-key"></a>

## Cursor API 金鑰

1. 開啟 [https://cursor.com/dashboard/integrations](https://cursor.com/dashboard/integrations)。
2. 建立個人使用者 API 金鑰。該值以 `cursor_` 開頭。
3. 匯出它：`export CURSOR_API_KEY="cursor_..."`。

團隊服務帳戶金鑰（Team Settings → Service accounts）也適用於本地與雲端執行。請參閱 [`cursor-sdk` plugin](https://github.com/cursor/plugins/tree/main/cursor-sdk) 以瞭解完整的驗證模型。

<a id="slack-app-optional"></a>

## Slack 應用程式（選配）

Slack 可見性是選配的。當 token 未設定時，指令碼會記錄一次紀錄並在不使用 Slack 的情況下執行；正確性不會改變。若要啟用：

1. 在 [https://api.slack.com/apps](https://api.slack.com/apps) → **From scratch** 建立 Slack 應用程式。選擇一個名稱與工作區。
2. 在 **OAuth & Permissions** → **Bot Token Scopes** 下，新增：

   | Scope | 原因 |
   | --- | --- |
   | `chat:write` | 發布與編輯訊息。 |
   | `chat:write.customize` | 在每則發布中設定 bot 使用者名稱與圖示。 |
   | `chat:write.public` | 在公開頻道中發布訊息，無需先邀請 bot。 |
   | `files:write` | 將 handoff 產物上傳至執行執行緒。 |
   | `files:read` | 與 `files:write` 配對，用於 upload v2 流程。 |
   | `reactions:read` | 監控開場訊息上的 Andon `:rotating_light:` 反應。 |
   | `channels:history` | 讀取執行緒回覆。如果您的執行頻道是私有的，請改用 `groups:history`。 |

   選配但建議：

   | Scope | 原因 |
   | --- | --- |
   | `users:read.email` | 從 `git config user.email` 解析排程員的名。若未設定，請明確傳遞 `--dispatcher-name`。 |

3. **Install to Workspace** 並複製 **Bot User OAuth Token**（`xoxb-...`）。
4. 匯出它：`export SLACK_BOT_TOKEN="xoxb-..."`。
5. 邀請 bot 加入您希望執行執行緒所在的頻道（`/invite @your-bot`）。具有 `chat:write.public` 的公開頻道可跳過此步驟；私有頻道需要邀請。
6. 取得頻道 ID。在 Slack 中：右鍵點選頻道 → **View channel details** → 對話方塊底部。透過 `kickoff` 上的 `--slack-channel <id>` 傳遞它（或設定 `SLACK_CHANNEL_ID`）。首次開場會將 id 持久化到計畫中；subplanners 與後續的 `run` 呼叫會繼承它。

<a id="install"></a>

## 安裝

```bash
cd skills/orchestrate/scripts
bun install
```

指令碼刻意放置在宿主儲存庫的套件管理器工作區之外。

<a id="invoke"></a>

## 呼叫

```bash
bun skills/orchestrate/scripts/cli.ts kickoff "<goal>" \
  [--repo <url>] [--ref main] [--model claude-opus-4-7] \
  [--slack-channel <id>] [--dispatcher-name "<first name>"]
```

CLI 會列印 `{ agentId, runId, status, url }`；從那裡開始，雲端根 planner 會自行驅動。請參閱 skill 以瞭解 `run`、`spawn`、`respawn`、`kill`、`tail`、`comment` 和 `andon` 子命令。

<a id="license"></a>

## 授權條款

MIT。請參閱 [`LICENSE`](./LICENSE)。
