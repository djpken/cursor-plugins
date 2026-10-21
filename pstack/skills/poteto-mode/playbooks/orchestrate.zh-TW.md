<a id="orchestrate"></a>

### 編排

**你擁有程式，而非程式碼。撰寫任務摘要、清空佇列、保持前沿綠色、做出決策。** 對於交給一個常駐協調聊天室的整個專案：多日、許多堆疊的 PR、數十到數百個子代理，人類每天檢查兩次而不是每五分鐘一次。一個任務驅動到謂詞是自主執行。一個雄心勃勃的執行需要定製工作流程是自行解決。當工作超出任何單一代理的生命週期時，路由到這裡。一個代理能在會話預算內完成的工作不是程式。

儀式必須隨程式規模擴充套件。在廉價且幾乎相同的單元上，按每個部分指示將其簡化。

三條規則承載其餘部分。

- 完成是佇列事件，而非中斷。
- 每次生成和每次恢復都逐字攜帶常駐指令。
- 任務摘要是產品。模糊的摘要會悄然失敗，因為工作者無法向你提問。

<a id="roles-and-placement"></a>

#### 角色與位置

- **協調者（此聊天室）。** 本地。構建框架、撰寫任務摘要、清空收件箱、擁有人類報告、做出判斷決策。它從不撰寫或編輯程式碼。衝突合併、鏈式 rebase 和程式碼變更始終是任務。機械性地落地已驗證單元（快進或乾淨 cherry-pick 工作者提交，然後推送）是協調者可在本地 git 便宜的儲存庫上自行執行的簿記。將已完成工作排在空閒堆疊器後面是截止日期收穫零的方式。迴圈是端到端代理式的。代理僅透過 Task 工具生成、恢復和清空。根據子代理部分，為每個子代理選擇推理級別和快速或標準速度。狀態讀取和寫入透過 `scripts/orch/orch.ts` 在清空點進行，一個命令進，一行出。CLI 從不生成、等待或喚醒任何東西。
- **子協調者。** 始終本地、持久、每個軌道一個，且僅在程式超出單一協調者清空能力時。協調者自身可清空的軌道不需要中間層。每個巢狀層重新支付完整的定向前言，而阻塞的子協調者會隱藏其子項，而父級閒置。擁有其軌道的單元和板，撰寫其工作者任務摘要，生成其自己的工作者和驗證者（巢狀可至深度 3，巢狀生成具有完整 Task 架構，包括 `environment`）。在波邊界彙總聚合。絕不轉發原始子報告。將飛行中的子項限制在單一清空可處理的數量，大約十個，作為滾動視窗。絕不作為阻塞批次，這會付出每個批次最慢子項的代價。
- **工作者 / 驗證者。** 始終 `environment: "cloud"`，除非任務需要此機器：`control-ui` 或 `control-cli` 執行時驗證（來自 `cursor-team-kit`）。讀取本地轉錄在 `agent-transcripts/` 下。模擬器和本地 IDE 狀態。僅存在於此處的認證。雲代理無法讀取本地儲存庫，因此其任務摘要內聯所需內容或指向儲存庫路徑。偏好較少、更廣泛的工作者。每個工作樹或分支一個撰寫者（原則-共享狀態前先分離）。執行單元的驗證者使用與工作者不同的模型家族。

深度保持在協調者、軌道、工作者。按專案撰寫軌道分解（構建、落地和驗證是常見切分，不是必需形狀）。硬編碼的群集樹曾被嘗試並因過於僵化而擱置。

<a id="store-layout"></a>

#### 儲存庫佈局

在當前代理的儲存庫中建立 `orchestrate/<project-slug>/`（路徑在系統提示中）。每個檔案只有一個撰寫者。所有者釋出事實，讀者在讀取時聚合。使用 `bun scripts/orch/orch.ts` 進行簿記，以下寫作為 `orch`，而其標準純 TSV 和 JSON 保持可讀，無需 CLI。

- `preferences.md` 是常駐指令暫存器：編號行，每行一個約束（模型政策、堆疊形狀和計數、驗證標準、禁止路徑、升級政策）。將其逐字貼上到每次生成和每次恢復。指令在恢復中衰減，每個遺漏的指令耗費一個人類回合。當你發現自己重述指令時，先追加該行再行動（原則-將教訓編碼進結構）。
- `overview.md` 是持久 PR 和問題資料庫。追加。絕不按事件整體改寫。
- `units.tsv` 每個單元一行：id、軌道、狀態、分支、PR、頭 SHA、任務摘要路徑。就地更新行。
- `frontier.json` 是計算的合併前沿，按堆疊安全性。
- `ledger.tsv` 是驗證臺賬，按驗證。
- `inbox/` 持有完成指標。`gates.md` 停放人類門（問題、選項、無回答時的預設）。
- `decisions.tsv` 是透過展示你的工作技能留下的軌跡。
- `status.md` 在每次清空時從 `units.tsv` 和 `ledger.tsv` 派生，絕不手工維護。從表格重新生成，而非將事件敘述進去。

