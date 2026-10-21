<a id="sentry-error-history"></a>

# Sentry 錯誤歷史

<a id="how-to-search-it"></a>

## 如何搜尋

使用 Sentry MCP。

1. **定位。** 如果您不知道專案 slug 和組織：

   ```
   find_organizations
   find_projects
   ```

2. **搜尋與目標相關的問題。**

   ```
   search_issues (natural language, e.g., "errors in PaymentService timeout", "unhandled exceptions in uploadFile")
   ```

   從目標處理的例外類別名稱、其函式或類別名稱、其檢查的錯誤訊息字串，以及其檔案路徑來建立查詢。

3. **以發布版本和時間範圍縮小範圍。**

   ```
   search_issue_events (filter by release, time, environment, trace ID, tags)
   get_issue_tag_values (for an issue, see distribution across versions, users, environments)
   ```

   對於疑似問題，檢查**首次出現**和**最後出現**（停止時間是否與目標的發布日期吻合？）、**受影響的發布版本**（哪些版本出現此問題，哪個版本是修正？），以及**頻率趨勢**（是否先激增，然後解決？）。

4. **取得完整事件以獲得上下文。**

   ```
   get_sentry_resource (pass a Sentry URL or type+ID)
   ```

   堆疊追蹤是否經過目標程式碼？標籤和 breadcrumbs 是否符合目標防禦的條件？

5. **檢查在目標附近發布的版本。**

   ```
   find_releases (around the commit date of the target)
   ```

   將發布版本與 PR 的合併日期交叉參考。

6. **謹慎使用 Seer。**

   ```
   analyze_issue_with_seer
   ```

   Seer 的 AI 根因分析是假說生成器。將其視為推論，而非權威。實際事件和堆疊追蹤是主要證據。

<a id="what-to-return"></a>

## 回傳內容

對於每個相關問題：
- 問題 ID 和標題
- 專案和組織
- 首次出現 / 最後出現時間戳
- 事件數量（若已知則包含抽樣率）
- 受影響的發布版本
- 顯示與目標相關性的代表性堆疊追蹤片段（逐字摘錄，而非摘要）
- 首次/最後出現與目標發布日期的相關性
- 問題連結
- 任何作者評論或解決說明
