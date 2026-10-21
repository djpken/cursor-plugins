---
name: why
description: "用於回答「為什麼 X 是這樣運作」「為什麼我們選擇了 Y」、設計理由、迴歸分析、事後檢討，或資料支撐的閾值。它會發現可用的 MCP，並並行查詢每個證據類別（原始碼控制、問題追蹤器、長篇文件、即時聊天、基礎設施觀測、錯誤追蹤、產品分析儲存庫），然後返回帶有引用來源的決策與權衡分析。使用 how 來詢問執行時行為。"
disable-model-invocation: true
---

<a id="why"></a>

# 為什麼

調查程式碼背後的動機與意圖。配套的 `how` 技能回答程式碼做了什麼以及如何運作。`why` 回答哪些力量導致了它的形態。

下面每個 spawn 都指定了 `pstack-models.mdc` 規則中的一條角色行和一個預設值。將 `model` 設為該行的值，若規則或該行缺失則設為預設值。當值為 `model` 或 `auto` 時，將 `inherit-parent` 留空。如果 Task 工具拒絕某個 slug，使用預設值並說明此事。如果它拒絕預設值，則從其錯誤訊息中選擇同一家族中最接近的有效 slug。根據 **poteto-mode** 技能的 Subagents 章節，為每個子代理選擇適當的推理層級以及快速或標準速度。

每個 spawn 都是 `generalPurpose`，使用 `readonly: false`（代理模式）。**不要使用 readonly/Ask 模式。** 它會移除 MCP 訪問，從而禁用 MCP 支援的調查者以及合成器的引用抽查。調查者仍然不應寫入任何內容。

<a id="operating-posture"></a>

## 運作姿態

以**謹慎、小心且精確的調查者**姿態運作。誠實區分你所知道與你所推斷的內容。閱讀 `references/epistemics.md` 以瞭解完整的信心框架與措辭指南。合成器必須遵循它。

<a id="step-1-understand-the-target-and-the-question"></a>

## 步驟 1. 理解目標與問題

**目標**通常是一段程式碼、一個模式、一個功能或一個具名的設計決策。**問題**通常是設計理由、權衡、動機性的邊界案例、外部約束、死程式碼，或廣泛的歷史掃描。

如果目標模糊（「為什麼我們這樣做？」但沒有明確的指涉物件），根據對話上下文（開啟的檔案、最近的編輯、游標位置、剛剛討論的內容）做出最佳猜測。簡要陳述你的解讀，以便使用者可以在你偏離時重新引導，然後繼續。

<a id="step-2-establish-the-code-anchor"></a>

## 步驟 2. 建立程式碼錨點

在 spawn 調查者之前，先建立種子上下文：檔案路徑與行範圍、關鍵符號（函式、類別、常量）、觸及目標的最新幾個 commit、來自 merge commit 的 PR 編號（主題行中的 `(#1234)` 模式），以及任何關聯的 ticket ID。執行 `git blame -L <start>,<end> <file>` 獲取最近觸及的 commit，`git log --follow -p -- <file>` 獲取包含重新命名路徑的完整歷史與 patch，`git log --oneline -20 -- <file>` 獲取顯示 PR 編號的最近 commit，`git log -1 --format=%B <commit>` 提取 PR 編號。然後為任何實質性 commit 提取 PR 正文與討論：`gh pr view <number> --json title,body,author,createdAt,mergedAt,labels,closingIssuesReferences,comments,reviews`。這個錨點會提供給每個調查者與合成器。

<a id="step-3-spawn-parallel-investigators"></a>

## 步驟 3. 並行 spawn 調查者

**預設採用完整的並行調查。** 目標是完整的覆蓋地圖，而非最小地圖。記錄空結果，不要跳過搜尋。

<a id="discovery"></a>

### 發現

在 spawn 調查者之前，從 Cursor 環境列出可用的 MCP。若有可用工具地圖則使用它。否則檢查 Cursor 暴露的 `mcps/` 目錄以檢視已啟用的 MCP 伺服器。

將每個可用 MCP 對映到一個證據類別：

1. 原始碼控制歷史
2. 問題 / ticket 追蹤器
3. 長篇文件
4. 即時團隊聊天
5. 基礎設施觀測
6. 錯誤 / 例外追蹤
7. 產品分析儲存庫

原始碼控制始終可透過 git 與 `gh` 使用。對於其他六類，使用 MCP 名稱、伺服器指令、工具名稱與資源描述進行分類。如果某個 MCP 可能符合多個類別，選擇與其主要證據匹配的類別。在覆蓋地圖中記錄模糊案例。

每個有匹配 MCP 的類別 spawn 一個調查者，全部在單一訊息中以便並行執行，每個調查者只擁有一個工具或 MCP。不要讓一個代理覆蓋多個 MCP。`model`：`why investigators` 行，預設 `grok-4.7-xhigh-fast`。

