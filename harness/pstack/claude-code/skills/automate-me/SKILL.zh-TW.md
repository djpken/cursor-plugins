---
name: automate-me
description: "適用於「幫我自動化」、「建立/更新/重新整理我的 -mode skill」、「將我的偏好或工作風格轉化為 skill」，或希望 agent 遵循使用者工作方式的場景。透過 create-skill 加上 unslop 起草或修訂個人 -mode skill，並可選地從近期對話記錄中擷取最新證據。"
disable-model-invocation: true
---

<a id="automate-me"></a>

# 幫我自動化

一個引導式流程，將使用者的工作慣例轉化為 agent 會遵循的 skill。輸出為一個量身打造的 `-mode` skill（例如 `jay-mode`、`priya-mode`）。

此 skill 協調另外三個 skill：內嵌的挖掘流程（見步驟 1）、Cursor 內建的 `create-skill`（撰寫），以及 **unslop** skill（散文紀律）。它負責排序這些步驟，但並不會取代它們。

<a id="flow"></a>

## 流程

<a id="0-check-for-an-existing-skill"></a>

### 0. 檢查是否有現存的 skill

遞迴搜尋符合使用者 handle 的 `.cursor/skills/**/*-mode/SKILL.md` 和 `~/.cursor/skills/*-mode/SKILL.md`。Mode skill 可以放在個人類別目錄（`.cursor/skills/<handle>/`）中，不一定只在頂層。如果已存在，請使用 `AskQuestion` 確認意圖（除非使用者已表示「更新我的 skill」或類似語句）：

- 更新現有的 skill（重複執行時的預設選項）
- 重新開始（罕見，執行前先詢問原因）

更新模式會改變後續流程：
- 步驟 1 僅挖掘自該 skill 上次編輯以來的歷史記錄（`git log -1 --format=%cI <path>`）。
- 步驟 2 詢問哪些部分已變更或缺失，而非從零開始詢問要捕捉什麼。
- 步驟 4 就地編輯現有檔案。保留使用者未反駁的章節。修訂有新證據的章節。僅針對真正的新規則新增章節。

<a id="1-mine-their-history"></a>

### 1. 挖掘其歷史記錄

在展開作業前，先定位活躍工作區的對話記錄。系統提示詞會指明該工作區的 `agent-transcripts/` 目錄。僅使用該路徑。不要跨 `~/.cursor/projects/*/` 進行 glob 搜尋。這樣會跨越工作區邊界，並讀取來自無關專案的私人對話。

在該範圍內調查近期的 agent 對話，尋找重複出現的模式。針對歷史記錄的不同切片（例如最近 2 至 4 週，分成 3 個切片以確保每個切片有足夠素材）執行多個並行 subagent。根據 **poteto-mode** skill 的 Subagents 章節，為每個 subagent 選擇合適的推理層級以及快速或標準速度。每個切片挖掘 subagent 會讀取父級提供的工作區範圍路徑中的對話記錄，尋找以下訊號，並回傳一份簡短的結構化模式清單，附上證據指標。預設值得尋找的訊號：

- 回應偏好（長度、語氣、格式、「講簡單點」的修正）
- 委派習慣（subagent、模型、專業化工作流程、並行性）
- 驗證態度（「完成」的定義、單元測試與實際重現、審查者）
- 程式碼與散文紀律（風格、引用的原則、lint/format 工具）
- 流程慣例（worktree、commit、PR、審查/合併工具）
- 元偏好（任務中修正 skill、提議新 skill）

在提升訊號前，先跨切片進行交叉檢查。在 2 個以上切片中看到的模式具有高置信度。孤立的訊號較弱，通常會被捨棄。

<a id="2-ask-the-user-directly"></a>

### 2. 直接詢問使用者

挖掘會遺漏尚未出現的意圖。使用 `AskQuestion` 工具（結構化多選），而不是要求使用者從零開始輸入。

形式：一到兩個問題，每個問題有 4 至 6 個選項，類別問題使用 `allow_multiple: true`。先從廣泛的問題開始（「哪些領域最重要？」），然後針對選定的領域提出具體選項進行追問。在結構化輪次之後，提出一個自由形式的聊天問題，以捕捉選項遺漏的內容。

不要一次丟出 20 個問題。

