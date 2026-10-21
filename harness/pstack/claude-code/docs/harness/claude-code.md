<a id="pstack-in-claude-code"></a>

# Claude Code 中的 pstack

pstack skills 使用 Cursor 名稱。在 Claude Code 中，每個 session 只需讀取此檔案一次，然後使用下表將 skill 中的每個 Cursor 名稱進行對應。`<pstack>` 代表存放 `skills/` 和 `docs/` 的資料夾。如果某個 Cursor 欄位沒有對應的列，請略過該欄位並說明原因。切勿傳遞工具 schema 拒絕的欄位或值。

<a id="tools"></a>

## 工具

| Cursor | Claude Code |
| --- | --- |
| `Task` | `Agent` |
| `AskQuestion` | `AskUserQuestion`。在 `-p` 模式下此工具不存在，因此請以純文字詢問。 |
| 開啟 todolist | 如果 session 提供 `TodoWrite`，請使用它。否則，請在回覆中保留清單，並在每個階段更新它。 |
| Run another pstack skill：執行另一個 pstack skill（`/name` 或「**name** skill」） | `Read` `<pstack>/skills/<name>/SKILL.md` 並遵循其指示。`Skill` 工具會拒絕大多數 pstack skills。當您告訴使用者輸入指令時，請寫 `/pstack:<name>`。 |
| `Grep`, `Glob` | 如果 session 沒有 `Grep` 或 `Glob`，請使用 `Bash` 搭配 `rg`。 |

<a id="subagent-fields"></a>

## Subagent 欄位

| Cursor | Claude Code |
| --- | --- |
| `subagent_type: generalPurpose` | `subagent_type: "general-purpose"` |
| `poteto-agent`, `comment-sicko` | `pstack:poteto-agent`, `pstack:comment-sicko` |
| `readonly: true` | `subagent_type: "Explore"`。它會保留 `Bash`，因此簡報中也必須寫明「不要編輯檔案」，並且您要在事後檢查 `git status`。 |
| `readonly: false` | `general-purpose`。它會保留 session 的 MCP 伺服器。 |
| `environment: "cloud"` | `isolation: "worktree"`。請指定 worker 要 checkout 的分支或 commit。 |
| `environment: "local"` | 省略。 |
| `cloud_base_branch: <b>` | 告訴 worker 先 checkout `<b>`。 |
| `run_in_background: true` | 省略。Subagents 預設在背景執行，完成時會通知您。 |
| `resume` | 啟動一個新的 agent，並將先前的結果貼到其簡報中。 |
| `file_attachments` | 在簡報中列出路徑。 |

<a id="models"></a>

## 模型

| Cursor | Claude Code |
| --- | --- |
| `claude-<family>-<version>-<effort>[-fast]`，例如 `claude-opus-5-5-xhigh` | `model: "opus"` 和 `effort: "xhigh"`。`model` 只接受 `opus`、`sonnet`、`haiku`、`fable`。略過 `-fast`。 |
| 一個 `grok-*`、`gpt-*` 或其他非 Claude slug | 省略 `model`，讓 agent 使用 session 模型，並說明此情況。 |
| `auto` 或 `inherit-parent` | 省略 `model`。 |
| `pstack-models.mdc` 規則，`~/.cursor/rules/pstack-models.mdc` | `~/.claude/rules/pstack-models.md`。Claude Code 會將其載入每個新的 session，因此其角色行已在上下文中。如果不在，請讀取該檔案。如果檔案或該角色的行缺失，請使用 skill 的預設 slug。 |
| 一個角色值 `<model> <effort>`，例如 `opus high` | `model: "opus"` 和 `effort: "high"`。 |

## setup-pstack

Claude Code 會將 `~/.claude/rules/` 中的每個 `.md` 檔案載入到每個新的 session，因此 `setup-pstack` 會將其規則寫入該處。請遵循其步驟，並進行以下變更：

- 偵測。偵測到的模型是 `Agent` 工具 schema 列出的 `model` 值，每個值都帶有來自其 `effort` 值的 effort。將真實模型寫為 `<model> <effort>`，例如 `opus xhigh`。effort 是最後一個 token，因此預算步驟可照原文運作。
- 預設值。使用上方的列對應每個預設 slug：`claude-opus-5-5-xhigh` 是 `opus xhigh`，而 `grok-4.7-xhigh-fast` 是 `inherit-parent`。
- 寫入。寫入 `~/.claude/rules/pstack-models.md`，不含 frontmatter，然後是註解行、`# budget` 行，以及第 5 步中每個角色的一行。覆寫整個檔案。切勿將角色行放入 `CLAUDE.md` 或 `AGENTS.md`。

<a id="paths"></a>

## 路徑

| Cursor | Claude Code |
| --- | --- |
| `.cursor/skills/`, `~/.cursor/skills/` | `.claude/skills/`, `~/.claude/skills/` |
| `~/.cursor/plugins/`, `.cursor-plugin/plugin.json` | `~/.claude/plugins/`, `.claude-plugin/plugin.json` |
| `.cursor/rules/*.mdc`，永遠套用的規則 | `CLAUDE.md`，或當不存在 `CLAUDE.md` 時使用 `AGENTS.md`。Claude Code 永遠不讀取 `.mdc` 檔案。模型規則是例外。請參閱模型。 |
| `https://github.com/cursor/plugins/blob/main/pstack/<path>` | 當該檔案存在時，使用 `<pstack>/<path>`。plugin 隨附 skills、`docs/guide/` 和 `README.md`。請讀取本地副本，切勿抓取遠端。當使用者需要連結時，請提供 URL。 |
| `~/.cursor/projects/<slug>/agent-transcripts/` 中的 Transcripts | `~/.claude/projects/<slug>/<session>.jsonl`。slug 是將 workspace 路徑中的每個 `/` 和 `.` 改為 `-` 後的結果，因此 `/Users/you/proj` 是 `-Users-you-proj`。該資料夾中最新的 `.jsonl` 即為目前的 session。其 `<session>/subagents/` 資料夾存放 subagent transcripts。請只讀取此 workspace 的資料夾。 |

<a id="cursor-only-features"></a>

## Cursor-only features：僅限 Cursor 的功能

Claude Code 沒有 Cursor Automations、Bugbot、Cursor cloud agents 或內建 PR 工具。當某個步驟需要這些功能時，請在回覆中寫一行 `Skipped in Claude Code: <step>. <reason>.`，然後繼續下一步。不要停止、詢問或重試。當後續步驟需要被略過步驟產生的內容（例如 URL 或金鑰）時，請留下佔位符並在那一行中標明其名稱。

| Cursor | Claude Code |
| --- | --- |
| Cursor Automations：常規、`update_state` 常規呼叫、`api2.cursor.sh/automations` webhooks、常規喚醒，以及 `SendToUser` 秘密請求 | 使用該行略過。 |
| Bugbot | GitHub PR 上的 Bugbot 評論是普通的審查執行緒，因此請使用該步驟提供的 forge 命令進行分級。如果 PR 沒有 Bugbot 檢查或評論，請略過啟動、等待或設定 Bugbot 的步驟。 |
| Cursor cloud agents | `environment: "cloud"` 對應到 worktree，如 Subagent 欄位所述。請略過需要 cloud agent 本身的步驟，例如其儀錶板狀態或其自身的機器。 |
| 內建 PR 工具 | 使用該步驟提供的 forge 命令，例如 `gh pr create`。這不算略過，因此不要寫那行。 |
