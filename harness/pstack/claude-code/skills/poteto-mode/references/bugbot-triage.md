<a id="bugbot-triage"></a>

# Bugbot 分診

當 Babysit 劇本 (`../playbooks/babysit.md`) 處理 Bugbot 或審查自動化評論時，請使用此參考。目標不是預設忽略 Bugbot。目標是停止將每個評論都視為必須的程式碼變更。

<a id="decision-rubric"></a>

## 決策規則

在行動之前，先分類每個 Bugbot 討論串：

- `fix`：評論指出一個合理的正確性、安全性、隱私、資料損失、認證、計費、遷移、冪等性、競態或已發布行為問題。在最低層級的擁有 PR 中修復，然後回覆提交 SHA 並解決討論串。
- `dismiss`：評論符合已記錄的低風險噪音模式，且當前程式碼/上下文證明該問題不需要程式碼變更。回覆簡短原因並解決討論串。
- `ask`：評論是新穎、高嚴重性、安全性/隱私/資料相關或模糊的。請詢問使用者，而不是猜測。

如有疑問，請詢問。跳過一個噪音程式碼品質評論是便宜的；跳過一個真實資料或安全性錯誤則不是。

<a id="learned-pattern-format"></a>

## 學習模式格式

以此格式新增未來模式：

```markdown
### <short pattern name>

- Confidence: candidate | recurring | strong
- Skip when: <conditions that must be true>
- Do not skip when: <risk boundaries>
- Example signal: <phrases or code context that identify the pattern>
- Source: <PR/comment URL or short historical note>
```

使用 `candidate` 作為一或兩個例子。使用 `recurring` 在多次實際駁回後。使用 `strong` 僅當模式是窄、反覆驗證且低風險。

<a id="recurring-skip-candidates"></a>

## 反覆跳過候選

<a id="intentional-ui-or-design-system-visual-changes"></a>

### 故意的 UI 或設計系統視覺變更

- 信心：候選
- 跳過條件：PR 描述、截圖、設計審查或附近程式碼明確說明視覺變更，且 Bugbot 評論僅重申共享視覺預設已變更。
- 不要跳過條件：評論指向無障礙、焦點可見性、鍵盤導航、色彩對比或元件 API 契約，且 PR 未故意變更這些。
- 例子訊號：關於焦點輪廓、按鈕大小、間距或共享元件視覺預設的評論，其中所有者回復「故意」或「有意」。

<a id="upstack-or-stack-local-usage-bugbot-cannot-see"></a>

### Bugbot 看不到的上層或堆疊本地使用

- 信心：候選
- 跳過條件：Bugbot 標記一個匯出、元件、輔助或檔案為未使用，且當前 forge 的 PR 列表和差異、上層堆疊差異或 PR 上下文顯示它被堆疊中的後續 PR 使用。
- 不要跳過條件：當前 PR 不是堆疊的一部分、該符號是公開 API，或所謂的上層使用無法驗證。
- 例子訊號：「匯出的元件從未使用」並有人類回覆「上層使用」。

<a id="temporary-duplication-during-parallel-implementation"></a>

### 平行實現期間的暫時複製

- 信心：候選
- 跳過條件：PR 故意複製少量程式碼，以保持新路徑與舊路徑平行，舊路徑將被刪除、替換或驗證。
- 不要跳過條件：複製的程式碼變更安全性、計費、資料訪問、API 行為，或長期共享抽象明顯降低風險。
- 例子訊號：「顯著複製」或「複製驗證邏輯」，其中所有者解釋舊路徑將被刪除或複製邏輯故意本地化。

<a id="existing-framework-or-component-invariant-covers-the-warning"></a>

### 現有框架或元件不變數涵蓋警告

- 信心：候選
- 跳過條件：該問題已由共享元件、框架契約、型別不變數或當前差異或附近程式碼中的單一真相來源保證。
- 不要跳過條件：不變數是假定但未強制、依賴時間，或跨越非同步/狀態邊界，值可能分歧。
- 例子訊號：關於內部 popover 缺少 max-height 的評論，當共享 popover 強制視口邊界，或可空值，當本地檢查值和傳遞值共享同一來源。

<a id="owner-declared-follow-up-or-deferred-cleanup"></a>

### 所有者宣告的後續或延遲清理

- 信心：候選
- 跳過條件：PR 所有者明確表示該問題是已知後續，當前 PR 不會使行為變糟，且評論不涉及高風險區域。
- 不要跳過條件：代理在無所有者輸入時行動、問題是中/高嚴重性產品行為，或延遲會合併新的回歸。
- 例子訊號：「我稍後會處理」或「我們最終會刪除」。

<a id="self-withdrawn-or-explicit-false-positive-rule-comments"></a>

### 自我撤回或明確誤報規則評論

- 信心：反覆
- 跳過條件：評論內容或後續 Bugbot 回覆明確表示該發現已撤回、符合規範或誤報，且代理可以本地驗證相關規則。
- 不要跳過條件：唯一證據是人類說「誤報」在高風險問題上，無解釋。
- 例子訊號：檔案命名規則評論，其內容說檔案已符合規範。

<a id="ask-by-default"></a>

