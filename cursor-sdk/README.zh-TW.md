# Cursor SDK

Cursor 外掛，包含一個 skill，協助使用者在 Cursor TypeScript SDK (`@cursor/sdk`) 之上進行開發。該 skill 涵蓋三種呼叫模式 (`Agent.prompt`、`Agent.create` + `agent.send`、`Agent.resume`)、新整合的常見陷阱、執行階段選擇 (本機 vs 雲端)、驗證、串流、MCP、錯誤處理，以及適用於 CI、排程工作、聊天和 webhook 的可擴充套件模式。

該 skill 刻意保持簡短，僅在使用者的任務明確屬於其中一個參考檔案時，才指向該參考檔案。

<a id="what-it-includes"></a>

## 包含內容

- `cursor-sdk`：使用 `@cursor/sdk` 進行開發的設計與整合指南，以及關於執行階段選擇、驗證、錯誤處理、串流、MCP、進階功能和整合模式的參考檔案。

<a id="when-to-use-it"></a>

## 使用時機

當使用者正在整合、安裝或撰寫針對 Cursor SDK 的程式碼時使用；提及 `Agent.create`、`Agent.prompt`、`Agent.resume`、`agent.send`、`run.stream`、`CursorAgentError` 或 `@cursor/sdk`；希望從指令碼、CI/CD 管線、GitHub Action、後端服務、機器人或 webhook 執行 Cursor agent；正在本機與雲端執行階段之間做選擇；正在為 SDK agent 設定 MCP 伺服器；或正在將 REST `/v1/agents` 呼叫移植到 SDK。

該 skill 是外部 `@cursor/sdk` 套件的唯一事實來源，應主動載入，而非依賴記憶回答。

<a id="reference-files"></a>

## 參考檔案

該 skill 保持主要的 `SKILL.md` 簡短，僅在使用者的任務明確屬於其中一個參考檔案時才讀取該檔案：

| 如果使用者正在...                                                                    | 參考檔案                              |
| ------------------------------------------------------------------------------------ | -------------------------------------- |
| 在本機與雲端執行階段之間做選擇                                              | `references/runtime-choice.md`         |
| 偵錯驗證問題 (401 錯誤、缺少金鑰、團隊 vs 使用者金鑰)                                | `references/auth.md`                   |
| 處理錯誤、重試、速率限制、`CursorAgentError`、`result.status === error` | `references/error-handling.md`         |
| 消費串流、選擇事件型別、取消操作，或串流 vs 等待                | `references/streaming.md`              |
| 設定 MCP 伺服器 (HTTP、stdio、傳輸、驗證注入)                     | `references/mcp.md`                    |
| 子 agent、恢復、產出物、列出/檢視 agent、`Agent.messages`           | `references/advanced.md`               |
| 建置特定整合 (CI 審查機器人、分級、聊天、webhook)               | `references/patterns.md`               |

<a id="license"></a>

## 授權條款

MIT
