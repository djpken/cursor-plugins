---
name: setup-benny
description: "設定 Benny 並準備其分級與重現自動化。在安裝 Benny 或變更其 Slack、追蹤器、儲存庫、路由、控制、模型或預算設定時使用。"
disable-model-invocation: true
---

<a id="set-up-benny"></a>

# 設定 Benny

Benny 以休眠自動化套件的形式包含在 pstack 中。外掛清單僅公開 pstack 的標準 skill 根目錄；此檔案與兩個操作檔案並非斜線 skill。

使用者透過將 Cursor 指向套件的 `FOR_AGENTS.md` 來進入設定流程。啟動流程會將整個套件複製到目標儲存庫，然後直接在 `.cursor/automations/benny/skills/setup-benny/SKILL.md` 讀取此檔案。

Benny 需要外部設定以及兩個實時的 Cursor 自動化。

除非使用者明確要求，否則不要建立或更新自動化。切勿將機密值放入外掛檔案、提示詞或已提交的設定中。

<a id="1-copy-the-pack-and-enable-shared-pstack-skills"></a>

## 1. 複製套件並啟用共用的 pstack skills

在詢問 Benny 設定以及呼叫內建的 `/automate` skill 之前執行此步驟。

詢問將執行自動化的儲存庫。來源套件是包含 `FOR_AGENTS.md` 的目錄。目的地為 `<target-repository>/.cursor/automations/benny/`。

將整個來源套件合併到目的地：

1. 若目的地不存在，請建立它。
2. 將每個來源檔案複製到相同的相對路徑。
3. 保留僅存在於目的地的檔案。在安裝或更新期間，切勿刪除無關檔案。
4. 將使用者擁有的設定、功能地圖和路由地圖保留在目的地之外。切勿覆寫它們。
5. 當現有的來源管理檔案存在差異時，檢查 diff 並進行合併，同時保留本地編輯。如果所有權不明確，請停止並詢問後再替換。
6. 驗證目的地包含 `FOR_AGENTS.md`、此設定檔案、兩個操作檔案、其參考專案以及範本。

如果此檔案已從目標目的地讀取，請視為複製完成，並在繼續之前執行相同的驗證。

將 pstack 加入目標儲存庫的 `.cursor/settings.json`。如果該檔案或 `.cursor` 目錄不存在，請建立它。

將此專案合併到現有的 JSON 或 JSONC 中：

```json
{
	"plugins": {
		"pstack": { "enabled": true }
	}
}
```

保留所有無關的頂層設定以及其他外掛專案。如果 `plugins.pstack` 已存在，僅變更其 `enabled` 值。當檔案使用 JSONC 時，保留註解和有效的 JSONC 語法。編輯後驗證檔案。

重新載入目標專案或啟動一個以該專案為根的新 agent。驗證這些共用的 pstack skills 是否從專案範圍解析：

- `how`
- `why`
- `tdd`
- `unslop`
- `principle-separate-before-serializing-shared-state`
- `principle-minimize-reader-load`
- `principle-guard-the-context-window`
- `principle-sequence-verifiable-units`
- `principle-fix-root-causes`
- `principle-prove-it-works`

不要計算從目前 session 或使用者範圍外掛載入的 skill。檢查必須顯示目標儲存庫中的新 agent 透過專案設定接收 pstack。

如果專案範圍的外掛安裝不可用，或任何共用依賴無法解析，請停止並說明失敗原因。

Benny 檔案直接從 `.cursor/automations/benny/` 讀取。不要將該目錄加入外掛清單，也不要預期其 `SKILL.md` 檔案會出現在斜線 skill 清單中。

告知使用者，在啟用任何自動化之前，必須提交 `.cursor/settings.json`、`.cursor/automations/benny/` 以及任何不含機密的參考設定。除非使用者要求，否則不要提交它們。

一旦此檢查透過，實時自動化提示詞即可透過穩定的儲存庫相對路徑讀取已提交的操作檔案。它們不得嵌入外掛快取路徑或複製檔案內容。

<a id="2-adapt-the-configuration"></a>

## 2. 調整設定

開啟這些複製的範例：

- `../../templates/configuration.example.yaml`
- `../reproduce-and-fix-issues/references/feature-map.example.md`

在 `.cursor/automations/benny/` 之外建立使用者擁有的副本。這些是設定檔案，而非套件檔案。範例位置：

- 專案設定，例如 `.cursor/benny/configuration.yaml`
- 專案功能地圖，例如 `.cursor/benny/feature-map.md`
- 專案路由地圖，例如 `.cursor/benny/routing.md`
- 使用者設定，例如 `~/.config/benny/configuration.yaml`
- 使用者功能地圖，例如 `~/.config/benny/feature-map.md`

為自動化可能重現的每個使用者端功能填寫一個功能地圖區段。保持使用者視角。不要在地圖中固定實作細節或目前的程式碼路徑。

不要編輯複製的範例。套件更新可能在衝突審查後更新來源管理的檔案，但絕不能觸及使用者擁有的副本。

