<a id="authentication"></a>

# 驗證

簡短版本：`export CURSOR_API_KEY=cursor_...` 和 `Agent.create({ apiKey: process.env.CURSOR_API_KEY! })`。如果這些方法不起作用，本頁其餘內容就是您需要閱讀的資訊。

<a id="key-types"></a>

## 金鑰型別

SDK 接受兩種金鑰型別；兩者皆適用於本機和雲端。


| 金鑰型別                 | 簽發位置                                                                              | 使用時機                                                         |
| ------------------------ | -------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| 使用者 API 金鑰             | [https://cursor.com/dashboard/cloud-agents](https://cursor.com/dashboard/cloud-agents) | 開發工具、個人指令碼、以特定使用者身分執行                   |
| 團隊服務帳戶金鑰 | 團隊設定 → 服務帳戶                                                       | 共用 CI、後端服務、任何不應該由真實人員持有金鑰的地方 |


兩者都放在相同的 `apiKey` / `CURSOR_API_KEY` 位置，沒有二等公民之說。Token 格式相同；您無法透過檢查來區分它們。

<a id="how-the-sdk-finds-the-key"></a>

## SDK 如何找到金鑰

優先順序：

1. 傳遞給選項物件的 `apiKey`（`Agent.create`、`Agent.prompt`、`Agent.resume`、`Agent.get` 等）
2. `process.env.CURSOR_API_KEY`

就是這樣，SDK 本身沒有設定檔，也沒有金鑰串整合。對於共用基礎架構程式碼，**請務必明確傳遞 `apiKey`**，而不是依賴環境變數，這樣憑證依賴關係在呼叫點才會一目瞭然。

某些僅限雲端的輔助函式（`Agent.archive`、`Agent.delete`、`Cursor.me`、`Cursor.models.list`、`Cursor.repositories.list`）也接受 `apiKey` 作為具名選項。當它們未收到該選項時，會回退到 `CURSOR_API_KEY`。

<a id="minimum-viable-setup"></a>

## 最小可行設定

```bash
export CURSOR_API_KEY="cursor_..."
```

```typescript
import { Agent } from "@cursor/sdk";

const agent = Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2" },
  local: { cwd: process.cwd() },
});
```

非 null 斷言（`!`）是一種可讀的方式，用來表示「如果缺少環境變數就大聲失敗」，否則 SDK 稍後會丟擲驗證錯誤，而堆疊追蹤不會指向環境變數。

稍微禮貌一點的模式：

```typescript
const apiKey = process.env.CURSOR_API_KEY;
if (!apiKey) {
  console.error("Missing CURSOR_API_KEY. Mint one at https://cursor.com/dashboard/cloud-agents.");
  process.exit(1);
}
```

<a id="symptoms-of-bad-auth"></a>

## 驗證失敗的症狀


| 症狀                                                  | 診斷                                                                      |
| -------------------------------------------------------- | ------------------------------------------------------------------------------ |
| 首次 `send()` 時出現 `AuthenticationError: ...`             | 金鑰缺失、過期或格式錯誤（包括空白字元）                      |
| `ConfigurationError: BAD_USER_API_KEY`                   | 金鑰在語法上無效                                                   |
| 僅在雲端出現 `AuthenticationError`，本機正常         | 金鑰無法存取雲端代理介面，可能是許可權問題        |
| 雲端執行錯誤，出現 `ERROR_GITHUB_NO_USER_CREDENTIALS` | 呼叫者沒有與目標儲存庫的 GitHub 連線                     |
| 本機正常，CI 中出現 401                                | 環境變數未進入 CI 工作（引號、秘密範圍、作用域等） |
| 間歇性 401                                        | 幾乎總是金鑰輪換問題，或兩個衝突的 `CURSOR_API_KEY` 值  |


<a id="error_github_no_user_credentials-is-not-a-code-bug"></a>

### `ERROR_GITHUB_NO_USER_CREDENTIALS` 不是程式碼錯誤

雲端執行會使用呼叫者的 GitHub 憑證來複製目標儲存庫。如果 `CURSOR_API_KEY` 後方的使用者從未在 Cursor 儀錶板中連線 GitHub，雲端代理無法存取儲存庫，執行會立即失敗。解決方法：呼叫者在儀錶板中連結 GitHub。無需變更程式碼。

對於服務帳戶金鑰，服務帳戶需要像使用者一樣設定 GitHub 存取許可權。

<a id="rotating-keys"></a>

## 輪換金鑰

- 對於生產工作負載，請定期輪換金鑰；不要將金鑰嵌入映像檔中。
- 當金鑰洩漏時，請先在儀錶板中撤銷，然後簽發新的金鑰。SDK 會在下一個程式啟動時讀取新的環境值。
- 建議使用每個環境的金鑰（staging、prod），而不是單一全域金鑰，以便撤銷範圍明確。

<a id="multiple-keys-in-one-process"></a>

## 同一個程式中的多個金鑰

完全支援，每個 `Agent.create` / `Agent.prompt` 呼叫都是獨立的。請明確傳遞 `apiKey`，以免意外回退到呼叫者未考慮到的環境變數：

```typescript
const userAgent  = Agent.create({ apiKey: userKey,  /* ... */ });
const botAgent   = Agent.create({ apiKey: botKey,   /* ... */ });
```

<a id="local-development"></a>

## 本機開發

如果您正在開發整合並快速迭代，請將金鑰放在已 gitignore 的 `.env` 檔案中，並透過類似 `dotenv` 的方式載入：

```typescript
import "dotenv/config";
// Now process.env.CURSOR_API_KEY is populated.
```

不要提交 `.env`。SDK 本身不會讀取 `.env`。

<a id="ci-checklist"></a>

## CI 檢查清單

- 將金鑰放在秘密儲存庫中，而不是工作流檔案中。
- 將秘密的範圍限定在需要它的特定工作/工作流。
- 對於 GitHub Actions，請使用儲存庫層級或環境層級的秘密（環境層級還提供核准閘門）。
- 如果需要確認使用的是哪個金鑰，請僅在紀錄中列印金鑰字首（前 6 個字元），切勿列印完整值。
- 如果金鑰缺失，請快速失敗，不要讓工作執行 10 分鐘後才在最後出現 401。

<a id="service-accounts-for-production-integrations"></a>

## 用於生產整合的服務帳戶

對於任何不是個人開發指令碼的情況：

1. 在團隊設定中建立團隊服務帳戶。
2. 只授予它所需的許可權。
3. 為它簽發金鑰，並將金鑰儲存在您的秘密管理器中。
4. 如果整合會啟動雲端代理，請為服務帳戶連結 GitHub（而不是操作人員的個人帳戶）。
5. 監控服務帳戶的使用情況，如果所有操作都在單一金鑰下執行，您將失去按呼叫者歸屬的能力。
