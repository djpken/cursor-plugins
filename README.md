> [English](https://github.com/djpken/cursor-plugins/blob/55d4101a400123c4bc8a60c8c10d7ebe419e454b/README.md) · [中文文件索引](./TRANSLATIONS.md)

<a id="cursor-plugins"></a>

# Cursor 外掛

適用於熱門開發工具、框架和 SaaS 產品的官方 Cursor 外掛。每個外掛都是儲存庫根目錄下的一個獨立資料夾，並擁有自己的 `.cursor-plugin/plugin.json` 清單。

<a id="plugins"></a>

## 外掛

| `name` | 外掛 | 作者 | 類別 | `description` (來自市集) |
|:-------|:-------|:-------|:---------|:-------------------------------------|
| `teaching` | [Teaching](teaching/) | Cursor | 工具 | 技能對映、練習計畫和學習回顧。 |
| `continual-learning` | [Continual Learning](continual-learning/) | Eric Zakariasson | 開發工具 | 僅使用高訊號專案符號，為 AGENTS.md 進行增量轉錄驅動的記憶更新。 |
| `cursor-team-kit` | [Cursor Team Kit](cursor-team-kit/) | Eric Zakariasson | 開發工具 | 用於 CI、程式碼審查、發布、本機自動化和驗證的內部團隊工作流程。 |
| `thermos` | [Thermos](thermos/) | Cursor | 開發工具 | 熱核分支審查：深入的安全性與正確性稽核、嚴格的程式碼品質評分標準、平行子代理、thermos 編排，以及可選的合併就緒 PR 流程。 |
| `create-plugin` | [Create Plugin](create-plugin/) | Cursor | 開發工具 | 建立並驗證新的 agent 外掛。 |
| `ralph-loop` | [Ralph Loop](ralph-loop/) | Cursor | 開發工具 | 使用 Ralph Wiggum 技術進行迭代式自我參考 AI 迴圈。 |
| `agent-compatibility` | [Agent Compatibility](agent-compatibility/) | Cursor | 開發工具 | 由 CLI 支援的儲存庫相容性掃描，以及稽核啟動、驗證和檔案是否符合實際情況的 agent。 |
| `cli-for-agent` | [CLI for Agents](cli-for-agent/) | Eric Zakariasson | 開發工具 | 設計 coding agent 可可靠執行的 CLI 的模式：旗標、含範例的說明、管線、錯誤、冪等性、預演。 |
| `pr-review-canvas` | [PR Review Canvas](pr-review-canvas/) | Cursor | 開發工具 | 將 PR 差異渲染為按重要性分組的審查畫布。 |
| `docs-canvas` | [Docs Canvas](docs-canvas/) | Cursor | 開發工具 | 將檔案渲染為可導航的畫布。 |
| `cursor-sdk` | [Cursor SDK](cursor-sdk/) | Cursor | 開發工具 | 使用 TypeScript SDK 建置應用程式、指令碼和自動化。 |
| `origin-apps` | [Origin Apps](origin-apps/) | Cursor | 開發工具 | 用於在 Cursor Origin API 上建置的技能。先取得即時規格，然後遵循 Origin 規則處理憑證、範圍、webhooks、分頁和錯誤。包含一個規劃現有 GitHub App 移植的技能。 |
| `orchestrate` | [Orchestrate](orchestrate/) | Cursor | 開發工具 | 透過規劃者、工作者、驗證者和結構化交接，將大型任務分散到平行的雲端 agent。 |
| `pstack` | [pstack](pstack/) | Lauren Tan | 開發工具 | 如果你想快速前進，請先深入。pstack 幫助你寫更少但品質更高的程式碼。嚴謹的 agent 工作流程，你可以放心地平行化。 |
| `dyl-stack` | [dyl-stack](dyl-stack/) | Dylan Gattey | 開發工具 | 建立在 pstack 之上的 Dylan 的 agent 風格：優先處理根本原因而非症狀補丁，設計前先執行 The Algorithm，簡潔且經驗證的交付，適合貼上即用的 PR 審查，以及帶有視覺裁判的 Figma 到 UI 轉換。 |
| `advisor` | [Advisor](advisor/) | Cursor | 開發工具 | 在重大決策前、卡住時以及宣告完成前，諮詢更強的模型。 |
| `grok-voice` | [Grok Voice](grok-voice/) | Eric Zakariasson | 開發工具 | 為應用程式新增 Grok 語音：即時語音對語音、語音轉文字聽寫、文字轉語音朗讀，以及用於語音會話的紀錄驅動修復迴圈。 |
| `gmail` | [Gmail](third_party/gmail/) | Cursor | 生產力 | 搜尋、閱讀、起草和管理電子郵件。 |
| `google-drive` | [Google Drive](third_party/google-drive/) | Cursor | 生產力 | 搜尋、閱讀、建立和分享檔案。 |
| `google-calendar` | [Google Calendar](third_party/google-calendar/) | Cursor | 生產力 | 搜尋事件和排程會議。 |
| `google-docs` | [Google Docs](third_party/google-docs/) | Cursor | 生產力 | 閱讀、建立和編輯檔案。 |
| `google-sheets` | [Google Sheets](third_party/google-sheets/) | Cursor | 生產力 | 閱讀、寫入和附加試算表資料。 |
| `google-slides` | [Google Slides](third_party/google-slides/) | Cursor | 生產力 | 建立、編輯和渲染簡報。 |
| `google-ads` | [Google Ads](third_party/google-ads/) | Cursor | 整合 | 報告活動、研究關鍵字並套用已預覽的變更。 |
| `gong` | [Gong](third_party/gong/) | Cursor | 整合 | 取得帳戶摘要、交易洞察和通話簡報。 |
| `salesforce` | [Salesforce](third_party/salesforce/) | Cursor | 整合 | 查詢、建立和更新組織中的記錄。 |
| `salesforce-headless-360` | [Salesforce (Headless 360, Beta)](third_party/salesforce-headless-360/) | Cursor | 整合 | 跨 Salesforce 管理記錄、使用者和組織設定。 |
| `playwright` | [Playwright](third_party/playwright/) | Cursor | 整合 | 在真實瀏覽器中導航、點選、截圖和測試。 |
| `github` | [GitHub](third_party/github/) | Cursor | 整合 | 管理儲存庫、問題、拉取請求和 Actions。 |
| `ashby` | [Ashby](third_party/ashby/) | Cursor | 整合 | 搜尋候選人、準備面試和管理管線任務。 |
| `hubspot` | [HubSpot](third_party/hubspot/) | Cursor | 整合 | 搜尋和更新聯絡人、公司、交易和工單。 |
| `intercom` | [Intercom](third_party/intercom/) | Cursor | 整合 | 搜尋對話、聯絡人和幫助中心文章。 |
| `zoom` | [Zoom](third_party/zoom/) | Cursor | 整合 | 搜尋會議、取得轉錄稿並處理 Zoom Docs。 |
| `x` | [X](third_party/x/) | Cursor | 整合 | 搜尋貼文、閱讀時間軸、取得趨勢、探索 Spaces 和社群、管理清單和書籤、存取 X Chat、排程 X 通話並管理直播。 |
| `clay` | [Clay](third_party/clay/) | Cursor | 整合 | 豐富個人和公司資料，執行 AI 研究 agent。 |
| `circleback` | [Circleback](third_party/circleback/) | Cursor | 整合 | 搜尋會議、轉錄稿、行動專案和電子郵件。 |
| `docusign` | [Docusign](third_party/docusign/) | Cursor | 整合 | 管理信封、範本、工作流程和協議。 |
| `navan` | [Navan](third_party/navan/) | Cursor | 整合 | 查詢費用、差旅預訂、政策和卡片。 |
| `profound` | [Profound](third_party/profound/) | Cursor | 整合 | 追蹤 AI 能見度、情緒和引用。 |
| `juicebox` | [Juicebox](third_party/juicebox/) | Cursor | 整合 | 查詢招聘分析、短名單和來源 agent。 |
| `outreach` | [Outreach](third_party/outreach/) | Cursor | 整合 | 搜尋序列、潛在客戶和 Kaia 會議。 |
| `amplemarket` | [Amplemarket](third_party/amplemarket/) | Cursor | 整合 | 搜尋個人和公司、豐富潛在客戶、執行序列。 |
| `klaviyo` | [Klaviyo](third_party/klaviyo/) | Cursor | 整合 | 管理個人檔案、區段、行銷活動與流程。 |
| `customer-io` | [Customer.io](third_party/customer-io/) | Cursor | 整合 | 建立行銷活動、管理區段並查詢人員。 |
| `mailerlite` | [MailerLite](third_party/mailerlite/) | Cursor | 整合 | 管理訂閱者、群組、行銷活動與自動化流程。 |
| `brevo` | [Brevo](third_party/brevo/) | Cursor | 整合 | 管理聯絡人、電子郵件與簡訊行銷活動，以及 CRM 交易。 |
| `typeform` | [Typeform](third_party/typeform/) | Cursor | 整合 | 建立表單、分析回應並管理聯絡人。 |
| `jotform` | [Jotform](third_party/jotform/) | Cursor | 整合 | 建立與編輯表單，然後讀取提交內容。 |
| `semrush` | [Semrush](third_party/semrush/) | Cursor | 整合 | 研究關鍵字、反向連結、流量與競爭對手。 |
| `ahrefs` | [Ahrefs](third_party/ahrefs/) | Cursor | 整合 | 研究關鍵字、反向連結、排名與網站健康狀況。 |
| `godaddy` | [GoDaddy](third_party/godaddy/) | Cursor | 整合 | 構思網域名稱並檢查可用性。 |
| `upwork` | [Upwork](third_party/upwork/) | Cursor | 整合 | 搜尋人才、發布工作並管理合約。 |
| `workable` | [Workable](third_party/workable/) | Cursor | 整合 | 搜尋候選人、移動招聘管道並管理人事記錄。 |
| `brex` | [Brex](third_party/brex/) | Cursor | 整合 | 查詢費用、收據、帳單、卡片與差旅。 |
| `todoist` | [Todoist](third_party/todoist/) | Cursor | 整合 | 建立、尋找並完成任務與專案。 |
| `calendly` | [Calendly](third_party/calendly/) | Cursor | 整合 | 檢查空閒時間並預約、取消或重新排程。 |
| `smartsheet` | [Smartsheet](third_party/smartsheet/) | Cursor | 整合 | 查詢與更新工作表、列與工作區。 |
| `wrike` | [Wrike](third_party/wrike/) | Cursor | 整合 | 搜尋專案、建立任務並發布留言。 |
| `coda` | [Coda](third_party/coda/) | Cursor | 整合 | 搜尋檔案、讀取頁面並更新表格。 |
| `guru` | [Guru](third_party/guru/) | Cursor | 整合 | 搜尋公司知識並起草已驗證的答案。 |
| `fireflies` | [Fireflies](third_party/fireflies/) | Cursor | 整合 | 搜尋會議逐字稿、摘要與行動專案。 |
| `otter` | [Otter.ai](third_party/otter/) | Cursor | 整合 | 搜尋會議歷史並提取完整逐字稿。 |
| `fathom` | [Fathom](third_party/fathom/) | Cursor | 整合 | 搜尋會議並提取逐字稿與摘要。 |
| `craft` | [Craft](third_party/craft/) | Cursor | 整合 | 搜尋、建立與更新檔案及每日筆記。 |
| `mem` | [Mem](third_party/mem/) | Cursor | 整合 | 捕捉、搜尋並整理筆記與收藏。 |
| `readwise` | [Readwise](third_party/readwise/) | Cursor | 整合 | 搜尋標記與 Reader 檔案，儲存文章。 |
| `similarweb` | [Similarweb](third_party/similarweb/) | Cursor | 整合 | 分析網站流量、受眾與競爭對手。 |
| `xero` | [Xero](third_party/xero/) | Cursor | 整合 | 讀取與寫入發票、聯絡人、報告與薪資。 |
| `x-ads` | [X Ads](third_party/x-ads/) | Cursor | 整合 | 管理廣告活動、建立廣告、追蹤轉換並提取效能統計。 |
| `meta-ads` | [Meta Ads](third_party/meta-ads/) | Cursor | 整合 | 管理行銷活動、廣告、受眾與目錄，並提取效能洞察。 |
| `attio` | [Attio](third_party/attio/) | Cursor | 整合 | 搜尋與更新 CRM 記錄、清單、筆記與任務。 |
| `hunter` | [Hunter](third_party/hunter/) | Cursor | 整合 | 尋找並驗證電子郵件、發現公司並儲存潛在客戶。 |
| `gamma` | [Gamma](third_party/gamma/) | Cursor | 整合 | 產生簡報、檔案與網頁。 |
| `teams` | [Teams](third_party/teams/) | Cursor | 生產力 | 搜尋、讀取與傳送 Microsoft Teams 聊天與頻道訊息。 |
| `sharepoint` | [SharePoint](third_party/sharepoint/) | Cursor | 生產力 | 搜尋與讀取 Microsoft SharePoint 網站、檔案庫、檔案與清單。 |
| `finance` | [Finance](third_party/finance/) | Cursor | 整合 | 安全地連結您的帳戶，讓 Grok 協助回答關於您的支出、訂閱、餘額與投資的問題。 |
| `webull` | [Webull](third_party/webull/) | Cursor | 整合 | 檢視帳戶、持倉、訂單、觀察清單與市場資料。 |
| `sp-global` | [S&P Global](third_party/sp-global/) | Cursor | 整合 | 查詢 S&P Capital IQ 財務、價格與逐字稿。 |
| `interactive-brokers` | [Interactive Brokers](third_party/interactive-brokers/) | Cursor | 整合 | 檢視持倉、餘額、損益並起草交易指示。 |
| `meltwater` | [Meltwater](third_party/meltwater/) | Cursor | 整合 | 搜尋媒體與社群提及並提取分析。 |
| `daloopa` | [Daloopa](third_party/daloopa/) | Cursor | 整合 | 提取連結來源的基本面、關鍵績效指標、申報檔案與價格。 |
| `excalidraw` | [Excalidraw](third_party/excalidraw/) | Cursor | 整合 | 從聊天中繪製並匯出手繪圖表。 |
| `google-cloud-bigquery` | [Google Cloud BigQuery](third_party/google-cloud-bigquery/) | Cursor | 整合 | 探索資料集與表格並執行 SQL 查詢。 |
| `statsig` | [Statsig](third_party/statsig/) | Cursor | 整合 | 檢視與管理功能閘門、實驗、動態設定與指標。 |
| `robinhood` | [Robinhood](third_party/robinhood/) | Cursor | 整合 | 檢視投資組合、持倉、訂單、觀察清單與市場資料，並在 Robinhood Agentic 帳戶中進行交易。 |
| `coinbase` | [Coinbase](third_party/coinbase/) | Cursor | 整合 | 檢查餘額、取得報價並預覽或下單交易。 |
| `etoro-trading` | [eToro Trading](third_party/etoro-trading/) | Cursor | 整合 | 檢視您的 eToro 投資組合、餘額、持倉與觀察清單，研究金融工具與交易者，並準備與下單交易。 |
| `x-money` | [X Money](third_party/x-money/) | Cursor | 整合 | 使用您的 X Money Card，向 X 上的使用者匯款，管理您的財務，檢視您的餘額並瀏覽交易歷史。 |
| `shopify-store` | [Shopify](third_party/shopify-store/) | Cursor | 整合 | 連結您的 Shopify 商店，讓 Grok 回答關於產品、訂單、客戶、庫存與銷售的問題。 |
| `quickbooks-online` | [QuickBooks Online](third_party/quickbooks-online/) | Cursor | 整合 | 讀取發票、帳單、費用、客戶、帳戶與財務報告。 |
| `workday` | [Workday](third_party/workday/) | Cursor | 整合 | 在 Workday 中查詢員工、組織、休假、薪資與招聘資料。 |
| `whatsapp-business` | [WhatsApp Business](third_party/whatsapp-business/) | Cursor | 整合 | 在 WhatsApp Business 中傳送訊息並管理範本、聯絡人與電話號碼。 |
| `zoho-crm` | [Zoho CRM](third_party/zoho-crm/) | Cursor | 整合 | 處理 Zoho CRM 中的潛在客戶、聯絡人、帳戶、交易及其他記錄。 |
| `zoho-desk` | [Zoho Desk](third_party/zoho-desk/) | Cursor | 整合 | 處理 Zoho Desk 中的工單、聯絡人、任務與知識庫文章。 |
| `zoho-books` | [Zoho Books](third_party/zoho-books/) | Cursor | 整合 | 處理 Zoho Books 中的聯絡人、發票、報價單與專案。 |
| `square` | [Square](third_party/square/) | Cursor | 整合 | 處理付款、訂單、目錄、客戶與發票。 |
| `tailscale` | [Tailscale](third_party/tailscale/) | Cursor | 整合 | 讓 Grok Bot 存取您私人 Tailscale 網路上的 MCP 伺服器。在您自己的 tailnet 中，它會以一個 tag:grokbot 裝置加入；在您團隊的公司 tailnet 中，若由團隊管理員開啟，則會使用每位成員自己的裝置。 |
| `highlevel` | [HighLevel](third_party/highlevel/) | Cursor | 整合 | 處理聯絡人、對話、商機、行事曆與付款。 |
| `finary` | [Finary](third_party/finary/) | Cursor | 整合 | 檢視您的淨資產、帳戶、持倉、支出與預算。 |
| `tiktok-for-business` | [TikTok for Business](third_party/tiktok-for-business/) | Cursor | 整合 | 管理 TikTok 廣告活動、廣告群組、廣告、受眾、創意素材與報告。 |
| `gusto` | [Gusto](third_party/gusto/) | Cursor | 整合 | 處理薪資、員工、承包商、休假與報告。 |
| `gopuff` | [Gopuff](third_party/gopuff/) | Cursor | 整合 | 透過 Gopuff 的官方 MCP 伺服器，從 Grok Bot 在 Gopuff 購物。使用您的 Gopuff 帳戶登入；Gopuff 會處理登入流程，您的搜尋、購物車與訂單將透過該帳戶傳送至 Gopuff，因此您下的任何訂單都會像一般 Gopuff 訂單一樣計費與配送。 |
| `jobber` | [Jobber](third_party/jobber/) | Cursor | 整合 | 處理客戶、請求、報價單、工作、拜訪與發票。 |
作者值與每個 plugin 的 `plugin.json` `author.name` 相符（Cursor 在 manifest 中列出 `plugins@cursor.com`）。

<a id="repository-structure"></a>

## 儲存庫結構

這是一個多 plugin 市集儲存庫。根目錄的 `.cursor-plugin/marketplace.json` 列出所有 plugin，且每個 plugin 都有各自的 manifest：

```
plugins/
├── .cursor-plugin/
│   └── marketplace.json       # Marketplace manifest (lists all plugins)
├── plugin-name/
│   ├── .cursor-plugin/
│   │   └── plugin.json        # Per-plugin manifest
│   ├── skills/                # Agent skills (SKILL.md with frontmatter)
│   ├── rules/                 # Cursor rules (.mdc files)
│   ├── mcp.json               # MCP server definitions
│   ├── README.md
│   ├── CHANGELOG.md
│   └── LICENSE
└── ...
```

<a id="license"></a>

## 授權條款

MIT
