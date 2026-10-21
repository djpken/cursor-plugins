<a id="databricks-analytics--system-tables"></a>

# Databricks Analytics 與系統表格

<a id="what-this-source-contains"></a>

## 此來源包含的內容

Databricks 是產品分析、資料管道與倉儲遙測層。它補足 Datadog。Datadog 是 *基礎設施/執行環境* 的視角，Databricks 是 *產品/資料* 的視角（使用者做了什麼、哪些實驗執行、功能使用如何演變、一個閾值常數來自哪裡）。

- **產品分析事件。** `your_warehouse.events.analytics_track_event`（原始）與在 `<your_analytics_db>.<schema>.<table>` 中型別化、逐事件去重後的 dbt 模型。使用者行為：功能呼叫、點選、接受/拒絕、提交、客戶報告的錯誤。
- **使用與計費事件。** `your_warehouse.events.usage_event` / `<your_analytics_db>.<schema>.stg_usage_events`、`your_warehouse.events.raw_model_event` / `<your_analytics_db>.<schema>.stg_raw_model_events`。用於成本或用量驅動的決策。
- **實驗 / 功能旗標資料。** 曝光與結果表格。**Schema 是公司特定。** 先用 `SHOW TABLES` 探查，不要先假定名稱。
- **系統表格。** `system.query.history`、`system.compute.warehouses`、`system.billing.*`、`system.access.audit`。回答「這個查詢是否昂貴？」、「這個多久被執行一次？」、「倉儲負載何時飆升？」
- **dbt 血統。** 在 `<your_analytics_db>.<schema>` 中的模型揭示哪些管道依賴一個表格/欄位。上游變動經常促使消費者程式碼變動。
- **Databricks 筆記本。** 工程師在程式碼變動前所做的探索性分析。**無法透過 SQL MCP 查詢。** 如果你懷疑理由在筆記本中，將其標為缺口。

<a id="how-to-search-it"></a>

## 如何搜尋

使用 Databricks SQL MCP。主要工具：`execute_sql_read_only`。如果它返回 `statement_id`，用 `poll_sql_result` 輪詢，而不是重新執行。

**先定向再查詢。** Schema 是公司特定。先探查，不要信任表格名稱：

```sql
SHOW TABLES IN <your_analytics_db>.<schema> LIKE '*<keyword>*';
DESCRIBE TABLE <your_analytics_db>.<schema>.stg_<event>;
```

**每個查詢都要時間限定。** 這些表格很大，無限制的掃描會逾時。以 `_timestamp`（事件）或 `start_time`（`system.query.history`）加上一個時間視窗，包住發布日期，通常約前後 30 天，只有強理由才更寬。

**優先使用型別化的 dbt 模型而非原始表格。** `<your_analytics_db>.<schema>.<table>` 是去重、型別化、liquid-clustered。`your_warehouse.events.analytics_track_event` 有重複與未型別化的 `properties_json`。模型名稱模式：`stg_<source>_<event_name_with_underscores>`，其中 `<source>` 是 `app`、`backend`、`website` 或 `cli`。當模式本身無法確定時，用 `SHOW TABLES` 確認確切模型名稱。只有在沒有 dbt 模型，或你需要 dbt 重新整理延遲期間的事件時，才降到原始表格。

**型別化 dbt 模型的欄位慣例**（知道這些避免 `DESCRIBE` 往返）：

- `_timestamp`、`_id`、`_auth_id`、`_request_id`、`event_name`。每個模型標準。
- `properties_<name>`。型別化、下劃線式的事件屬性（`properties_entrypoint`、`properties_size_bytes`、…）
- `context_team_id`、`context_client_version`、`context_country`、`context_client_os`。預先提取的客戶上下文

<a id="investigation-patterns-that-tend-to-pay-off"></a>

### 通常有成效的調查模式

選擇與目標匹配的表格 + 欄位組合：

1. **事件使用軌跡。** 在 PR 合併前後 ±30 天視窗內，對相關 `stg_*` 模型的每日計數。在合併後一兩天內從零到穩定量的階躍函式是強有力的間接證據，表明 PR 啟動了該功能。衰減到零則暗示棄用或刪除。
2. **護欄 / 防禦性檢查來源。** 在 PR *之前* 14 天內，相關 `properties_<name>` 欄位的分佈（中位數 / p99 / 最大值）。p99 與目標的閾值常數匹配則暗示該數字從資料中選出。
3. **實驗 / 功能旗標查詢。** `SHOW TABLES ... LIKE '*experiment*'` 找到曝光表格，然後在 PR 日期附近提取相關旗標鍵的各變體曝光計數。
4. **遷移、回填或效能重寫的查詢歷史證據。** `system.query.history` 以 `statement_text ILIKE '%<table_or_symbol>%'` 過濾，用緊密的 `start_time` 視窗，會浮現可能促使變動的昂貴查詢（按 `total_duration_ms` 排序或聚合 `SUM(read_bytes)`、`COUNT(*)`）。
5. **dbt 血統。** 如果目標讀取或寫入一個 `<your_analytics_db>.<schema>` 模型，該模型自身的 git 歷史（在此儲存庫中）經常包含理由。將這個線索交給 git 調查者，而不是自己追查。

<a id="what-good-evidence-looks-like-here"></a>

## 這裡的良好證據看起來像

超越上述模式形狀：

- 一個錯誤分類事件的計數在防禦性程式碼 PR 後幾天內降到接近零。暗示該 PR 解決了該錯誤類別
- 一個曝光表格行命名目標的功能旗標鍵，並在 PR 發布日期附近有「已發布」/「已結束」的決策

<a id="common-pitfalls"></a>

## 常見陷阱

- **被記錄 ≠ 被導致。** 一個事件的存在意味著有人足夠關心去記錄它，不代表目標程式碼 *因為* 它存在。與 git 調查者的 PR/commit 引用搭配，才能主張因果關係。
- **靜默的記錄變動。** 事件量的階躍函式可能意味著新事件開始被記錄，而不是使用者行為改變。在同一視窗檢查記錄 PR，不要將上升讀為功能發布訊號。
- **Schema 漂移。** 事件屬性會演變。今天型別化 dbt 模型上的欄位可能在目標寫入時不存在。較舊資料可能只在原始 `properties_json` 中攜帶該屬性。
- **dbt 重新整理延遲。** `<your_analytics_db>.<schema>.*` 按計劃重建（通常每小時/每天）。對於最近幾小時的事件，回退到 `your_warehouse.events.*` 並按 `_id` 去重。
- **公司特定表格。** 實驗、功能旗標、計費與使用表格各不相同。報告一個你從未確認存在的表格結果是典型失敗模式。先用 `SHOW TABLES` / `DESCRIBE TABLE` 探查。
- **保留懸崖。** 如果相關視窗早於表格的保留期或 dbt 模型的建立日期，那是 *缺口*，不是空結果。明確標出，讓合成器不會將「無結果」讀為「無活動」。
- **筆記本無法查詢。** SQL MCP 看不到 Databricks 筆記本。如果你懷疑理由在筆記本中，返回一個缺口。

<a id="what-to-return"></a>

## 要回傳的內容

對於每個相關發現：
- 型別（產品事件 / 實驗曝光 / 使用或計費事件 / 系統表格行 / dbt 模型）
- 完整表格名稱與你執行確切的查詢
- 查詢的時間視窗
- 簡潔的數字摘要（計數、百分位、首次/最後出現時間戳）。**不要傾倒原始行。**
- 與目標發布日期的時間相關（例如，「首行 2024-08-15，PR #49074 合併 2024-08-14」）
- 相關性與強度：直接 / 間接 / 弱
