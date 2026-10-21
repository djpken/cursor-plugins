---
name: no-comments
description: "生成 Comment Sicko，修正已接受的發現，並為所聲稱的限制提供編碼方案。"
disable-model-invocation: true
---

<a id="no-comments"></a>

# 無評論

生成 Comment Sicko。處理已接受的發現。

交由 Comment Sicko 的新觀點處理。

<a id="scope"></a>

## 範圍

使用呼叫者的檔案或差異。否則使用當前對基礎分支的差異，預設為 `main`，包括工作樹。

<a id="steps"></a>

## 步驟

1. 生成 `Task` 並使用 `subagent_type: "comment-sicko"`。傳入範圍。根據 **poteto-mode** 技能的 Subagents 部分，為每個子代理選擇推理等級和快速或標準速度。不要重述其規則。
2. 檢查其報告和差異。拒絕應用程式碼編輯、範圍外擴充套件、例外保護的刪除、錯誤表述的 `MUST KILL` 理由，以及將保留的故意程式碼視為有罪的標記。將對我們程式碼的意外標記重新調整為可行動的。不要恢復那些評論。保留僅在證明其涉及我們無法更改的事物時才有效。審核未處理的範圍內 lint 和 TypeScript 抑制。正確性或安全性抑制保持為可行動的 `MUST KILL`。僅在確切的例外和範圍內證明下恢復刪除。在接受薄弱的 `IMPORTANT` 或 `do not remove` 刪除或保留之前，對其符號執行 `/how` 或 `/why`。如果刪除是模糊的，不要恢復。如果保留被推翻或仍模糊，刪除它。回滾並重新執行一次被拒絕的報告，並指明失敗原因。拒絕第二次，報告為開放，並使 `/no-comments` 失敗。
3. 直接修正簡單的已接受標記，透過刪除死路、移除引數或使用真實 API。如果任何修正需要形狀，對已接受的集合和周圍程式碼執行一次 `/architect`。停在草稿階段。設計形狀。步驟 4 進行實作。
4. 實作範圍內最小的根本原因修正。移除每個命名的繞行方案。如果根本原因超出範圍，落地最小的範圍內修正並將其餘報告為開放。**principle-fix-root-causes** 和 **principle-redesign-from-first-principles** 技能僅指引意圖。兩者都不授權擴大範圍或修正範圍外的例項。切勿附加症狀防護。
5. 限制評論說明 `do not remove`、`do not change wording` 或 `talk to X before changing`。保留關於我們無法更改事物的保留。提供範圍內最便宜的型別、執行時間、測試或 CI lint。等待互動式批准。無人值守和評估需要呼叫者預先批准。如果批准，則編碼後刪除。否則刪除，報告限制為開放，並草擬範圍外工作。
6. 報告刪除數量、恢復的評論、重新執行、設計草稿、修正、編碼提案、編碼、未強制的限制以及其他開放工作。