<a id="3-cluster-findings"></a>

### 3. 對發現進行分群

將合併後的訊號分組為章節。常見章節（僅使用適用者）：

- **回應風格**：長度、語氣、格式。
- **自主性**：在不詢問的情況下執行多少工作、MCP 工具使用。
- **先理解**：在界定範圍或調查變更時，應使用哪些 skill。
- **Subagent**：預設值、並行性、模型與任務對應、專業化工作流程。
- **散文 / 程式碼紀律**：原則、lint 工具、風格指南。
- **審查與驗證**：重現態度、驗證 skill、實測工具。
- **流程**：git worktree、commit、PR、審查/合併工具。
- **Skill**：skill 撰寫習慣、優先修正 skill、提議新 skill。

**poteto-mode** skill 展示了結構。閱讀它以瞭解粒度。不要複製其內容。使用者的規則與 poteto-mode 的規則不同。

<a id="4-draft-the-skill"></a>

### 4. 起草 skill

使用 Cursor 內建的 `create-skill` skill 來撰寫此 skill。放置位置：

- 路徑：保留現有 mode skill 的類別。對於新的 mode，如果儲存庫中已為該 handle 建立個人類別，則使用 `.cursor/skills/<handle>/<handle>-mode/SKILL.md`。否則預設使用專案中的 `.cursor/skills/<handle>-mode/SKILL.md`（如果使用者偏好個人 skill，則使用 `~/.cursor/skills/<handle>-mode/`）。
- Handle：使用者的名字或選擇的識別碼。
- Frontmatter `description`：觸發條件為其名字加上 `/<handle>-mode` 加上「以其風格工作」，而非「寫程式碼」或「審查 PR」等通用關鍵字。
- Frontmatter 格式：遵循 `create-skill` 的 YAML 規則。將 `description` 保持為單一 YAML 標量。當標點符號或換行需要時，加上引號或使用 `description: >-` 並搭配縮排的續行。
- 預設使用 Frontmatter `disable-model-invocation: true`。僅當使用者明確希望其 mode 在每一輪都適用時，才取消此設定。

<a id="5-iterate-on-prose"></a>

### 5. 迭代散文

對每一行應用 **unslop** skill 和 `create-skill` 的寫作指南。

向使用者展示草稿並接受回饋。預期會有多次迭代。毫不留情地刪減。Mode skill 不是手冊。

<a id="6-land-it"></a>

### 6. 落地

在 main 分支的 worktree 中工作。Commit 並開啟 PR。不要直接 push 到 main。

<a id="guardrails"></a>

## 防護措施

- **不要過擬合單一對話。** 一次提出但另一次被反駁的偏好是雜訊。在將其編碼化之前，需要多個例項。
- **不要耍聰明。** 重述其他 skill 的內容、發明隱喻，或為 agent 讀者撰寫「詩意」的散文，都是沒有益處的開銷。保持操作性。
- **引用，不要內嵌。** 使用者依賴的其他 skill 應以路徑引用的形式出現，而不是貼上的摘錄。他們在其他地方維護的任何原則檔案也是如此。
- **保持章節最小化。** 僅當使用者在該處有具體且非預設的規則時，才新增章節。「清晰溝通」不是一個章節。「短段落。比較選項時使用表格。僅當專案真正平行時才使用專案符號。」才是。
- **命名慣例保持通用。** 在祈使句中，使用「使用者」或「人類」，而不是作者的名字。
- **不要強求對稱。** 如果使用者沒有值得寫下的流程規則，完全跳過 Process 章節。

<a id="evaluation"></a>

## 評估

`-mode` skill 是主觀輸出。`create-skill` 風格的測試/迭代基準迴圈在此處沒有用處。與使用者進行氛圍檢查：讀起來像他們嗎？有遺漏什麼嗎？然後發布。

僅當該 skill 的觸發準確性在實際使用中確實成為問題時，才執行描述最佳化迴圈。

<a id="when-not-to-use"></a>

## 何時不適用

- 使用者需要特定任務的 skill（而非工作慣例）：僅需 `create-skill`，無需挖掘。
- 使用者希望記錄一個狹窄的工作流程（例如「我如何撰寫 commit 訊息」）。這是一般 skill，而非 mode skill。
