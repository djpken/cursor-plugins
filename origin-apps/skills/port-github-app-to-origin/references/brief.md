<a id="the-brief-and-the-feedback"></a>

# 簡報與回饋

<a id="the-brief"></a>

## 簡報

在儲存庫根目錄撰寫一個 Markdown 檔案（`ORIGIN-PORTING-BRIEF.md`
除非團隊以不同方式命名檔案），並印出其路徑。小型應用程式的目標字數約為
800 字，大型應用程式約為 2,000 字。省略任何沒有內容可說的部分。選擇適合該應用程式的表格格式。一份好的簡報應：
以摘要開頭。包含結論（直接移植、需變通方案移植，或受阻於 X）、決定後續內容的關鍵問題（通常是儲存庫為原生或映象），以及是否有給 Cursor 的回饋，若有，是否阻礙移植。

- 僅列出無法直接移植的功能。針對每一項，列出其對應的 Origin 操作、事件 slug 或檔案章節，或註明無對應專案。以一行總結其餘專案，例如「14 個操作可直接對應；請參閱 scopes 行」。僅在 webhook payload 欄位缺失或需要額外 API 呼叫時才顯示該欄位。以需要請求的 scopes 結尾。
  若有助於說明，描述此應用程式的端到端測試。應選擇哪些事件、如何檢查儲存庫的映象狀態、應到達的第一個事件及其應包含的內容、第一次寫入操作。略過通用設定；檔案的「Implementation checklist」已涵蓋此部分。
  說明使移植規模變大或變小的因素，以及如何推出（並行執行兩個版本，或切換）。不要提供時間估算。
  僅提出團隊必須決定的問題。不要將發現轉化為問題。
- 僅列出無法直接沿用之功能。針對每一項，
  其對應的 Origin 操作、事件 slug 或檔案章節，或是一則註記，
  給 Cursor 的回饋
  Cursor 希望瞭解團隊的需求。提出任何阻礙團隊主要流程、影響其正確性、安全性或規模，或團隊希望 Origin 執行的事項。下方的測試僅將專案分類為回饋（Origin 應新增的功能）和問題（團隊必須做出的決定）。它們從不決定是否要提出意見。
  變通方案是以其他方式達到相同結果，例如額外讀取、不同的識別碼、變更的路徑、在使用者端過濾，或由應用程式控制的標記。這通常是正確的答案。當它導致以下任一成本時，即成為缺口，並需建立回饋專案：
  扇出（fan-out），即每個事件的額外呼叫，且隨此應用程式的儲存庫或活動規模增長；
- 可能錯誤的答案，例如猜測哪個 check run 或評論屬於該應用程式、從多個版本共用的 SHA 推斷 pull request，或建構檔案未保證其格式的 URL；
  更廣泛的 scope、更長生命週期的 token，或在 installation token 足夠的情況下使用使用者憑證；
  對團隊使用者所見或可執行事項的變更；
  應用程式主要流程或其第一個端到端測試所依賴的功能。
- 應用程式存在以回應的狀態變更，若沒有對應事件且無其他偵測方式，也屬於缺口。檔案從未提及的事專案前不可用，應提出問題；僅當它阻礙主要流程時才成為回饋。
  不屬於回饋，僅為問題或註記：程式碼未使用的欄位或過濾器；有機械替代方案但慣例不同的專案；檔案記載的設計選擇，例如 token 生命週期或無 GraphQL。
- 僅提出團隊必須決策的問題。不要將發現轉化為
  描述該功能。不要建議 scope、欄位或路由名稱。由團隊傳送回饋，而非你，且他們會先移除任何揭露內部資訊的內容。
- 以兩行結尾，說明哪個規格版本（`info.version` 與 fetch
  在將任何事項稱為缺口之前，請先檢查此清單，然後在已儲存的檔案中確認。此清單會過時；以檔案為準。
  Cursor 作為最後一節，不編號。

所有關於該應用程式的陳述，皆須以 `file:line` 為據，或以「from
`<dependency>`」為據（當函式庫代為應用程式執行時）。所有關於
Origin 的陳述，皆須以已儲存的檔案內容為據。若希望在
表格中使用簡短標籤，請使用直白用語：可直接使用、替代方案（說明成本）、無法使用
behalf of users"（使用者確認收據、installation 使用者 token）。

<a id="feedback-for-cursor"></a>

## GitHub 映象：installation 只能讀取中繼資料和內容，合併 pull request 或變更預設分支不可用。

所有寫入操作都需要原生儲存庫，即在 Origin 上建立的儲存庫（"Mirrored repositories"）。
目前規格中沒有（詢問團隊；僅當阻礙主要流程時才作為回饋）：GraphQL（將每個查詢拆解為 REST 呼叫）；Issues（pull request 評論、執行緒、reviews 和標籤涵蓋 pull request 部分）；OAuth-app token 鑄造；寫入任意 blobs 或 trees（存在 commit-from-files 和 pushes）；查詢使用者、電子郵件、團隊或成員（reviewer 識別碼透過公開 id、電子郵件或群組 slug 解析）；讀取群組成員資格或使用者的有效許可權。對於最後兩項，應命名的變通方案是限制於特定儲存庫和 scopes 的使用者 token。
希望 Origin 執行的事項。下列測試僅將專案分類為回饋（
Origin 應新增的功能）與問題（團隊必須做出的決策）。
它們絕不決定是否要提出意見。

