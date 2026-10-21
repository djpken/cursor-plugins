# dyl-stack

我的 agent 風格，疊加在 [pstack](../pstack/) 之上。pstack 負責繁重的基礎工作：原則、操作手冊、subagent 路由。dyl-stack 則補充了我經常需要糾正 agent 的幾道閘門、一份可以貼上即用的 PR 審查，以及一個 Figma-to-UI 流程，該流程在視覺效果正確之前不會宣告完成。

<a id="install"></a>

## 安裝

```bash
/add-plugin pstack
/add-plugin cursor-team-kit
/add-plugin thermos
/add-plugin dyl-stack
```

`/build-figma` 也需要連線 Figma plugin 及其 MCP。

## Skills

| Skill | 使用時機 |
|---|---|
| [`/dyl-mode`](./skills/dyl-mode/SKILL.md) | 非平凡工作的預設入口。透過 pstack 的 `poteto-mode` 操作手冊進行路由，並在其上疊加我的閘門。 |
| [`/dyl-review`](./skills/dyl-review/SKILL.md) | 當您需要最多 7 條可貼上即用的審查意見，以及一個 🟢/🟡/🔴 判定時。輸入 "deep" 可加入 thermos 和 Bugbot。絕不發布。 |
| [`/dyl-ready-pr`](./skills/dyl-ready-pr/SKILL.md) | "讓 PR 變綠。" 進行深度審查直到 🟢，標記為 ready，解決衝突，並監控 CI 直到達到可合併狀態。絕不合併。 |
| [`/build-figma`](./skills/build-figma/SKILL.md) | 當您有一個包含 `node-id` 的 `figma.com/design` URL 時。先進行接收，對映到您儲存庫的設計系統，然後針對即時 UI 進行視覺判斷。也會從 Figma URL 自動觸發。 |
| [`principle-the-algorithm`](./skills/principle-the-algorithm/SKILL.md) | 由 `dyl-mode` 引用。質疑需求，刪除，然後最佳化、加速、自動化。 |

<a id="what-dyl-mode-adds-over-poteto-mode"></a>

## dyl-mode 相較於 poteto-mode 的額外功能

- **根本原因，而非症狀。** 任何失敗在修復之前都會建立一個 `Root cause: X because Y` todo。空值保護、重試、`.skip` 以及快照更新，在該行正當化它們之前，都只是症狀修復。
- **The Algorithm** 在設計任何大於一瞥大小的編輯之前執行。
- **簡潔回覆。** 預設語氣是 pstack 的 `/bro`。
- **合併閘門。** 在當前回閤中未經許可絕不合併。更新現有的 PR，絕不開啟重複的 PR。
- **即時 UI 證明** 透過 `control-ui`，並在編寫佈局錯誤的程式碼之前進行測量。
- **品味否決權具有約束力。** "Roll that back" 意味著回滾它。

## Subagent

[`dyl-agent`](./agents/dyl-agent.md) 端到端執行此風格。使用 `subagent_type: "dyl-agent"` 啟動它。

<a id="not-shipped-here"></a>

## 此處未發布

- 原則、操作手冊、`/bro`、`/unslop` 以及 babysit watcher 包含在 `pstack` 中。
- `deslop`、`control-ui`、`control-cli` 和 `verify-this` 包含在 `cursor-team-kit` 中。
- thermos 審查 subagents 包含在 `thermos` 中。
- `/review-bugbot` 和 `/create-skill` 是 Cursor 內建功能。

<a id="license"></a>

## 授權條款

MIT
