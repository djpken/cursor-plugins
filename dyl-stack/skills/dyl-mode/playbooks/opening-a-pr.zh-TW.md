<a id="opening-a-pr-dylan-overlay"></a>

### 開啟 PR（Dylan 覆蓋層）

先閱讀共用操作手冊：pstack `poteto-mode/playbooks/opening-a-pr.md`。

然後套用這些閘門。發生衝突時，這些規則優先。

1. 使用儲存庫的 PR forge。若儲存庫位於 Origin，則使用 `origin pr`；否則使用 `gh pr`。切勿在同一個分支上同時開啟兩者。
2. 若這是後續工作，請更新現有的 PR。不要開啟重複的 PR。
3. 除非人類在此輪次中明確授權，否則絕不合併或啟用自動合併。
4. 開啟 PR 不會啟動監控。發布 URL 後即停止，除非另有要求。
5. 不要提交暫存產物（暫存目錄、稽核輸出、本機截圖、一次性指令碼）。