<a id="the-brief"></a>

#### 簡報

你對代理的提示是你唯一的產品，而粗糙的任務摘要會在整個樹中複合為粗糙。每次生成攜帶全部內容。你無法填寫的欄位是你尚未界定範圍的單元。

```
GOAL         one sentence, the outcome, executable by a stranger with no chat access
SCOPE        paths this unit may write; paths it may not; its exclusive worktree or branch
CONTEXT      pointers to files and PRs; upstream reports pasted in full when this unit
             depends on them, because workers cannot see siblings
ACCEPTANCE   checkable criteria, one per line
VERIFY       exact commands or the control-skill path, plus known gotchas
TIMEBOX      rough cap on runtime; on expiry, return partial findings and stop rather than run on
FORBIDDEN    no PR retargets or merges, no rebase, no force-push, no fixes outside scope,
             plus unit-specific bans
REPORT       status, branch, head SHA, PRs, verdict, what you actually ran, deviations,
             suggested follow-ups
STANDING     <preferences.md pasted verbatim>
```

將任務摘要大小匹配單元。一個命令的單元將模板壓縮為一段，仍命名目標、範圍、驗證命令和報告形狀。圍繞兩行編輯的 4KB 腳手架比編輯本身更昂貴。本地生成可透過儲存庫路徑引用常駐指令檔案。逐字貼上用於雲生成和每次恢復。

子協調者任務摘要新增其軌道邊界和單元列表、其生成預算（雲預設和本地例外列表）、清空協議和彙總格式（每個子項：名稱、狀態、PR、頭 SHA、裁決、一行，加上軌道狀態和前沿增量）。

依賴是上下文轉播，而非僅排序。未宣告的上游上下文使工作者猜測。缺失欄位是拒絕生成條件。每波審計每個子協調者一個抽樣工作者任務摘要，與抽樣波並行，絕不作為前置門。失敗的任務摘要停止該軌道並修復子協調者的指令，而非僅工作者，因為任務摘要質量在執行後期衰減。絕不恢復鏈任務摘要。以整合範圍重新生成。

<a id="steps"></a>

#### 步驟

1. **框架。** 將完成條件表述為可計數的專案（「全部 126 個單位已合併，每個帳本驗證為 `unit-test-verified` 或更高」）。量化範圍：單位、粗略工作量、預期堆疊數量，以及時間預算。如果一個代理能在該預算內完成，請在此停止並執行 Autonomous 執行。收束不得依賴另一份檔案的存在。這意味著直接在本次會話中完成工作，使用普通 worker 協助、內嵌驗證、邊做邊落地，且不使用下方所述的 store、register 或 pilot 機制。將落地時間安排對準預算。約在預算的 70% 時，停止產生新工作並將已驗證的部分落地。依專案命名各軌道。爭議性的分解或單向門須先透過 arena skill 再進入 pilot。僅呈現一次框架。可逆的準備工作不需等待。
2. **安裝執行環境。** 執行 `orch init`。透過 show-me-your-work skill 開啟 trail，在任何 spawn 之前寫下常設指令，並從現有 PR 以 `orch frontier set --repo <repo-dir>` 種入 `frontier.json`，當 forge 為 Origin 時加入 `--forge origin`。
3. **Pilot。** 將一個單位推過整個流程：brief、worker、驗證、堆疊記錄、帳本行、merge。Pilot 的目的是在僅耗費一個代理而非五十個代理的成本下，驗證 brief 模板、驗證配方和單位大小的有效性。根據 pilot 證據修正契約，再進行任何擴散。將 pilot 規模調整至單位規模。對於近乎相同且成本低廉的單位，第一個單位即為 pilot，以正常單位方式執行並內嵌驗證指令，一旦落地便開始擴散。專用的 pilot 流程（獨立驗證代理、審核門）適用於成本高昂或新型單位形態，而非複製型單位，因為序列化 pilot 對複製型單位無需驗證任何事項。
4. **擴散。** 產生滾動視窗的 worker，上限為飛行中數量，當子任務完成時補充。阻塞批次須等待每個批次中最慢的子任務。僅在 Roles 中超過單一 drain 閾值時才產生軌道子協調者。每次 drain 後重新計算待辦工作。將上游報告轉達至下游 brief。保持同層級溝通僅向上。抽樣 brief 審核與其所抽樣的批次同時執行，失敗時停止下一次補充，而非當前批次。
5. **Drain。** 在每個 drain 點執行以下佇列規則。
6. **落地。** 落地是持續過程，而非終端階段。整合從第一個驗證單位開始，並與剩餘批次同時執行。在大型儲存庫上，stacker 從第一批起為常設角色，隨單位驗證進行整合。在本地 git 成本低廉的儲存庫上，協調者依 Roles 自行落地驗證單位。保持 frontier 綠燈狀態，再進行上層堆疊工作。堆疊安全規則優先。僅在 merge 或報告新的 head SHA 時推進 `frontier.json`。
7. **收束。** 清空最終 inbox，將每個 spawned 代理對應至終端行（完成、放棄、zombie 對帳），確認真實產物符合條件，確認每個已落地的 PR 對其當前 head SHA 有判定結果，依 show-me-your-work 審核 trail，包括跨模型檢視，將重現性修正編入 `preferences.md` 或 brief 模板。保留 store 完整。這是事後檢討。

