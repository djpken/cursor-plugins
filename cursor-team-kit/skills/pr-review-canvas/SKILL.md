---
name: pr-review-canvas
disable-model-invocation: true
description: "產生一個互動式 PR 審查導覽 HTML 頁面。透過 gh API 擷取 PR 資料，將檔案分類為核心變更與機械式變更，加入審查者註解，並呈現包含移動程式碼偵測的 diff。當使用者貼上 GitHub PR URL 並要求審查、導覽或摘要，或說「審查這個 PR」時使用。"
---

<a id="pr-review-canvas"></a>

# PR 審查畫布

產生一個互動式 HTML 審查頁面，內容如同同事在向你說明 GitHub PR 中重要的部分。

<a id="workflow"></a>

## 工作流程

<a id="1-fetch-pr-data"></a>

### 1. 擷取 PR 資料

並行執行這些 `gh api` 呼叫：

```bash
gh api repos/{owner}/{repo}/pulls/{number} --jq '{title, body, user: .user.login, state, additions, deletions, changed_files, base: .base.ref, head: .head.ref}'
gh api repos/{owner}/{repo}/pulls/{number}/files --paginate --jq '.[] | {filename, status, additions, deletions, patch}'
gh api repos/{owner}/{repo}/pulls/{number}/comments --jq '.[] | {user: .user.login, body, path, line}'
```

<a id="2-analyze-the-pr-and-write-the-body-html"></a>

### 2. 分析 PR 並撰寫主體 HTML

閱讀 diff，理解 PR，並直接以 HTML 撰寫 `<body>` 內容。你擁有完全的創作自由，目標是向審查者清楚說明 PR。使用最適合該 PR 的結構。

**典型結構**（視需要調整）：
- 包含標題、PR 編號、作者與統計資料的頁首
- 用白話文解釋 PR 內容的摘要框
- 包含註解與 diff 的核心檔案區段
- 預設摺起的機械式或樣板檔案
- 底部的審查檢查清單

**你也可以加入：**
- **偽程式碼摘要** 用於冗長的程式碼，以白話文或簡短偽程式碼展示演演算法，並將實際 diff 摺起在下方（使用標記為「顯示完整實作」的 `.bp-section` 卡片）。當 150 行的重試、退避或錯誤處理程式碼其實只是「使用指數退避與斷路器的擷取」時，這非常實用。
- 圖表（內嵌 SVG、透過 CDN 的 mermaid、`<pre>` 中的 ASCII 藝術）
- 顯示變更前後控制流程的流程圖
- 比較舊版與新版行為的表格
- 用於警告、問題或注意事項的提示框
- 有助理解的互動元件
- 任何讓審查更清楚的其他內容

**偽程式碼範例：**
```html
<div class="file-card">
  <div class="file-hdr" onclick="toggle(this)">
    <span class="fname">retryClient.ts</span>
    <div class="fstats"><span class="pill add">+173</span><span class="pill del">&minus;11</span><span class="chev open">&#9654;</span></div>
  </div>
  <div class="file-body open">
    <div class="file-note">
      <strong>What this does in plain English:</strong>
      <pre style="margin-top:8px;color:var(--text);font-size:12px;line-height:1.6;">
fetch(url):
  if circuit breaker is open → fail fast
  retry up to N times:
    try fetch with timeout
    on success → close circuit breaker, return
    on retryable error → wait (exponential backoff + jitter)
    on non-retryable error → throw
  circuit breaker records failure</pre>
    </div>
    <div class="bp-section" style="margin:0;border:0;border-radius:0;">
      <div class="bp-hdr" onclick="toggleBP(this)">
        <span>Show full implementation (+173 lines)</span><span class="chev">&#9654;</span>
      </div>
      <div class="bp-body"><div data-diff="retryClient"></div></div>
    </div>
  </div>
</div>
```

<a id="3-available-css-classes-and-js-utilities"></a>

### 3. 可用的 CSS 類別與 JS 工具

從這個 skill 目錄讀取 [styles.css](styles.css) 和 [renderer.js](renderer.js)。這些提供預先建置的深色主題工具包。將它們原封不動地注入 [template.html](template.html)。

**可用的 CSS 類別：**

| 類別 | 用途 |
|-------|---------|
| `.header`, `.header h1`, `.header-meta` | 頁面頁首 |
| `.pill.add`, `.pill.del`, `.pill.files` | 統計徽章（+N, -N, N 個檔案） |
| `.content` | 置中內容包裝器（最大 900px） |
| `.summary` | 摘要或 TL;DR 框 |
| `.section-title` | 帶有底邊框的區段標題 |
| `.ic` | 行內程式碼參考（等寬字型、藍色、深色背景） |
| `.file-card`, `.file-hdr`, `.file-body` | 可摺起的檔案卡片（在 `.file-hdr` 上使用 `onclick="toggle(this)"`） |
| `.file-note` | 檔案卡片內的固定審查者註解 |
| `.bp-section`, `.bp-hdr`, `.bp-body` | 摺起的樣板卡片（使用 `onclick="toggleBP(this)"`） |
| `.bp-note` | 樣板卡片內的註記 |
| `.verdict` | 審查檢查清單框 |

**可用的 JS 函式：**

