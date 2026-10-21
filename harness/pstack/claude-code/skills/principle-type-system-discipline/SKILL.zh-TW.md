---
name: principle-type-system-discipline
description: "在設計型別、檢視函式簽名或在任何靜態型別語言中寫程式碼時應用此原則。讓非法狀態無法表示、為語義上的基本型別建立品牌、在邊界解析外部資料、窮盡所有變體、從權威的 schema 中衍生型別。"
disable-model-invocation: true
---

<a id="type-system-discipline"></a>

# 型別系統規範

讓編譯器拒絕不可能的狀態、不匹配的基本型別及未處理的變體。優先透過定義來消除錯誤與特殊情況，而非擴增處理器。關於 TypeScript 語法，請參考 `typescript-best-practices` 技能。

- **為語義上的基本型別建立品牌。** `UserId` 與 `OrderId` 在底層都是字串，但應不可互相交換。為每個型別賦予其獨特的型別，例如在 TypeScript 中使用 branded intersection，或在其他地方使用 newtype。在建立時驗證一次，之後在下游信任該型別。如果兩個引數共享基本型別但意義不同，請為它們建立品牌。
- **外部資料在解析前是無型別。** RPC payload、JSON、IPC 訊息、CLI 引數、設定檔案、環境變數、資料庫行。在每個邊界都有一個 parse 函式，將非結構化輸入轉為型別模型。**邊界規範**原則技能說明應在哪裡放置驗證。
- **從權威的 schema 衍生型別。** 當 protocol buffer、OpenAPI 規格、GraphQL schema、資料庫遷移、設計 token 檔案或其他檔案擁有一個形狀，請從它衍生，而非手動建立平行型別。請參考 **encode-lessons-in-structure** 原則技能。
