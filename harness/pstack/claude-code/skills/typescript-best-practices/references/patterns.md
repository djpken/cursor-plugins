<a id="typescript-patterns"></a>

# TypeScript 模式

`SKILL.md` 中每個規則的程式碼範例。基本原則與語言無關。請參閱 **type-system-discipline** 和 **boundary-discipline** 原則技能。

<a id="branded-types"></a>

## 品牌化型別

品牌化基本型別，使其無法混淆。在邊界處驗證一次。下游程式碼信任該型別。

```ts
type AgentId = string & { readonly __brand: "AgentId" };

function isAgentId(input: string): input is AgentId {
  return isUUID(input);
}

function parseAgentId(input: string): AgentId {
  if (!isAgentId(input)) throw new Error(`Invalid agent id: ${input}`);
  return input;
}

function focusAgent(id: AgentId): void {
  /* input is trusted */
}
```

匹配 `readonly __brand: 'X'` 的結構。不要發明新的慣例。

<a id="discriminated-unions"></a>

## 判別聯合

用字面判別值來建模變體。每個變體共享相同的欄位名稱，且每個變體的值是獨特的，因此不可能的組合無法被表示。

```ts
// Don't. Boolean + optionals lets contradictory states exist.
type DiffState = { loading: boolean; diff?: GitDiff; error?: string };

// Do. Only valid states exist.
type DiffState =
  | { kind: "loading" }
  | { kind: "ready"; diff: GitDiff }
  | { kind: "error"; error: string };
```

選擇一個判別名稱（`kind`、`type`、`tag`）並保持一致。

<a id="constructive-modeling"></a>

## 構造性建模

從全部合法的部分構建型別，而不是用執行時檢查限制鬆散的型別。

非空，透過可變引數元組：

```ts
type NonEmpty<T> = [T, ...T[]];

// Don't: T[] plus a length check every caller must repeat
function pickWinner(entries: string[]): string {
  if (entries.length === 0) throw new Error("no entries");
  return entries[Math.floor(Math.random() * entries.length)];
}

// Do: an empty value of the type can't exist
function pickWinner(entries: NonEmpty<string>): string {
  return entries[Math.floor(Math.random() * entries.length)];
}
```

當一個普通的 `T[]` 到達時，用一個守衛進行一次縮窄。該事實隨後在型別中傳遞：

```ts
const isNonEmpty = <T>(arr: T[]): arr is NonEmpty<T> => arr.length > 0;
```

偶數長度，作為對：

```ts
type Pairs<T> = [T, T][];
```

時間範圍，作為開始時間加上持續時間：

```ts
// Don't: a comment holds the invariant
type TimeRange = { start: Date; end: Date }; // start <= end

// Do: a negative range can't be written; derive end when needed
type TimeRange = { start: Date; durationMs: number };
```

保持 `durationMs` 為普通數字。僅當原始數字可能被傳入而期望持續時間時，才將其品牌化（根據品牌化型別），不要反射性地做。選擇使壞狀態無法構造的表示，然後在其之上提供所需的讀取方式（`pairs.flat()`、一個 `rangeEnd()` 輔助函式）。

<a id="simplest-total-type"></a>

## 最簡單的全函式型別

不要強化一切。當對 `T[]` 的每個操作都是全函式時，保持它：

```ts
const sum = (xs: number[]) => xs.reduce((a, b) => a + b, 0); // [] is 0, fine
```

當鬆散型別在使用點迫使謊言時，強化。跡象是 `!`、`arr[0] as T` 和一個「應該不會發生」的丟擲：

```ts
// Don't: partiality smuggled past the compiler
function newestSession(sessions: Session[]): Session {
  return sessions.at(0)!;
}

// Do: strengthen the input; the assertion disappears
function newestSession(sessions: NonEmpty<Session>): Session {
  return sessions[0];
}
```

將結果弱化為 `Session | undefined` 是另一個全函式簽名。

<a id="unknown-over-any"></a>

## `unknown` 優先於 `any`

外部資料始終是 `unknown`。在使用前縮窄。

```ts
// Don't
function handle(input: any) {
  return input.foo.bar;
}

// Do
function handle(input: unknown) {
  if (typeof input === "object" && input !== null && "foo" in input) {
    // narrowed; compiler verifies access
  }
}
```

外部來源包括 RPC 載體、`JSON.parse`、`postMessage`、IPC、檔案內容、環境變數、資料庫結果。

<a id="schemas-before-hand-rolled-guards"></a>

## 先使用結構定義，而非手寫守衛

在為外部資料寫逐屬性型別守衛之前，查詢儲存庫的執行時結構定義庫和現有結構定義。讓一個結構定義負責驗證，並從它派生 TypeScript 型別。不要維護一個結構定義、一個重複的介面和一個可能漂移的守衛。

```ts
import { z } from "zod";

const UserSchema = z.object({
  id: z.string().uuid(),
  role: z.enum(["admin", "member"]),
});

type User = z.infer<typeof UserSchema>;

function parseUser(input: unknown): User {
  return UserSchema.parse(input);
}
```

當失敗是預期的分支時，使用 `safeParse`。當儲存庫使用另一個結構定義庫時，使用等效的推斷輔助函式。不要為一個守衛新增新的結構定義依賴。此規則優先使用程式碼庫已經信任的結構定義系統。