每個調查者的提示是填入的 `references/investigator-prompt.md`，再加上：
- 所選 MCP 的類別劇本 `references/sources/<source>.md`，根據 `references/source-playbook.md` 中的示例改編
- `references/sources/incident-postmortem.md` **如果目的碼看起來是防禦性的**（空值檢查、重試邏輯、時限處理、速率限制、功能旗標、出口防護、OOM 處理器）
- 程式碼錨點與使用者原始問題

<a id="investigator-roster-one-per-available-evidence-category"></a>

### 調查者名單，每個可用證據類別一個

以下每個類別都獨特地適合揭示一種「為什麼」的形態，顯示於「擅長」之後。用它來知道預期回報、在類別返回空時指出缺口，並且僅在罕見且可證明無關的情況下，用它來 justify 跳過。

1. **原始碼控制**（git 歷史、`gh` 用於 PR、程式碼註釋、測試）。始終 spawn，唯一保證的來源。擅長 *審查期間捕獲的實現時間理由*。
2. **問題 / ticket 追蹤器**（例如 Linear、Jira、GitHub Issues、Plane、Shortcut MCP）。擅長 *產品或業務驅動因素*。當「為什麼」來自工程外部時最強。
3. **長篇文件**（例如 Notion、Confluence、Google Docs、Coda MCP）。擅長 *長篇設計理由*，在成為程式碼之前就已寫出來。
4. **即時團隊聊天**（例如 Slack、Discord、Microsoft Teams、Mattermost MCP）。擅長 *從未到達文件的即時討論*。當原始碼控制、ticket 與文件的紙面記錄薄弱時尤其重要。
5. **基礎設施觀測**（例如 Datadog、New Relic、Honeycomb、Grafana、Splunk MCP）。擅長 *驅動程式碼的基礎設施與執行時現實*。當目標回應基礎設施訊號（時限、重試、速率限制、斷路器）時最強。
6. **錯誤 / 例外追蹤**（例如 Sentry、Rollbar、Bugsnag、Airbrake MCP）。擅長 *驅動防禦性或修正性程式碼的特定例外與錯誤軌跡*。對於 catch 塊、空值防護、型別檢查、重試與其他防禦最強。
7. **產品分析儲存庫**（例如 Databricks、Snowflake、BigQuery、ClickHouse、dbt、Redshift MCP）。擅長 *塑造程式碼的產品與資料現實*。對於旗標門控程式碼、實驗驅動的釋出、資料遷移與「這個數字從哪裡來」的問題最強。

<a id="when-to-skip-an-investigator"></a>

### 何時跳過調查者

只有在有**明確、書面理由**且放入最終 Sources Consulted 時才跳過。兩個有效理由：

- **此環境中沒有適用於該類別的 MCP**。將其標記為缺口，而非選擇：「已略過實時團隊聊天。沒有相符的 MCP 可用，因此對話記錄無法搜尋。」
- **來源明確不相關**，而非僅是「可能不相關」。高標準：「已略過錯誤／例外追蹤。目標是構建時間的指令碼，沒有執行時間的程式碼路徑。」

如果您的範圍評估顯示目標是單一提交的簡單目標，且 PR 描述已包含完整答案，您可以在確認所有七個可用類別搜尋都會是冗餘之後，才直接回答。請明確說明。這應該很少發生。

<a id="step-4-synthesize"></a>

## 步驟 4：綜合

啟動一個綜合器。`model`：`why synthesizer` 行，預設 `claude-opus-5-5-xhigh`。它會接收：
1. 調查員的發現，包括任何空結果以及已略過並附理由的類別
2. 程式碼錨點
3. 使用者原始問題
4. 來自 `references/epistemics.md` 的知識框架
5. 來自 `references/synthesizer-prompt.md` 的綜合器提示模板

<a id="step-5-present"></a>

## 步驟 5：呈現

將綜合器的輸出呈現給使用者。您可以輕微編輯以增加清晰度或從對話中補充上下文，但**不要重寫信心語言**。

<a id="output-format"></a>

## 輸出格式

結構是 `references/synthesizer-prompt.md` 中的結構：問題、相關程式碼、我們發現的內容、我們能合理推斷的內容、競爭假說、我們不知道的內容、參考來源、信心摘要。根據需要調整，但保持信心分離，並將參考來源保持為每個調查員一行，包括那些沒有結果或已略過的，附上理由。

在參考來源之後，如果使用者 `why` 的問題是修改此程式碼的前置步驟，將血統發現轉換為規劃變更的保留／變更／避免／風險限制集合。

<a id="common-failure-modes-to-avoid"></a>

## 常見失敗模式需避免

- **近期偏差**。假定最近的提交是權威的。當前形態通常是許多早期決策的積累。追溯回去。
