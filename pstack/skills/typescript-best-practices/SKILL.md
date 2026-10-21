---
name: typescript-best-practices
description: "TypeScript 最佳做法。在讀取或編輯任何 .ts 或 .tsx 檔案時使用。"
paths: ["**/*.ts", "**/*.tsx"]
disable-model-invocation: true
---

<a id="typescript-best-practices"></a>

# TypeScript 最佳做法

首先應用 **type-system-discipline** 原則技能。

| 規則 | 摘要 |
|------|---------|
| 判別聯合 | 使用 `kind` 字面判別來建模變體，使不可能狀態無法表示。不使用可選欄位袋。 |
| 品牌型別 | 用 `& { readonly __brand: "X" }` 標記基本型別，使其無法混淆。在邊界驗證一次。品牌來自驗證型別守衛（`input is AgentId`），絕不來自 `as`。 |
| 建設性建模 | 構建形狀使非法值無法構造。`[T, ...T[]]` 用於非空，`[T, T][]` 用於偶數長度，`start` 加 `duration` 用於範圍。不是執行時守衛，不是對細化型別的需求。 |
| 最簡單全型 | 保持 `T[]` 而每個操作在其上保持全型。僅在鬆散型別迫使 `NonEmpty<T>`、一個轉換或一個「絕不應發生」丟擲時，強化到 `!`。 |
| `unknown` 優先於 `any` | 外部資料是 `unknown`。 |
| 先使用 Schema 再守衛 | 在手寫逐屬性型別守衛之前，使用儲存庫的執行時 schema 庫並從 schema 推斷型別，例如 `z.infer`。 |
| 不使用 `as` 轉換 | 每個 `as` 是等待的執行時崩潰。僅在驗證後轉換。 |
| 縮窄層級 | 判別 switch > `in` 運運算元 > `typeof`/`instanceof` > 使用者定義型別守衛 > `as`。 |
| 型別守衛 | 必須驗證宣告。說謊的守衛比 `as` 更糟，因為錯誤藏在一個說安全的名字後面。命名為 `isX` 或 `hasX`。 |
| 完整性 | 在 default 分支中放置 `const _exhaustive: never = x;`，使編譯器在新增變體時出錯。 |
| `satisfies` 優先於 `as` | 驗證值而不擴大字面型別。 |
| 邊界驗證 | 在資料進入時解析，轉為具名領域型別。`Record<string, unknown>`（無論如何拼寫）在該解析處停止。內部信任型別。見 **boundary-discipline** 原則技能。 |
| Schema 衍生型別 | 在宣告新介面之前，先使用 `Pick`/`Omit`/`Parameters`/`ReturnType`/`Awaited`/`typeof`。 |
| 物件引數 | 傳物件而非位置引數，使引數順序自我說明。在熱路徑（每幀渲染、tokenizer、parser）上跳過。 |
| 真實測試 | 不要 mock 你能執行的事物。優先使用框架的真實測試基礎元件，帶 leak/disposable 檢查，並在執行構建中驗證 UI。僅 mock 無法在本地執行的事物。 |
| 結構化遙測 | 優先使用結構化 logger 診斷，帶足夠上下文從 id 除錯。在發布程式碼中不使用 `console.log`。 |

示例：`references/patterns.md`。