<a id="queue-and-drain"></a>

#### 佇列與 drain

- 收到完成通知時，執行 `orch inbox push <agent> <unit> <status> [--report PATH]` 並返回原先工作。絕不在 inline 中深度審核。需要審核的完成事項應成為 verifier 單位。絕不在 drain 過程中審核 diff。
- 在四個點分批 drain：關鍵段落結束、軌道彙整、frontier watcher 喚醒（透過 loop skill 設定，並有長心跳備援），以及人類報告之前。每批以 `orch inbox drain` 開始。drain 期間的新到訊息等待下一批。
- 優先完成關鍵段落：撰寫 brief、堆疊操作、衝突決策、寫 gate、更新帳本或 frontier。
- 每個 drain 將每個 pointer 分類（已落地、需驗證、失敗、zombie、噪音），透過 `orch unit add`、`orch unit set` 和 `orch ledger record` 寫入結果行，執行 `orch status`，然後在單一訊息中 spawn 下一批。
- 在每個軌道的彙整中，對每個 spawned 子任務進行對帳：已到達、重新 spawn，或其範圍明確被吸收。靜默重做缺失子任務的工作會隱藏浪費的支出及其結果本應填補的覆蓋缺口。
- 一個 drain 回合以 `orch status` 的三行結束：各狀態計數、變更內容、開啟的 gate。細節在 `status.md`。完整回覆契約適用於檢查點和收束。

<a id="stack-safety"></a>

#### 堆疊安全

- frontier 是計算物件，而非敘述。每次 merge 和堆疊變動後重新計算 `frontier.json`：有序 PR 列表、分支名稱、head SHA、世代號、最低未 merge 的 PR。指令透過 checkout 當前分支走 PR 基礎鏈，因此鏈順序和 PR 狀態來自 forge。預設使用 `gh`。當程式將 forge 解析為 Origin 時傳入 `--forge origin`。head SHA 來自本地分支引用，因此從 stacker 的 clone 解析。無 PR 的分支應報錯而非猜測。
- 每個堆疊僅一個 stacker 可變動拓撲（鏈 rebase、force-push、基礎重定向、merge），並在該堆疊內序列化。記錄持有者在常設指令中。鏈 rebase 在雲端執行。此規模的本地 rebase 會使筆電癱瘓。
- Worker 絕不 rebase、重定向或 merge PR。Babysitter 遵循 `playbooks/babysit.md`，每個堆疊一個，僅限於一個不可變 frontier 世代。他們向 stacker 報告衝突，而非自行 rebase。
- PR 關閉和重定向僅透過 stacker。關閉基礎 PR 會孤立其上所有鏈。Merge 和堆疊手術是單位，如同其他單位需有 brief。
- 一個 retro watcher 追蹤已 merge 的 PR，檢查 revert、post-merge CI 故障和孤立後續事項。

<a id="verification"></a>

#### **驗證。** 將驗證規模調整至單位規模。當 VERIFY 是單一低成本指令時，worker 執行並報告輸出，協調者抽查收據。專用 verifier 代理（使用與 worker 不同的模型家族）適用於驗證成本高昂、需判斷或高影響範圍的單位。一個 verifier 代理的整個產物僅是重跑一個指令，那是儀式而非驗證。