當新的自動化 checkout 必須讀取時，優先使用目標儲存庫中已提交且不含機密的檔案。否則，將所需的值改寫到實時提示詞中。只有在內建的 `/automate` skill 確認該檔案已在自動化執行的儲存庫中提交後，才參考儲存庫檔案。

對已提交的套件和設定檔案使用穩定的儲存庫相對路徑。切勿從實時自動化中參考外掛來源目錄或外掛快取路徑。

<a id="3-fill-the-required-choices"></a>

## 3. 填寫必要的選項

詢問或確認：

- 來源 Slack 頻道 ID
- 選用的操作或狀態頻道 ID
- 儲存庫 URL 和預設分支
- 分級身分或 Slack 使用者 ID
- 問題追蹤器型別、團隊、專案、標籤和接收狀態
- 追蹤器介面卡 skill 或 MCP 動作
- 選用的路由地圖路徑
- 必要的控制 skill 名稱
- 必要的端使用者功能地圖路徑
- 狀態 emoji 字串
- Pull request URL 格式
- 輪詢和工作量預算
- 用於分級、重現、程式碼工作和媒體審查的 model slug

僅使用在 Cursor 模型選擇器或支援模型清單中顯示為可用的 model slug。不要猜測 slug，也不要沿用私人預設值。

來源頻道、分級身分、儲存庫、追蹤器介面卡、控制 skill 和功能地圖必須明確。如果任何必要值仍不明確，請讓設定失敗。

在儲存最終自動化名稱、描述和提示詞 shim 之前，使用 pstack 的 `unslop` skill。

<a id="4-check-integration-capabilities"></a>

## 4. 檢查整合功能

分級自動化需要：

- 對設定的來源 Slack 頻道及其執行緒的讀取許可權
- 該頻道中的執行緒回覆許可權
- 當報告包含媒體時，需要附件中繼資料和檔案下載許可權
- 透過設定的問題追蹤器介面卡進行搜尋、讀取、建立和更新的許可權

重現自動化需要：

- 對來源執行緒的讀取許可權
- 來源頻道中的執行緒回覆許可權
- 設定的操作頻道中的選用發布和編輯許可權
- 儲存庫讀取和歷史記錄許可權
- 可以開啟草稿 pull request 的 pull request 動作
- 設定的控制介面卡 skill

優先使用設定的 Cursor Slack 動作進行讀取和發布。選用的 `BENNY_SLACK_BOT_TOKEN` 可以填補狹窄的缺口，例如編輯一條操作狀態訊息或下載附件。將值儲存在機密管理器或環境中，而非 YAML 中。

不要使用未記錄的整合端點。

<a id="5-prepare-the-routing-map"></a>

## 5. 準備路由地圖

如果使用者希望重新路由或通知負責人：

1. 將 `../triage-issue-reports/references/routing.example.md` 複製到 `.cursor/automations/benny/` 之外。
2. 將每個佔位符替換為公開或組織本地的值。
3. 預設關閉負責人通知。
4. 僅允許向已設定的功能負責人或已確認的可能回歸作者傳送 ping。

如果未設定路由地圖，分診可以對報告進行分類，但不得猜測目的地或負責人。

<a id="6-verify-the-control-adapter"></a>

## 6. 驗證控制介面卡

讀取 `../reproduce-and-fix-issues/references/control-adapter.md` 以及使用者已完成的功能地圖。

確認指定的 skill 能夠：

- 啟動目標應用程式
- 透過真實 UI 導覽所有已對映的功能
- 透過已宣告的介面卡動作執行已對映的狀態
- 檢查狀態而不強制產生結果
- 擷取螢幕截圖
- 開始和停止錄製
- 清理其程式與暫存資料

如果缺少任何功能，請保持重現自動化處於停用狀態。它必須在失敗時關閉，而不是聲稱執行了一項未實際進行的重現。

<a id="7-prepare-the-live-automations"></a>

## 7. 準備即時自動化

詢問這是一次性建立，還是對現有自動化的設定。

從複製的 pack 中讀取 `../../FOR_AGENTS.md`，作為兩種路徑的主要使用者意圖來源。使用它來理解兩個觸發器、工具、指示、結果以及共用規則。

<a id="first-time-creation"></a>

### 首次建立

一次建立一個自動化。

對於每個自動化：

1. 讀取對應的複製提示範本，作為次要內部來源材料。
2. 將 `FOR_AGENTS.md`、完成的 Benny 設定以及範本意圖轉換為完整的自然語言請求。
3. 告訴即時提示讀取並遵循其位於 `.cursor/automations/benny/` 下的精確已提交操作檔案。
4. 使用穩定的儲存庫相對路徑，而非 plugin 來源或快取路徑。不要將操作檔案內容複製到即時提示中。
5. 讀取並遵循內建的 `automate` skill。
6. 讓 `automate` 發現 Slack 頻道、儲存庫以及已連線的整合。
7. 讓 `automate` 確認複製的 pack 和任何引用的設定檔案都已提交在自動化將要執行的同一個儲存庫中。
8. 讓 `automate` 顯示其草稿表格、取得核准、詢問就緒狀態，並開啟 Automations 編輯器。
9. 在開始下一個之前，完成此自動化的編輯器交接。