| 函式 | 用法 |
|----------|-------|
| `toggle(hdrElement)` | 切換 `.file-body` 的開啟或關閉狀態 |
| `toggleBP(hdrElement)` | 切換 `.bp-body` 的開啟或關閉狀態 |
| `renderDiff(target, diffInput)` | 呈現統一 diff。`target` 可以是 DOM 元素、字串 ID 或 CSS 選擇器。`diffInput` 可以是原始 patch 字串或行陣列，兩者皆可。自動過濾 import、摺起僅空白變更，並偵測移動的程式碼（藍色或紫色色調）。 |
| `esc(string)` | HTML 轉義字串 |

**呈現 diff，使用 `data-diff` 屬性進行自動偵測。**
在你想呈現 diff 的主體 HTML 位置放置 `<div data-diff="KEY"></div>` 佔位符。渲染器會在 DOM 載入後自動找到它們，並從 `template.html` 中的 `<script id="pr-diffs-json" type="application/json">` 元素填入內容。

**重要：Patch 字串除了換行、反斜線和引號外，還可能包含 `</script>`。** 如果你將原始輸出貼入可執行的 `<script>`，即使有 `json.dumps(...)` 也不夠，因為 HTML 解析可能提前終止標籤。切勿手動將 patch 字串嵌入 JS 或 JSON。請改用以下安全方法：

1. 在擷取步驟中，使用 `jq` 將 patch 儲存到 JSON 檔案（它能正確處理轉義）：
```bash
gh api repos/{owner}/{repo}/pulls/{number}/files --paginate \
  --jq '[.[] | {key: (.filename | gsub("[^a-zA-Z0-9]"; "_")), value: (.patch // "")}] | from_entries' \
  > /tmp/pr-patches-{number}.json
```

2. 在組裝期間，使用 Python 安全地將 JSON 注入 `template.html`：
```bash
python3 <<'PY'
import json
from pathlib import Path

patches = json.loads(Path('/tmp/pr-patches-{number}.json').read_text())
html = Path('/tmp/pr-review-{number}-body.html').read_text()
css = Path('styles.css').read_text()
js = Path('renderer.js').read_text()
tmpl = Path('template.html').read_text()

# Prevent literal </script> from terminating HTML script tags early.
safe_json = json.dumps(patches).replace('<', '\\u003c').replace('>', '\\u003e').replace('&', '\\u0026')

out = (
  tmpl.replace('/* INJECT_CSS */', css)
      .replace('/* INJECT_JS */', js)
      .replace('<!-- INJECT_BODY -->', html)
      .replace('{"__PR_DIFFS_PLACEHOLDER__":true}', safe_json)
)

Path('/tmp/pr-review-{number}.html').write_text(out)
PY
```

這可確保有效的 JSON 與指令碼安全的 HTML 嵌入。Agent 將主體 HTML 寫入暫存檔案，然後 Python 安全地組裝所有內容。

Diff 資料的鍵應與 HTML 中的 `data-diff` 屬性值相符：
```html
<div data-diff="path_to_file_ts"></div>
```

由於 renderer.js 在 `<head>` 中載入，如有需要，你也可以從行內 `<script>` 標籤直接呼叫 `renderDiff(target, lines)` 以處理自訂用途。該函式接受 DOM 元素、ID 字串或 CSS 選擇器作為 `target`，並接受字串或陣列作為 `lines`。

**你不受這些限制。** 加入你自己的行內 `<style>` 區塊、`<script>` 區塊、SVG、圖表或其他任何內容。預先建置的元件可節省時間，但不會限制你。

<a id="4-assemble-and-serve"></a>

### 4. 組裝並提供服務

1. 將你的主體 HTML（放入 `<body>` 內的所有內容）寫入 `/tmp/pr-review-{number}-body.html`
2. 使用上述步驟 3 中的 `jq` 命令將 patch 儲存到 `/tmp/pr-patches-{number}.json`
3. 執行上述步驟 3 中的 Python 組裝指令碼（從這個 skill 目錄讀取 styles.css、renderer.js、template.html，安全地注入主體與 patch，並寫入最終 HTML）
4. 在固定埠啟動本地伺服器：
   ```bash
   cd /tmp && python3 -m http.server 8432 --bind 127.0.0.1
   ```
   在背景執行此命令，然後在應用程式內瀏覽器中導航至 `http://127.0.0.1:8432/pr-review-{number}.html`。

   **為什麼使用固定埠和 `cd /tmp`：** 背景 shell 沒有 TTY，因此 Python 會無限期緩衝其啟動訊息（「Serving HTTP on...」），使用埠 0 意味著你永遠無法讀取選擇了哪個埠。`--directory /tmp` 可行，但 `cd /tmp` 在不同 Python 版本中更穩健。如果埠 8432 被佔用，請嘗試 8433、8434 等。

<a id="diff-features-handled-automatically-by-rendererjs"></a>

### Diff 功能（由 renderer.js 自動處理）

- 過濾掉僅包含 import 的行
- 將僅空白變更摺起為上下文行
- 偵測移動的程式碼區塊（在一個位置刪除 3 行以上連續行，並在另一處以相同方式新增），以藍色或紫色呈現，而非紅色或綠色
- 近似匹配（移動加小幅編輯）會使用不同的紫色色調

<a id="style-notes"></a>

### 樣式說明

- 深色主題：`#1a1a1a` 背景、Inter 正文字型、程式碼使用 IBM Plex Mono
- 使用 `var(--warning)` 代表橙色、`var(--success)` 代表綠色、`var(--danger)` 代表紅色、`var(--accent)` 代表藍色
- 固定檔案頁首（`position: sticky; top: 0`）和註記（`top: 35px`）在捲動時保持固定
- 核心檔案預設展開（`.file-body.open`），機械式檔案預設摺起
