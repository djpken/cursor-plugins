<a id="agent-compatibility"></a>

# Agent 相容性

Cursor 外掛程式，用於檢查儲存庫在 agent 工作流程下的表現。它將已發布的 `agent-compatibility` CLI 與針對啟動、驗證及檔案可靠性的專注審查相結合。

預設情況下，完整檢查會回傳一個總體分數，以及一份列出最高效益修正專案的簡短清單。如果使用者需要完整的細項分析，agent 可以顯示各組成分數及其背後的推理過程。

<a id="what-it-includes"></a>

## 包含內容

- `check-agent-compatibility`：完整相容性檢查
- `compatibility-scan-review`：原始 CLI 支援的掃描
- `startup-review`：冷啟動與初始化審查
- `validation-review`：小型變更驗證審查
- `docs-reliability-review`：檔案可靠性審查

<a id="score-model"></a>

## 分數模型

- `Agent Compatibility Score`：顯示給使用者的最終混合分數
- `Deterministic Compatibility Score`：來自已發布 CLI 的原始分數
- `Startup Compatibility Score`：啟動儲存庫所需的猜測程度
- `Validation Loop Score`：驗證小型變更的實用性
- `Docs Reliability Score`：檔案與實際設定路徑的吻合程度

最終分數將確定性掃描與工作流程檢查進行混合：

```text
Agent Compatibility Score = round((deterministic * 0.7) + (workflow * 0.3))
```

CLI 也會報告已提交的 agent 工具加速器層。這些額外資訊會影響建議內容，但不會膨脹確定性相容性分數本身。

<a id="how-to-use-it"></a>

## 使用方式

當您需要完整檢查時，請使用 `check-agent-compatibility`。該 skill 會分發至上述四個審查 agent，然後回傳精簡的結果：

```md
## Agent Compatibility Score: 72/100

Top fixes
- First issue
- Second issue
```

如果您需要組成分數或權重，請要求提供細項分析。

<a id="cli-notes"></a>

## CLI 說明

此外掛程式不包含掃描器。它會在需要時執行已發布的 npm 套件。

預設掃描（精簡終端機儀錶板）：

```bash
npx -y agent-compatibility@latest .
```

JSON 輸出：

```bash
npx -y agent-compatibility@latest --json .
```

Markdown 輸出：

```bash
npx -y agent-compatibility@latest --md .
```

純文字輸出：

```bash
npx -y agent-compatibility@latest --text .
```

用於忽略路徑或權重覆蓋的設定覆寫：

```bash
npx -y agent-compatibility@latest . --config ./agent-compatibility.config.json
```

掃描器是啟發式的。它對儲存庫訊號進行評分並顯示可能的摩擦點，但並非對程式碼庫的完整品質判定。

<a id="local-install"></a>

## 本機安裝

如果您想直接使用此外掛程式，請將此資料夾建立符號連結至：

```bash
~/.cursor/plugins/local/agent-compatibility
```
