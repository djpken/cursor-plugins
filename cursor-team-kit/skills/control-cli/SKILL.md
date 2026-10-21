---
name: control-cli
description: "建置或調整本地測試框架，以驅動、檢查並分析互動式 CLI 或 TUI，且不需依賴外部服務。適用於 CLI 使用者體驗檢查、啟動回歸測試、記憶體洩漏、掛起、提示流程或終端機示範。"
---

<a id="control-cli"></a>

# 控制 CLI

使用可重複執行的本地測試框架來操作互動式 CLI，而非手動進行測試。若儲存庫中已有測試或示範框架，請優先重用；否則，請使用標準本地工具組裝一個暫時的測試框架。

<a id="what-it-is-used-for"></a>

## 用途

- 使用確定性輸入重現 CLI 或 TUI 的錯誤。
- 驗證鍵盤流程、提示、中斷、調整大小行為以及終端機版面設定。
- 為錯誤修復擷取修復前後的記錄。
- 分析啟動時間、緩慢操作、掛起或記憶體增長。
- 當輸出比說明更容易展示時，錄製一段簡短的終端機示範。

<a id="harness-loop"></a>

## 測試框架迴圈

1. 識別待測試的命令以及最小的可重現工作區。
2. 發現現有的本地測試框架：套件指令碼、端到端測試、示範錄製器、expect 指令碼或 PTY 輔助工具。
3. 若不存在測試框架，請在隔離的終端機會話中啟動 CLI，並使用確定性的環境變數。
4. 在進行互動之前，擷取目前的螢幕畫面。
5. 一次傳送一個動作：文字、Enter、方向鍵、Escape、Ctrl-C、調整大小。
6. 在執行下一個動作之前，等待出現具體的螢幕模式或提示。
7. 儲存記錄以及任何分析產出物。
8. 乾淨地終止會話。

<a id="harness-options"></a>

## 測試框架選項

- 儲存庫原生測試框架：優先使用已簽入的指令碼，因為它們瞭解應用程式的啟動、環境和提示。
- `tmux`：管理會話、`capture-pane`、`send-keys`、附加/分離。
- PTY 偵測：當 tmux 不可用時，使用簡短的 Python、Node 或 Expect 指令碼。
- 執行時檢查器：使用 Node 或 Bun 檢查器進行 CPU 分析、堆積快照和即時評估。
- 終端機錄製器：當使用者要求示範時，使用儲存庫本地的示範工具或相容於 asciinema 的工具。

<a id="minimal-tmux-harness"></a>

## 最小 tmux 測試框架

```bash
SESSION="cli-harness-$(date +%s)"
tmux new-session -d -s "$SESSION" -- <command-under-test>
tmux capture-pane -pt "$SESSION"
tmux send-keys -t "$SESSION" "help" Enter
tmux capture-pane -pt "$SESSION"
tmux kill-session -t "$SESSION"
```

對於 Node CLI：

```bash
NODE_OPTIONS="--inspect=127.0.0.1:0" tmux new-session -d -s "$SESSION" -- <node-cli-command>
```

讀取終端機輸出以找到檢查器 URL，若需要分析，請使用相容於 Chrome DevTools 的工具。

<a id="minimal-pty-harness"></a>

## 最小 PTY 測試框架

當需要在沒有 tmux 或示範測試框架的儲存庫中進行確定性等待時，請使用 PTY 指令碼。除非使用者要求新增可重用的測試，否則請保持其為暫時性的。

```python
import os
import pty
import select
import subprocess
import time

master_fd, slave_fd = pty.openpty()
proc = subprocess.Popen(
    ["<command>", "<arg>"],
    stdin=slave_fd,
    stdout=slave_fd,
    stderr=slave_fd,
    close_fds=True,
)
os.close(slave_fd)

deadline = time.time() + 30
buffer = b""
while time.time() < deadline:
    ready, _, _ = select.select([master_fd], [], [], 0.25)
    if not ready:
        continue
    chunk = os.read(master_fd, 4096)
    buffer += chunk
    if b"<ready text>" in buffer:
        os.write(master_fd, b"help\n")
        break

print(buffer.decode(errors="replace"))
proc.terminate()
os.close(master_fd)
```

若 CLI 需要更豐富的終端機控制，請使用 `pty.fork()` 或現有的 PTY 函式庫。

<a id="profiling-recipes"></a>

## 分析配方

- 啟動回歸：在相同的機器、環境和命令下，擷取基準和處理後的啟動時間。
- 緩慢操作：啟動 CPU 分析，執行該操作，停止分析，並比較頂層自我時間函式。
- 記憶體洩漏：若可用，強制執行 GC，取得堆積快照，重複執行該操作，再次強制執行 GC，並取得另一個快照。
- 掛起：在中斷之前，擷取螢幕、活動的處理常式/資源以及堆疊/CPU 樣本。

<a id="guardrails"></a>

## 防護措施

- 優先使用確定性等待而非睡眠。若必須使用睡眠，請說明原因。
- 不要將憑證或破壞性命令傳送至受控會話中。
- 除非儲存庫已有測試或示範測試框架，否則請將測試框架保留在 `/tmp` 中。
- 不要硬編碼來自其他儲存庫的路徑。請根據目前儲存庫的指令碼和執行時環境調整命令。
- 除非使用者要求保留，否則請清理 tmux 會話、暫存資料夾、檢查器程式以及示範產出物。