將此完整的分診意圖提供給 `automate`，並從設定中填入內容：

- 命名為 `benny-triage`。
- 在每次執行時讀取並遵循 `.cursor/automations/benny/skills/triage-issue-reports/SKILL.md`。
- 在設定的來源 Slack 頻道中，針對每個新的頂層報告觸發。
- 讀取觸發的執行緒，並僅在其中回覆。
- 使用設定的問題追蹤器整合。
- 進行分類、檢查證據、追蹤原因、去重，並僅建立明確的新 bug。
- 以設定的 `[benny:bug]`、`[benny:performance]` 或 `[benny:other]` 標記以及可選的追蹤器 URL 結束僅限執行緒的單一判定。
- 絕不發布來源頻道的根訊息。

分診編輯器交接完成後，將此完整的重現與修復意圖提供給 `automate`：

- 命名為 `benny-reproduce`。
- 在每次執行時讀取並遵循 `.cursor/automations/benny/skills/reproduce-and-fix-issues/SKILL.md`。
- 在設定的來源 Slack 頻道中，針對相同的新頂層報告觸發。
- 使用設定的儲存庫和預設分支。
- 讀取來源執行緒，並僅在其中回覆。
- 包含 pull request 建立以及設定的追蹤器、控制介面卡和功能地圖要求。除非 `automate` 確認同一儲存庫中存在符合資格的已提交檔案，否則請改述已對映的使用者路徑和狀態。
- 在採取行動前，等待受信任的分診標記。
- 透過已對映的真實 UI 兩次重現精確的症狀，並擷取證據。
- 驗證現有修復，而不覆蓋其作者資訊。
- 僅在確認重現後，嘗試可選的有限修復，然後在證明和檢查透過時開啟草稿 pull request。
- 絕不發布來源頻道的根訊息。

不要重複 `automate` 的 Slack、儲存庫、整合、完整性、驗證、草稿審查、核准、就緒狀態或編輯器交接工作。

<a id="existing-automations"></a>

### 現有自動化

內建的 `automate` skill 僅用於建立。不要使用它來搜尋、檢查或更新現有自動化。

完成設定、路由、控制介面卡和功能地圖驗證。然後向使用者提供此簡潔的編輯器檢查清單。

對於現有的分診自動化，更新：

- 名稱和描述
- 讀取 `.cursor/automations/benny/skills/triage-issue-reports/SKILL.md` 的直接指示
- 新的頂層 Slack 報告觸發器和來源頻道
- Slack 執行緒讀取和回覆功能
- 問題追蹤器整合
- 改述的分診指示、僅限執行緒規則以及 Benny 判定標記

對於現有的重現自動化，更新：

- 名稱和描述
- 讀取 `.cursor/automations/benny/skills/reproduce-and-fix-issues/SKILL.md` 的直接指示
- 對應的 Slack 觸發器和來源頻道
- 儲存庫和預設分支
- Slack 執行緒讀取和回覆功能
- Pull request 動作
- 追蹤器、控制介面卡和功能地圖要求
- 改述的標記等待、證據、驗證和有限修復指示

要求使用者直接在各自的 Automations 編輯器中更新每個現有自動化。不要建立替代品或重複專案。

<a id="creation-boundary"></a>

### 建立邊界

絕不呼叫直接的自動化後端服務或後端自動化工具。絕不使用攜帶草稿欄位的瀏覽器 URL。絕不建立或開啟 Cursor 協定深連結。對於新自動化，唯一的完成路徑是內建 `automate` skill 的已審查 Automations 編輯器交接。

在編輯器儲存後，執行緒安全性測試透過之前，不要啟用任何一個自動化。

<a id="8-test-thread-safety"></a>

## 8. 測試執行緒安全性

使用測試頻道或無害的測試報告。

在測試前，確認目標儲存庫的 `.cursor/settings.json`、`.cursor/automations/benny/` 以及所有引用的無機密設定檔案都已提交在自動化 checkout 所使用的分支上。確認兩個即時提示都指向其精確的已提交操作檔案。如果任何檢查失敗，請停止。告訴使用者該自動化目前尚無法啟用。

驗證：

1. 分診儲存根 `thread_ts`，並作為回覆發布精確的一個判定。
2. 判定包含一個設定的標記。
3. 重現僅接受來自設定的分診身分的標記。
4. 重現保持相同的不可變來源座標。
5. 不出現來源頻道的根訊息。
6. 委託的工作者無法使用任何 Slack 寫入動作。
7. 缺少座標、已刪除的父專案或預檢失敗不會產生任何發布或追蹤器問題。

僅在所有七項檢查透過後，才啟用正常流量。
