<a id="datadog-telemetry"></a>

# Datadog 遙測

<a id="what-this-source-contains"></a>

## 此來源包含的內容

Datadog 持有執行記錄，也就是生產環境中實際發生的事件，而非計畫或討論中的內容。

- **指標。** 團隊所設定的計數器、量測儀、歷史圖。指標的*存在*本身就是證據，顯示有人認為該數字值得關注。
- **監控與警示。** 團隊決定足以喚醒某人注意的條件。監控在 `rate_limit_hit > 10/min` 上觸發，是團隊對該閾值感到擔憂的直接證據。
- **儀錶板。** 團隊認為對子系統重要事項的精心檢視畫面。
- **APM 追蹤與跨度。** 請求層級的執行資料，適用於「為何這裡慢」或「為何這裡有逾時」的問題。
- **紀錄。** 高量事件記錄，常包含促使防禦性程式碼的錯誤條件。
- **事件。** 正式的事件記錄，包含時間軸與連結的後續檢討報告。
- **筆記。** 團隊的探索性調查，常包含假說與分析。

<a id="how-to-search-it"></a>

## 如何搜尋

使用 Datadog MCP。先廣泛探索，再逐步縮窄。

1. **識別負責的服務。**

   ```
   search_datadog_services (filter by name or team)
   search_datadog_service_dependencies (see upstream/downstream)
   ```

2. **先檢視儀錶板與監控。**

   ```
   search_datadog_dashboards (query: feature name, service name, symbol)
   search_datadog_monitors   (same queries)
   ```

   當儀錶板或監控涵蓋目標時，記錄其查詢與監看的閾值。閾值常是「為何這裡限制為 N？」的答案。

3. **目標周圍的指標。**

   ```
   search_datadog_metrics (by name pattern, e.g., the feature or symbol)
   get_datadog_metric_context (metadata: description, units, tags)
   get_datadog_metric (timeseries; "was there a spike around the PR date?")
   ```

   將指標的趨勢與目標的新增/變更日期進行關聯，是強有力的支援證據：「`payment_timeout` 指標在 2023-11-03 飆升，而重試邏輯在 2023-11-06 合入。」

4. **紀錄。縮窄範圍，不要傾倒。**

   ```
   search_datadog_logs (raw log patterns near the target, set use_log_patterns=true)
   analyze_datadog_logs (SQL-style aggregations, only when you need counts)
   ```

   使用符號、錯誤字串或功能名稱進行搜尋。**強烈偏好時間限定查詢**（例如，變更前後各 30 天）。

5. **APM 跨度與追蹤。**

   ```
   aggregate_spans    (stats: "how often does this endpoint fail?")
   search_datadog_spans (inspect individual spans)
   get_datadog_trace  (a specific trace ID)
   ```

   適用於逾時、重試、慢速路徑與跨服務行為。

6. **事件。**

   ```
   search_datadog_incidents (by title, team, date range)
   get_datadog_incident     (full detail for a specific incident)
   ```

   若目標看起來是防禦性的，搜尋該目標新增時間附近的事件。事件時間軸包含「新增防禦性檢查 X」是近乎直接的證據。

<a id="what-good-evidence-looks-like-here"></a>

## 這裡的良好證據看起來像

- 監控的查詢與閾值符合程式碼所強制的限制（程式碼限制為 100，監控在請求超過 100/分鐘時警示）
- 目標作者建立的儀錶板，其元件與程式碼所測量或防護的內容相符
- 指標顯示生產環境在程式碼合入前立即飆升，且合入後穩定
- 事件記錄引用目標程式碼、相同符號或相同錯誤字串
- 紀錄顯示防禦性程式碼會防止的特定錯誤模式，時間戳落在變更前的視窗內

<a id="common-pitfalls"></a>

## 常見陷阱

- **關聯不代表因果。** PR 前的飆升與 PR 後的穩定是暗示性的，而非確定性的，因為同一視窗內可能有其他變更。請檢查鄰近的 PR。
- **對找到的圖表過度擬合。** 圖表反映其製作者的框架。名為「重試成功率」的圖表是團隊關注重試成功的證據，而非特定程式碼行存在的原因。
- **消失的遙測。** 指標可能被重新命名、刪除或僅有短期保留。若無法找到相關視窗的資料，這是缺口，而非空結果。
- **規模下的噪音。** 積極縮窄紀錄搜尋的服務、標籤與時間視窗。使用 `analyze_datadog_logs` 進行聚合，而非傾倒原始紀錄。
- **被測量不等於成因。** 指標顯示有人認為某事值得測量，不代表程式碼是因它而新增。請與 commit/PR 日期交叉參考。

<a id="what-to-return"></a>

## 要回傳的內容

對於每個相關專案：
- 型別（儀錶板 / 監控 / 指標 / 紀錄模式 / 追蹤 / 事件 / 筆記）
- 標題或名稱
- 連結或識別碼（儀錶板 ID、監控 ID、指標名稱、事件 ID）
- 擁有者/作者與建立/修改日期
- 與問題相關的特定條件、查詢或引用（儘可能逐字）
- 相關性：這對目標程式碼的暗示，以及連結的強度