## 預設詢問

不要自動跳過這些類別，即使先前 PR 駁回類似事項：

- 安全性、隱私、認證、計費、資料保留、訓練資料和許可權邊界發現。
- 高嚴重性發現。
- 遷移、schema、冪等性、併發和跨系統行為發現。
- 建議修復小而明顯降低風險且不改變產品意圖的評論。

歷史資料顯示人類有時駁回安全性/資料流評論。將這些視為所有者判斷，而非團隊級跳過規則。

<a id="candidate-learnings-from-recent-babysits"></a>

## 近期 babysit 的候選學習

在 babysit 期間或之後，當看起來對團隊有用但尚未成熟時，在此新增新的候選學習。優先將反覆候選提升到上方區段，一旦幾個 PR 確認模式。

<a id="manual-reimplementations-of-native-browser-behavior"></a>

### 原生瀏覽器行為的手動重新實現

- 信心：候選
- 跳過條件：幾乎從不。當差異將原生瀏覽器行為替換為手動等效（原生 sticky → JS 定位克隆、原生 scroll 目標 → 轉發 wheel/touch 事件、繪製順序遮擋 → masks/clip-path），Bugbot 對該程式碼的邏輯錯誤發現一直合理。
- 不要跳過條件：發現涉及事件轉發缺口（wheel deltaMode、touch pans、邊緣 scroll-chaining、tap slop）、mask/clip 命中測試分歧，或 observer-vs-React 狀態時間競態。預設修復。
- 例子訊號：「masks 不影響命中測試」、「overlay 阻止 wheel scroll」、「忽略 deltaMode」、「在 IntersectionObserver 回撥中執行，React 尚未應用狀態」。
- 來源：一個 sticky-occlusion PR：六次 Bugbot 透過，約十八個發現，每個都修復而非駁回。

<a id="contract-test-drift-claims-are-cheaply-verifiable--run-the-test-first"></a>

### 契約測試漂移聲稱便宜驗證。先執行測試

- 信心：候選
- 跳過條件：永遠不要跳過驗證本身；它只需一個命令。當 PR
  發布一個契約測試，以固定協議或文件說明文字（對
  符合 SKILL.md，文件措辭的截圖），且 Bugbot 聲稱「測試不再
  決定。
  分類。紅色執行結果以經驗證據確認該主張；綠色執行結果是
  具體的反證，用以支援駁回回應。
- 不要跳過的情況：無。這是驗證的快捷方式，而非駁回
  模式。注意，多次重跑的精簡駁回 heuristic 在此會誤判：
  散文固定測試會漂移，正是因為早期的修正回合修改了散文。
- 示例訊號：「契約測試忽略了修正前的等待」出現在一個 PR 上，其早期
  修正 commit 改寫了固定的段落；在最新提交上執行測試失敗於
  正是被引用的 assertion。
- 來源：一個散文固定 PR 有八次 Bugbot 透過；該主張在第 7 次
  是真實的，儘管每個早期回合都已修正並解決。

<a id="stale-security-review-finding-already-fixed-later-in-the-same-pr"></a>

### 過期的安全審查發現，已在同一 PR 中較晚修正

- 信心：候選
- 跳過的情況：當 agentic 安全審查（或類似）聲稱缺少 authz/validation 呼叫，且當前 PR 的最新提交明顯包含該確切檢查（含測試），通常在審查執行後新增的強化 commit 中加入。
- 不要跳過的情況：當被引用的 helper 對討論的主體是無效操作，檢查在它所保護的副作用之後執行，或對聲稱的主體缺少覆蓋。
- 示例訊號：一個 HIGH「缺少 authorization 檢查」的發現，而確切的檢查已在最新提交上於副作用之前被呼叫。
- 來源：一個 webhook 端點 PR，其強化 commit 晚於審查執行時間。

<a id="widening-a-deliberately-narrow-error-condition-would-mask-the-real-error"></a>

### 擴大刻意窄化的錯誤條件會遮蔽真實錯誤

- 信心：候選
- 跳過的情況：當發現要求將窄化的錯誤條件（特定的
  `errno`、錯誤碼或狀態類別）擴大為 catch-all，且該窄化
  編碼了真實的區別。典型形態是依賴 fallback
  以 `ENOENT` 作為門控：「binary 未安裝」與
  「命令執行並失敗」是不同的情況。對任何非零退出碼重試會對 fallback
  重新執行合法的失敗（找不到、auth 過期、網路問題），
  然後報告 fallback 的錯誤，隱藏真實的錯誤。
- 不要跳過的情況：當窄化條件漏掉同一類別中的案例
  （另一個「binary 無法使用」的 errno 如 `EACCES`、另一個傳輸層
  失敗），未處理的路徑會丟失資料或留下部分狀態，或重試
  是 idempotent 且原始錯誤仍會被呈現。
- 示例訊號：「僅當 X 以 ENOENT 失敗時重試 … 即使存在可用的 Y，
  也從不嘗試 fallback」，指向的程式碼中 fallback
  存在於缺少依賴而非操作失敗。
- 來源：一個 CLI 改名 PR，其 fallback 存在於缺少 binary 而非
  命令失敗。
