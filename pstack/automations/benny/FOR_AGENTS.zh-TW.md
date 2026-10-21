<a id="benny-automation-intent"></a>

# benny 自動化意圖

<a id="what-i-want-to-automate"></a>

## 我想要自動化的內容

我想要兩個 Cursor 自動化流程，它們在同一個 Slack 問題頻道中協同運作。

<a id="automation-1-triage-issue-reports"></a>

### 自動化 1：分發問題報告

- 觸發條件：當有人在設定的來源 Slack 頻道中發布新的頂層報告時，我希望此自動化流程針對該報告啟動，並保留其原始執行緒座標。
- 行為：我希望它讀取執行緒和附件，將報告分類為錯誤或效能問題、功能請求、問題或回饋，或重新路由，並在路由前追蹤可能的負責層級。
- 追蹤器：我希望它在設定的追蹤器中搜尋重複專案，更新確定的重複專案，並僅為明確的全新錯誤建立工單。
- 工具：我需要 Slack 執行緒讀取和回覆存取許可權、設定的追蹤器整合，以及選用的路由地圖。
- 結果：我希望在來源執行緒中恰好有一則回覆，包含簡短的判定結果以及 `[benny:bug]`、`[benny:performance]` 或 `[benny:other]`。錯誤或效能標記可以包含追蹤器 URL。
- 邊界：我絕不希望此自動化流程在來源頻道中發布根訊息。

<a id="automation-2-reproduce-and-fix-confirmed-bugs"></a>

### 自動化 2：重現並修復已確認的錯誤

- 觸發條件：我希望此自動化流程從相同的新頂層報告開始，或從設定期間選擇的其他支援觸發條件開始，然後等待原始執行緒中的可信分發標記。
- 閘門：我希望當有人明確負責修復時停止。如果現有的 pull request 或已合併的 commit 可能修復該報告，我希望進行驗證，而不是進行競爭性的變更。
- 行為：我希望它使用設定的控制介面卡和功能地圖，透過真實 UI 兩次重現確切的症狀，並擷取螢幕截圖、影片和唯讀狀態交叉檢查。
- 修復：我希望它驗證現有的 pull request，而不對其進行覆寫。在確認重現後，它可以嘗試一次有邊界的根本原因修復，在測試成本低時使用 TDD，測試影響範圍，並僅在修復前後證明透過時開啟草稿 pull request。
- 工具：我需要 Slack 執行緒讀取和回覆存取許可權、儲存庫和歷史記錄存取許可權、草稿 pull request 建立功能、設定的追蹤器，以及我的控制介面卡。
- 結果：我希望在來源或選用的操作執行緒中有證據和驗證結果，外加選用的草稿 pull request。更新應簡潔。
- 邊界：我絕不希望此自動化流程在來源頻道中發布根訊息。

<a id="shared-rules"></a>

### 共用規則

- 我希望來源頻道和根執行緒座標在整個執行期間保持不變。
- 我將公用程式和除錯機器人視為證據，而非委派或修復所有權。
- 我允許 subagents 協助，但它們不能發布到 Slack 或接收 Slack 憑證。
- 我希望整個套件在目標儲存庫中提交於 `.cursor/automations/benny/`。其 `SKILL.md` 檔案是直接的自動化指令，而非已註冊的 plugin skills。
- 我希望透過目標儲存庫中已提交的 `.cursor/settings.json` 啟用 pstack，僅限於 `how`、`why`、`tdd`、`unslop` 等共用依賴項，以及所需的原則 skills。
- 我希望每個實時自動化提示直接讀取其已提交的操作性檔案。我不希望使用 plugin 快取路徑、複製的摘要或斜線 skill 發現。
- 我將使用者擁有的設定、功能地圖、路由地圖和金鑰保留在 `.cursor/automations/benny/` 之外，以便套件更新不會覆蓋它們。
- 我希望當頻道座標、追蹤器存取許可權、控制介面卡或功能地圖缺失或不確定時，兩個自動化流程都失敗關閉。
- 我只希望有草稿 pull request。不要合併或部署。

<a id="my-configuration"></a>

