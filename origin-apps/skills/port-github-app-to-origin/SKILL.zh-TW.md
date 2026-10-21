---
name: port-github-app-to-origin
description: >-
  規劃將現有的 GitHub App 移植到 Cursor Origin App。當任務是將 GitHub App 帶到 Origin，或將其使用的功能與 Origin API 進行比較時使用。
  從應用程式的程式碼中讀取其需求，將其對應到現行的 Origin 規格，並撰寫一份包含給 Cursor 回饋的移植簡報。
  僅進行規劃。
  將 GitHub App 移植為 Origin App
  僅限。
license: MIT
compatibility: >-
  Needs network access to https://cursor.com/docs/api/origin/* at run time.
---

<a id="port-a-github-app-to-an-origin-app"></a>

# 將 GitHub App 移植為 Origin App

**在程式碼中尋找，不要詢問。** 從原始碼中讀取應用程式使用的內容。任何無法找到的專案都將成為給團隊的問題。
團隊，以及一個可直接傳送的「給 Cursor 的回饋」章節
(`references/brief.md`)。此 skill 負責規劃。它不會撰寫或修改程式碼
除非使用者在閱讀簡報後要求這樣做。請遵循
`origin-api` skill 中的檔案與需優先檢查的規則。還有兩條規則：

1. 應用程式處理的每個 webhook 事件，以及每個處理器讀取的每個 payload 欄位，包括僅記錄紀錄的欄位。
   每個 REST 和 GraphQL 呼叫，包括程式碼傳遞的引數與過濾器、讀取的回應欄位、是否在每個 webhook 上執行或是在迴圈中執行，以及如何進行分頁。
2. 驗證：JWT 演演算法、應用程式在安裝後如何得知 installation ID、如何處理 token 過期、任何使用者登入及其用途，以及應用程式是否複製或推送 git。
   簡報中可能會引用 `file:line`。給 Cursor 的回饋僅說明該應用程式
   需要執行的動作，以及 Origin 在這些方面所缺乏的功能，使用 Origin 的術語，且不涉及任何檔案
   如何對應

<a id="what-to-find"></a>

## 需要尋找的內容

為每個專案記錄 `file:line`，並註記您查詢過但未
找到的內容。

- 已宣告的許可權與事件，來自 manifest 或基礎設施程式碼
  （如果已提交至版本控制）。否則，從呼叫中推導它們。
- 匹配的名稱只是候選，不是答案。將操作的描述、引數和回應欄位與程式碼傳遞和讀取的內容進行比對。如果程式碼依賴的引數或欄位缺失，那是變通方法或缺口，而非匹配。
  讀取操作，包括它僅記錄紀錄的欄位。
- Origin 沒有 GraphQL。將每個查詢分解為 REST 呼叫並記錄扇出（fan-out）。
  傳遞路徑、它讀取的回應欄位，以及它是在每個 webhook 上執行，還是
  每個 GitHub 事件和動作對最多對應「Events」中的一個 slug。動作是 slug 的一部分。沒有 slug 的對在 Origin 上不是事件。
- 驗證：JWT 演演算法、應用程式如何得知安裝 ID
  檔案從未提及的功能目前不可用。向團隊詢問。檔案既未確認也未否認的行為，需提出問題，並在端到端測試中加入檢查該行為的步驟。不要假設它像 GitHub 上一樣運作。
  完成前
- 關於 Origin 的每個宣告都指向儲存檔案中的某處。每個缺口都有對應的回饋專案，並指明其成本。回饋不透露團隊的任何內部資訊。摘要需指出原生或映象的問題。
  主體（body）在驗證時，以及它如何對傳遞進行去重。
- 框架或函式庫為應用程式發出的呼叫。Probot 的接收器、token
  快取與設定載入器；Octokit `App` 的安裝與儲存庫
  清單；app-auth 函式庫。閱讀相依專案的檔案，並列出這些
  標記為「來自 `<dependency>`」的呼叫。

<a id="how-to-map"></a>

## 如何對應

簡報需要完整的規格，但 `openapi.yaml` 和 `llms-full.txt` 各自
都有數百 KB 大，無法完整讀入上下文。如果可以，請將它們儲存到本地
應用程式儲存庫之外的臨時位置，以免工作樹中的任何內容被覆寫或遺留，
儲存庫，因此工作樹中的任何內容都不會遭到覆寫或遺留，
然後搜尋它們，並僅讀取匹配的部分：

- `x-origin-scopes:` 中的 `openapi.yaml` 會標記每個操作及其
  範圍；`operationId` 位於其上方一行。
- `x-origin-webhook-events:` 中的 `openapi.yaml` 會標記每個 webhook 負載
  模式，以及傳遞該負載的事件 slug。
- `### <Endpoint name>` 和 `### <Payload type>` 標題在 `llms-full.txt` 中
  開始說明該端點或酬載的欄位，格式為
  點分路徑；從標題讀取到下一個 `###`。

對於稍後的單一問題，從 `llms.txt` 開始，僅擷取該
章節。

- 匹配的名稱只是候選，並非答案。將操作的
  描述、引數與回應欄位，與程式碼傳遞和讀取的內容進行比對。如果程式碼依賴的引數或欄位缺失，那
  以及讀取。如果程式碼依賴的引數或欄位缺失，那就是
  是變通方法或缺口，而非匹配。
- GitHub 位於 `/issues/{n}/…` 下的 Pull Request 呼叫（評論、
  標籤）在 Origin 上位於 Pull Request 端點下。如果程式碼在實際的 issue 上使用
  它們，請參閱 Origin 中的「GitHub 功能在 Origin 上的位置」。
  `references/brief.md`.
- Origin 沒有 GraphQL。將每個查詢拆解為 REST 呼叫，並記錄
  扇出（fan-out）。
- 需要請求的範圍是 `x-origin-scopes.scopes` 在您指定的操作上的
  聯集。不要翻譯 GitHub manifest。
- 每個 GitHub 事件與動作組合最多對應「Events」中的一個 slug。
  動作是 slug 的一部分。沒有 slug 的組合在 Origin 上不是事件。
- 對於程式碼讀取的每個負載欄位，記錄它是否存在於
  負載中、存在於負載周圍的傳遞封包中（`event.type` 攜帶
  動作）、需要額外讀取（說明是哪個操作以及每個事件需要多少次呼叫）、可以從其他欄位計算得出，或缺失。負載是
  每個事件），可以從其他欄位計算得出，或是缺失。酬載是
  快照。REST 資源有但負載缺乏的欄位需要額外讀取。
  額外讀取。
- 檔案從未提及的功能目前不可用。向團隊詢問
  相關情況。檔案既未確認也未否認的行為，需提出問題，並在端到端測試中加入
  檢查該行為的步驟。不要假設它像
  在 GitHub 上那樣運作。

<a id="before-finishing"></a>

## 完成前

關於 Origin 的每個宣告都指向已儲存檔案中的某處。每個缺口
都有一個回饋專案，指明其成本。回饋不透露任何關於
團隊內部的情況。摘要需指明原生或映象（mirror）的問題。
