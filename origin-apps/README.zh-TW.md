# Origin Apps

兩個用於在 [Cursor Origin](https://cursor.com/docs/api/origin) 上建構的 skill
Cursor 的程式碼鍛造場。它們涵蓋建立 Origin App、呼叫 API、
接收 webhook，以及將現有的 GitHub App 遷移過來。此 plugin 僅包含
skill，因此可在 Cursor、Claude Code、Codex 以及任何能
讀取 [Agent Skills](https://agentskills.io) 的 agent 中執行。

<a id="what-it-includes"></a>

## 包含內容

`origin-api` 會引導 agent 前往 Origin 檔案中回答其問題的章節，並列出應優先檢查的規則：原生與映象
儲存庫、事件訂閱、webhook 驗證、來自
規格 (spec) 的許可權範圍 (scopes)、不透明 token 與 ID。適用於任何 Origin 相關工作。
spec、不透明 token 和 ID。請將其用於任何 Origin 工作。

`port-github-app-to-origin` 規劃現有 GitHub App 的遷移。執行它
將其對應到現行的 Origin 規格，並撰寫一份遷移簡報，說明哪些內容
可以沿用、哪些不能，您的處理器 (handlers) 讀取的 webhook 欄位及其
在 Origin 上的來源、需要請求的許可權範圍、針對您 App 的端到端測試、
給 Cursor 的回饋，以及您的團隊必須決定的問題。它只負責規劃。除非您要求，否則不會撰寫任何程式碼。
這兩個 skill 會在執行時讀取現行規格，絕不會憑記憶指名任何端點。
何時使用

詢問 Origin API，或要求遷移 GitHub App 時，對應的 skill

<a id="when-to-use"></a>

## 何時使用

在 Cursor 中安裝
載入。在 Cursor 中，您也可以執行 `/origin-api` 或
`/port-github-app-to-origin`.

<a id="install-in-cursor"></a>

## 在 Cursor 中安裝

或開啟 Customize，找到該 plugin，並在使用者或專案範圍內安裝。
([cursor.com/marketplace/origin-apps](https://cursor.com/marketplace/origin-apps))，
或開啟 Customize，找到該 plugin，並在使用者或專案範圍中安裝它。

<a id="use-outside-cursor"></a>

## 在 Cursor 之外使用

執行 ](https://agentskills.io)。其他任何 agent：將
`/plugin marketplace add cursor/plugins` 複製到其 skill 資料夾（兩者都要複製；遷移 skill
會參考 `/plugin install origin-apps@cursor-plugins`）。
`origin-apps/skills/*` 到其 skills 資料夾（複製兩者；porting skill
執行期間需能存取 `origin-api` 的網路。

<a id="requirements"></a>

## 對於遷移 skill，需要對 App 原始碼的讀取許可權。撰寫簡報

- 執行期間對 `https://cursor.com/docs/api/origin/*` 的網路存取。
- 簡報存放位置
  不需要 Origin 憑證。您執行 brief 的端到端測試
  列印其路徑。如果有給 Cursor 的回饋，它也會將該

<a id="where-the-brief-goes"></a>

## brief 的存放位置

porting skill 會在儲存庫根目錄寫入 `ORIGIN-PORTING-BRIEF.md` 並
授權條款
section 到 `ORIGIN-FEEDBACK.md`，可直接傳送；brief 的其餘部分
是給您的團隊的。

<a id="license"></a>

## 授權條款

MIT