### 我的設定

- 來源 Slack 頻道：`<channel>`
- 選用的操作頻道：`<channel or none>`
- 儲存庫和預設分支：`<repo>`, `<branch>`
- 追蹤器：`<type, team, project, labels, intake status>`
- 路由地圖：`<path or none>`
- 分發身分：`<slack identity>`
- 控制 skill：`<configured skill or adapter>`
- 功能地圖：`<committed same-repo path outside the copied pack, or behavior to paraphrase>`
- 模型：`<triage, reproduce, code, media review>`
- 狀態 emoji 字串：`<seen, reproducing, reproduced, blocked, fixing, failed, pull request opened>`
- 預算：`<polling, verdict wait, follow-up, repro, rejection, fix>`
- 選用的 bot token 功能：`<none, file download, or editable operations status>`

從 [`configuration.example.yaml`](./templates/configuration.example.yaml) 和 [`feature-map.example.md`](./skills/reproduce-and-fix-issues/references/feature-map.example.md) 開始。在套件外部複製並填寫它們，例如在 `.cursor/benny/` 下。將金鑰值保留在金鑰管理器或環境中。

<a id="for-the-agent"></a>

## 給 agent 的說明

人類透過將 Cursor 指向此檔案來進入設定。不要尋找或呼叫已發現的 benny 斜線 skill。

1. 詢問哪個儲存庫將執行自動化流程。
2. 將包含此 `FOR_AGENTS.md` 的目錄視為來源套件。
3. 將整個來源套件合併到 `<target-repository>/.cursor/automations/benny/`。
4. 保留每個僅存在於目標的檔案。絕不刪除無關檔案或覆蓋使用者擁有的設定、功能地圖或路由地圖。
5. 當來源管理路徑上的現有目標檔案有差異時，檢視 diff 並合併，而不丟棄本地編輯。如果所有權不明確，停止並詢問後再替換。
6. 驗證複製的 `FOR_AGENTS.md` 和 `skills/setup-benny/SKILL.md` 是否存在於目標儲存庫中。
7. 直接從目標儲存庫讀取並遵循 `.cursor/automations/benny/skills/setup-benny/SKILL.md`。

我希望你將此條目合併到目標儲存庫的 `.cursor/settings.json`：

```json
{
	"plugins": {
		"pstack": { "enabled": true }
	}
}
```

保留每個無關的設定和 plugin。當現有檔案使用 jsonc 時，保留註解和有效的 jsonc 語法。

我希望從根植於目標儲存庫的新 agent 進行驗證。確認 pstack 的 `how`、`why`、`tdd`、`unslop` 以及 benny 使用的原則 skills 在專案範圍內解析。不要計算從目前 session 或使用者範圍安裝中載入的 skills。

如果專案範圍的 plugins 不可用或任何共用依賴項無法解析，停止並解釋失敗原因。不要將 `.cursor/automations/benny/skills/` 新增到 plugin 清單，也不要預期其檔案出現在斜線 skill 清單中。

告訴我 `.cursor/settings.json`、`.cursor/automations/benny/` 以及任何引用的無金鑰設定必須在啟用任何自動化流程之前提交。在我明確要求之前，不要建立或更新自動化流程。

對於首次建立，使用內建的 `/automate` 一次用於分發，一次用於重現和修復。在開始第二個之前，完成第一個自動化流程的草稿審查、核准、就緒檢查和 Automations 編輯器交接。

將此意圖和完成的設定改寫到每個草稿中。分發提示必須讀取並遵循 `.cursor/automations/benny/skills/triage-issue-reports/SKILL.md`。重現提示必須讀取並遵循 `.cursor/automations/benny/skills/reproduce-and-fix-issues/SKILL.md`。僅在 `/automate` 確認這些 repo 相對路徑已在自動化流程將執行的儲存庫中提交後，才使用這些路徑。

對於現有的自動化流程，不要使用 `/automate` 來檢查或更新它們。驗證設定，然後使用複製的設定檔案中的簡潔欄位清單，以便我可以直接在編輯器中編輯每個自動化流程。不要建立重複專案。