替代方案是以其他方式達成相同結果，例如額外讀取、
不同的識別碼、變更的路徑、在使用者端進行篩選，或
由應用程式控制的標記。這通常是正確的答案。當它產生以下任一成本時，便成為缺口，並
獲得一則回饋專案：

- 扇出（fan-out），即每個事件產生額外呼叫，且隨該應用程式的儲存庫或
  活動規模而增加；
- 可能錯誤的答案，例如猜測哪個檢查執行或評論屬於
  該應用程式本身、從多個版本共用的 SHA 推斷 pull request，或建構一個檔案未保證其格式
  的 URL；
- 更廣泛的範圍、更長生命週期的 token，或在
  安裝 token 已足夠的情況下使用使用者憑證；
- 對團隊使用者所見或可執行事項的變更；
- 應用程式主要流程或其第一個端到端測試所依賴的功能。

應用程式存在以回應的狀態變更，若沒有對應事件且無其他
方式察覺，也屬於缺口。檔案從未提及的事項，目前
無法使用，並獲得一則問題；僅當它阻礙
主要流程時，才成為回饋。

不屬於回饋，僅為問題或註記：程式碼未使用的欄位或篩選器；
有差異但存在機械式替代方案的慣例；檔案記載的
設計選擇，例如 token 生命週期或不支援 GraphQL。

每個缺口撰寫一則專案，使用 Origin 術語，且不包含任何揭露
團隊內部資訊的內容。若至少有一則專案，也請將該章節
寫入 `ORIGIN-FEEDBACK.md`，置於簡報旁邊。若無專案，則不寫入任何檔案
並以一行說明。該檔案不包含授權標頭、儲存庫名稱、
產品名稱，或提及任何其他 forge。若檔案與觀察到的
行為不一致，請將其列入回饋結尾的簡短「給 Cursor 的檔案問題」清單，
而非團隊的問題中。建議格式：

```markdown
### Feedback: <capability, in Origin terms>
- **Use case:** the app needs to <do what, for whom>, <how often or at what volume>.
- **Origin today:** <what is missing or costly; cite the closest operationId, slug, or section, or "no operation">.
- **Workaround considered:** <the route and the cost that makes it insufficient, or "none found">.
- **Blocking?** yes / no, for which flow.
- **Spec version checked:** <info.version>, <date>.
```

描述該功能。不要建議範圍、欄位或路由名稱。
由團隊傳送回饋，而非你，且他們會先移除任何揭露
內部資訊的內容。

<a id="where-github-features-live-on-origin"></a>

## GitHub 功能在 Origin 上的對應位置

在將任何事項視為缺口之前，請先檢查此清單，再於已儲存的檔案中確認。
此清單可能過時；以檔案為準。

具有 Origin 對應項：安裝回呼引數 → "Installation
receipt"；RS256 app JWT → "App JWT"；長生命週期安裝 token →
"Installation access token"；許可權 → "Scopes" 與 `x-origin-scopes`；
數字 ID 與 `/repositories/{id}` → "IDs"、"Repository paths"；`Link`
分頁與總數 → "分頁"；commit 狀態 → 檢查執行
使用穩定的 `key`（"檢查執行"）；`/issues/{n}/comments` 以及
`/issues/{n}/labels` → pull request 端點；
儲存庫 webhook CRUD → 在建立 App 與更新 App 頁面上的 `events` 清單
App；一個 `pull_request` 事件搭配一個 `action` 欄位 → 每個動作對應一個 slug
（"Events"）；`x-github-*` 標頭與 HMAC 簽章 → "Headers"、"Signature
verification"；GitHub 在 payload 中內嵌的資料（變更檔案、before-SHA、
URL、使用者檔案）→ 額外讀取（"Resource references"、"Current
limitations"）；以 commit SHA 為鍵的審查 → `pullRequestVersion`；透過作者找出
應用程式本身的檢查執行或評論 → 檢查執行的 `key`，或
由應用程式控制的標記；使用者登入與代表使用者操作 → "Acting on
behalf of users"（使用者確認回執、安裝使用者 token）。

GitHub 映象：安裝僅能讀取中繼資料與內容，且
合併 pull request 或變更預設分支無法使用。
所有寫入操作都需要原生儲存庫，即在 Origin 上建立的儲存庫（"Mirrored
repositories"）。

目前規格中未包含（詢問團隊；僅當阻礙主要
流程時才視為回饋）：GraphQL（將每個查詢拆解為 REST 呼叫）；Issues（pull request
評論、討論串、審查與標籤涵蓋 pull request 部分）；
OAuth-app token 鑄造；寫入任意 blob 或 tree（commit-from-files
與 push 已存在）；查詢使用者、電子郵件、團隊或成員（審查者
識別碼透過公開 id、電子郵件或群組 slug 解析）；讀取群組
成員資格或使用者的有效許可權。對於最後兩項，
應命名的替代方案是限定於特定儲存庫與範圍的使用者 token。
鑄造該 token 時，若使用者不具備該許可權，則回傳 `403`，因此它
同時可作為許可權檢查。