<a id="no-as-casts"></a>

## 不使用 `as` 轉換

每個 `as` 都是潛在的執行時崩潰。僅在型別系統已驗證該宣告後才轉換。

```ts
import { z } from "zod";

// Don't
const user = data as User;

// Don't
function isUser(data: unknown): data is User {
  return typeof data === "object" && data !== null && "id" in data;
}

// Do
const userSchema = z.object({ id: z.string(), name: z.string() });
type User = z.infer<typeof userSchema>;

function parseUser(data: unknown): User {
  return userSchema.parse(data);
}
```

當型別優先時，用它所證明型別來標註驗證器。編譯器隨後會拒絕證明少於該型別的驗證器。從下面的物件中移除 `name`，該賦值將無法編譯。

```ts
type User = { id: string; name: string };

const userSchema: z.ZodType<User> = z.object({ id: z.string(), name: z.string() });
```

當從現有程式碼中重構出 `as` 時，識別 TypeScript 無法推斷的原因：

- 缺少判別值：新增一個，切換到判別聯合。
- 過寬的來源型別（例如 `Record<string, unknown>`）：縮窄它。
- 無型別邊界：用擁有該結構的結構定義進行解析。僅在不存在結構定義時新增一個。
- 真正無法表達：使用品牌化型別或 `satisfies`。

<a id="narrowing-hierarchy"></a>

## 縮窄層級

從最佳到最後手段：

1. **判別聯合 switch / if。** 編譯器自動縮窄。
2. **`in` 運運算元。** `"key" in obj` 縮窄到包含該鍵的變體。
3. **`typeof` / `instanceof`。** 適用於基本型別與類別例項。
4. **使用者定義型別守衛。** 當上述不足時。
5. **`as` 轉換。** 僅在驗證後。

```ts
function area(s: Shape): number {
  if ("radius" in s) return Math.PI * s.radius ** 2; // narrowed to circle
  return s.width * s.height; // narrowed to rect
}
```

<a id="type-guards"></a>

## 型別守衛

守衛必須實際驗證宣告。說謊的守衛比 `as` 更糟。

```ts
function isCircle(s: Shape): s is Shape & { kind: "circle" } {
  return s.kind === "circle";
}
```

優先使用判別縮窄。

<a id="exhaustiveness"></a>

## 窮盡性

在 default 分支中，將判別值賦給一個 `never` 型別化的區域性變數。

```ts
// Value-returning switch
function area(s: Shape): number {
  switch (s.kind) {
    case "circle":
      return Math.PI * s.radius ** 2;
    case "rect":
      return s.width * s.height;
    default: {
      const _exhaustive: never = s;
      return _exhaustive;
    }
  }
}

// Void switch
function handle(s: Shape): void {
  switch (s.kind) {
    case "circle":
      drawCircle(s);
      break;
    case "rect":
      drawRect(s);
      break;
    default: {
      const _exhaustive: never = s;
      void _exhaustive;
    }
  }
}
```

在返回值 switch 中使用 return 風格，在語句 switch 中使用 void 風格。

<a id="satisfies-over-as"></a>

## `satisfies` 優先於 `as`

`satisfies` 驗證而不擴寬字面型別。

```ts
// Don't. Widens, loses literal types.
const config = { theme: "dark", cols: 3 } as Config;

// Do. Validates AND preserves literal types.
const config = { theme: "dark", cols: 3 } satisfies Config;
// config.theme is "dark" (literal), not string
```

<a id="boundary-validation"></a>

## 邊界驗證

在資料進入邊界時驗證一次。內部信任型別。請參閱 **boundary-discipline** 原則技能。

- **線格式**（proto、JSON-RPC）：用 `ignoreUnknownFields` 解析，以便向前相容的變更不會破壞舊客戶。
- **持久化 JSON：** 帶版本化的 blob，並在解析周圍使用 try/catch。
- **不要在深層呼叫鏈中重新驗證。**

<a id="schema-derived-types"></a>

## 結構定義派生型別

當 `.proto`、OpenAPI 規格、GraphQL 結構定義或資料庫遷移已定義結構時，從生成的型別派生，而不是重複它們。

```ts
// Don't. Duplicate shape, drifts when the schema changes.
type CheckSummary = {
  totalCount: number;
  checks: { name: string; status: string }[];
};
function renderChecks(s: CheckSummary) {
  /* ... */
}

// Do. Derive from the generated schema type.
import type { ChecksMessage } from "<generated module>";
function renderChecks(s: Pick<ChecksMessage, "totalCount" | "checks">) {
  /* ... */
}
```

在寫新介面之前，先使用 `Pick`、`Omit`、`Parameters`、`ReturnType`、`Awaited`、`typeof`。

<a id="object-args"></a>

## 物件引數

```ts
// Don't. Swap two args, still compiles.
openFile(uri, {
  startLineNumber: 10,
  startColumn: 1,
  endLineNumber: 10,
  endColumn: 1,
});

// Do. Order-independent, self-documenting.
openFile({
  uri,
  selection: {
    startLineNumber: 10,
    startColumn: 1,
    endLineNumber: 10,
    endColumn: 1,
  },
});
```

在熱路徑上跳過：每幀渲染、詞法分析器、解析器、任何緊密迴圈中分配成本重要的地方。