將驗證規模化到單位層級。當 VERIFY 是單一低成本指令時，工作者執行它並報告輸出，而協調者抽查收據。專門的驗證者代理（使用與工作者不同的模型家族）適用於驗證成本高昂、涉及判斷或影響範圍大的單位。若驗證者代理的整個產出僅是重新執行一個指令，那是儀式，而非驗證。

以 `orch ledger record` 寫入帳本行。用 `orch ledger check` 檢查目前的 PR 與 head SHA。`ledger.tsv`，每個判定一行，以 PR 編號加上 head SHA 作為鍵：`live-ui-verified | unit-test-verified | type-check-only | verifier-blocked | verifier-failed`。CI 綠燈是判定的輸入，不是判定本身。行為相關工作需要比 `type-check-only` 更好的標準。`verifier-blocked` 不算透過。環境恢復後重新啟動。`verifier-failed` 獲得修正單位，而非重新驗證。工作者可自行報告。驗證者在相同鍵上覆蓋其結果。新的 head SHA 使該行失效，因此重新堆疊後需重新驗證。帳本回答「是否已驗證」，而非記憶或對話紀錄。

一個單位只有在其輸出在落地時立即外部化才算完成，絕不等到整個執行結束才批次處理。工作者推送其分支，驗證者寫入其帳本行，收據存入儲存庫。僅存在於某個 VM 上的工作，當該 VM 消失時，該工作便未曾完成。

<a id="liveness-and-failure"></a>

#### 存活與失敗

- 絕不重新啟動代理來檢查其狀態。重新啟動會重啟一個閒置的代理。僅以讀取方式探查：帳本、`units.tsv`、`gh`、已推送的分支、雲端代理在 Cursor 介面中的狀態。對話紀錄的修改時間不代表存活狀態。
- 無聲死亡會在收件箱中產生一行合成事後分析記錄（單位、失敗模式、最後證據、選項）。根據證據到達的情況重新規劃。切勿等待完全靜止。
- 依模式重試：達到上限或記憶體不足時，以較小範圍重新啟動。網路中斷時，原樣重試。工具錯誤時，改用不同模型重試。未知錯誤時，重試一次。重試兩次後，放棄該單位並繞過它重新規劃。
- 數小時後才回傳的殭屍任務，在接受任何內容之前，必須先與目前的邊界和帳本進行對帳。透過新的單位搶救獨有的發現，絕不進行盲目合併。
- 若持續生成會導致整個樹狀結構產生垃圾（上游輸出不良、驗收失敗、基礎設施停擺），請在常規指令頂部寫入停止線，讓進行中的工作完成，修復原因，然後清除該停止線。
- 以限制子任務的方式來限制自身基礎設施的重試。在連續幾次工具中斷後，停止重試。將終端交接寫入持久狀態（已完成事項、存放位置、精確的恢復指令），並結束執行。
- Cursor 重新啟動後：本機 agent 已死亡，雲端工作未死亡。重新閱讀常規指令和 `units.tsv`，重新計算邊界，透過 PR 和分支而非 agent id 重新掛接雲端工作，根據儲存的簡報加上目前狀態為每個軌道重新啟動一個子協調者，排空佇列，恢復執行。已死亡 session 的儲存鎖會在下次寫入時自行清除。`orch` 會取代持有者 pid 已消失的鎖。

<a id="escalation"></a>

#### 升級

到達人類層級時，會批次匯入狀態頁面，而非逐項處理：不可逆操作（對共享分支強制推送、部署、刪除、關閉他人的 PR）、實驗無法解決的真正產品或偏好決策、與觀察到的現實相矛盾的常規指令、經歷重新規劃後仍存在的程式層級死路。在詢問之前，將每項作為 `gates.md` 條目暫存，並繞過該問題繼續工作。

絕不到達人類層級：邊界調整、rebase 機制、重試、CI 不穩定問題分級、審查執行緒分級、格式修正、簡報已禁止的範圍（拒絕並繼續），以及「我是否應該繼續」。當有疑問時，採取行動並記錄。

執行中期的發現僅修復阻擋邊界的事項。其他一切暫存於後續事項中。在此扇出規模下，微小的範圍洩漏會倍增為無人要求的 PR。

**回覆：** 在檢查點和結束時：來自 `units.tsv` 和 `ledger.tsv` 的預言條件及其對應計數、軌道及各軌道落地的內容、邊界（PR 清單加上 SHAs）、判決摘要、被放棄的內容及原因、等待人類處理的閘門（唯一的請求）、儲存路徑，以及軌跡路徑。數字來自表格，而非敘述。包含 PR 連結。
