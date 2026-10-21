---
name: principle-migrate-callers-then-delete-legacy-apis
description: "在引入新的內部 API 時，若舊的呼叫者仍存在，應同時遷移呼叫者並刪除舊 API，而非保留相容層。"
disable-model-invocation: true
---

<a id="migrate-callers-then-delete-legacy-apis"></a>

# 遷移呼叫者後刪除舊版 API

- 更新測試以驗證新契約，並刪除僅保護重構前實作細節的測試

**適用情境：**
- 沒有外部使用者依賴向後相容
- 專案能承受協調的破壞性變更
- 新 API 屬於簡化或重構計畫的一部分
