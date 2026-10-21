---
name: X Money guide
description: >-
  在首次 X Money 操作之前閱讀此內容，並在出現任何
  X Money 錯誤、拒絕或缺少功能時再次閱讀。涵蓋每個涉及資金操作的
  批准規則（每次都詢問，無例外），
  連線方式（連線碼加上 X 應用中的通行金鑰），如何
  指導使用者進行連線、重新連線和撤銷，詳見
  x.com/i/money/settings/connections，以及每個失敗訊息的含義。
---

<a id="x-money-guide"></a>

# X Money 指南

此外掛透過 **X Money MCP** 將代理連線到使用者於 `https://mcp.money.x.com/mcp` 的 X Money 帳戶。使用者在 Grok Bot 中連線一次，代理隨後代表使用者操作其 X Money 帳戶。

從伺服器的工具列表中瞭解使用者可以執行哪些操作。列表由 X Money 根據每個帳戶決定，因此不要因為這裡提到某個操作就假定它存在。每個結果都包含一個為使用者撰寫的 `message`。請用自己的話傳達，而不是描述內部細節。

<a id="always-confirm-before-moving-money"></a>

## 在移動資金之前始終確認

某些操作會移動真實資金或創造消費方式：傳送資金、請求資金和支付商家。對於每個操作：

1. **每次都先詢問。** 傳送一個 `SendToUser` 元件，包含 **批准** 和 **取消**。提示為一句話，明確說明使用者批准的內容：誰收款或被請求、金額多少、用途是什麼。對於購買：商品、商家和總金額。將詳細資訊（商品、運費、稅費、費用）放在說明文字中。如果金額是估算值，請說明。
2. **等待回答。** 僅在使用者選擇批准後呼叫工具。如果他們選擇取消或未回答，則不執行任何操作並說明。
3. **一次批准一個操作。** 絕不在一個批准下批次處理多個付款。絕不從先前訊息、先前會話或一般指令（如「處理它」）中沿用批准。
4. **當任何事項變更時重新詢問。** 不同的金額、收款人、商家或用途是新操作，需要新的批准。
5. **絕不偽造。** 每個資金移動工具都有批准輸入。伺服器在未設定時拒絕呼叫並確認未執行任何操作。僅在本次對話中實際批准後設定它。

可以自由詢問餘額和交易。這些是僅讀操作。

<a id="how-the-connection-works"></a>

## 連線如何運作

1. 使用者在 Grok Bot 中點選 X Money 外掛上的 **連線**。
2. 一個標題為 **將 Grok Bot 連線至 X Money** 的頁面開啟。它顯示一個 **連線碼** 和一個 QR 碼。
3. 使用者在擁有 X Money 帳戶的 X 帳戶的 **X Money 應用** 中輸入該碼（或掃描 QR 碼），然後使用他們的 **通行金鑰** 批准。
4. 頁面顯示 **已連線。您可以返回 Grok Bot。** 連線自動完成。

該碼有效期約 5 分鐘。連線隨後保持有效，直到使用者撤銷或長時間不使用。使用者不需要處理客戶 ID、API 金鑰或令牌，代理也永遠不會看到這些。

<a id="connections-screen"></a>

## 連線畫面

在 X 應用中：`https://x.com/i/money/settings/connections`。路徑為 Money → Settings → Connections。

將使用者引至此處以：

- 檢視哪些代理已連線至 X Money 以及每個代理可以執行什麼操作，
- 撤銷 Grok Bot 的存取權，
- 確認他們剛批准的連線已列出。

撤銷會立即結束連線。下一次 X Money 操作會失敗並出現授權錯誤，使用者必須重新連線。

<a id="troubleshooting"></a>

## 故障排除

匹配情況，用自己的語氣說出引用的句子，然後提供一個下一步。不要解釋 OAuth、令牌或連線的內部運作方式。

<a id="not-connected-or-authorization-error-on-a-call"></a>

### 未連線，或呼叫時出現授權錯誤

訊號：X Money 顯示需要授權，沒有列出 X Money 工具，或呼叫失敗並出現 `401` 或 `invalid bearer token`。

> 您的 X Money 連線未啟用。開啟 Grok Bot 中的 X Money 外掛並點選連線，然後在 X Money 應用中輸入連線碼並使用您的通行金鑰批准。

如果使用者說他們沒有撤銷任何東西，仍然重新連線。沒有其他方式恢復連線。

<a id="this-code-has-expired-start-again-from-grok-bot"></a>

### "此碼已過期。請從 Grok Bot 重新開始。"

同意頁面約 5 分鐘後顯示此訊息。

> 連線碼已過期。在 Grok Bot 中再次點選連線以取得新碼，然後立即在 X Money 應用中批准。

<a id="this-connection-was-not-approved"></a>

### "此連線未批准。"

使用者在 X Money 應用中拒絕了。

> 連線被拒絕。如果您仍想連線，在 Grok Bot 中再次點選連線並批准新碼。

<a id="the-connection-code-is-invalid-expired-or-already-used"></a>

### "連線碼無效、已過期或已被使用。"

X Money 應用在碼已在另一個帳戶上被使用、已被使用或已過期時顯示此訊息。

詢問使用者在 X Money 應用中登入的 X 帳戶。碼必須從持有 X Money 帳戶的 X 帳戶批准。每個碼僅使用一次。告訴他們從連線重新開始。

<a id="passkey-step-fails"></a>

### 通行金鑰步驟失敗

X Money 應用無法完成通行金鑰挑戰。

> 批准代理連線需要您的 X 帳戶上的通行金鑰。在 X → Settings → Security 中設定一個，然後再次批准連線。

<a id="new-customer-connections-are-not-currently-accepted"></a>

### "目前不接受新客戶連線。"

連線已批准但 X Money 未啟用。這是分階段推出，不是帳戶問題。

> X Money 目前不接受您的帳戶的新代理連線。請稍後再試。

不要建議替代方案。

<a id="something-went-wrong-refresh-this-page-to-try-again-or-something-went-wrong-try-again"></a>

### "發生錯誤。請重新整理此頁面以重試。" 或 "發生錯誤。請重試。"

同意頁面上的暫時錯誤。

> 重新整理頁面。如果再次失敗，在 Grok Bot 中點選連線重新開始。

<a id="this-x-money-mcp-tool-isnt-ready-for-you-yet-or-an-action-is-missing"></a>

### "此 X Money MCP 工具尚未為您準備。" 或操作缺失

X Money 按帳戶啟用操作。缺少工具或呼叫時出現此訊息表示該操作尚未為此使用者啟用。

> 該 X Money 操作尚未為您的帳戶啟用。

提供現有的操作。不要告訴使用者重新安裝或重新連線。

<a id="not-authorized-to-call-tool"></a>

### "未授權呼叫工具"

使用者在帳戶上的角色不允許此操作，例如共同帳戶的成員。

> 您的 X Money 帳戶不允許從這裡執行該操作。您可以在 X Money 應用中執行。

<a id="mcp-is-not-enabled"></a>

### "MCP 未啟用"

X Money 已暫停所有人的代理存取。

> X Money 暫時無法供代理使用。請稍後再試。

<a id="user-has-no-x-money-account"></a>

### 使用者沒有 X Money 帳戶

同意頁面或 X Money 應用顯示使用者無法使用 X Money，或使用者說他們從未設定。

> X Money 可供美國合格客戶使用。先在 X 應用中設定 X Money，然後在此連線。

<a id="reading-payment-results"></a>

## 讀取付款結果

每個傳送或請求都會返回一個 `outcome` 和一個 `message`。

| 結果 | 含義 | 應做的事 |
| --- | --- | --- |
| `completed` | 資金已移動，或請求已到達對方 | 傳達訊息 |
| `pending` | X Money 正在審核，或對方需要採取行動 | 轉達訊息。不要再次傳送。使用者可以在 X Money 應用中檢視 |
| `refused` | X Money 未完成此操作 | 轉達訊息。**絕不重試拒絕**，也不要更改金額來繞過限制 |

常見拒絕訊息及應補充內容：

| 訊息包含 | 補充內容 |
| --- | --- |
| `verify this payment in the X Money app` | 代理人無法完成驗證。使用者可以在 X 應用中自行傳送。 |
| `per-payment limit for agents` 或 `Agents can't move money for this account` | 代理人付款有其自身限制。使用者可以在 X Money 應用中執行此操作。 |
| `sending limits` 或 `Too many transfers` | 稍後再嘗試，或在 X Money 應用中付款。 |
| `balance is too low` | 檢查餘額並提供較小金額，或在 X 應用中加值。 |
| `No X user named` | 確認 @handle。handle 可能變動；數字形式的 X 使用者 ID 較穩定。 |
| `isn't on X Money yet` | 對方必須先設定 X Money，才能收款或被請求付款。 |
| `own X account` | 選擇另一位 X 使用者。 |
| `only accepts money from people they follow` | 此處無法修正。 |
| `different currencies` 或 `can't receive` 或 `can't send` | 此處無法修正。請在 X Money 應用中付款。 |
| `X Money did not allow this payment` | X Money 已阻止此操作，且未提供進一步原因。不要重試。 |

兩個結果並非結果而是錯誤，且兩者都很重要：

- **`X Money could not process this right now; try again later`**：暫時性失敗。沒有資金移動。告知使用者。僅在他們仍想要時稍後重試一次，並需新的核準。
- **`X Money accepted this payment but could not confirm it. Do not send it again`**：付款可能已完成。**絕不要再次傳送。**告知使用者在 X Money 應用中檢查交易。

當您在網路失敗後重試同一筆付款，請重用相同的 idempotency key，讓 X Money 絕不會執行兩次。重用 key 會返回先前結果並說明此事。

<a id="purchases-with-a-card"></a>

## 使用卡片購買

- 核准購買本身：商品、商家和總金額。X Money 發行的卡片並非使用者核准的物件。
- 如果結果顯示**未建立卡片**是因為每日限制已達上限，它會指出限制重置的時間。告知使用者並停止。今天不要再嘗試。
- `failed to create card, try again` 是暫時性的。一次重試即可，並使用相同的核準。
- 卡片資訊直接進入商家結帳流程。絕不要在聊天中顯示卡片號碼、CVC 或到期日，也不要儲存這些資訊。

<a id="never"></a>

## 絕不要

- 絕不要要求使用者提供 X Money 密碼、passkey、卡片號碼或銀行登入資訊。
- 絕不要要求使用者將 token 或 code 貼入聊天。
- 絕不要移動資金、請求資金或建立卡片，除非有針對該特定動作的新核准。
- 絕不要重試拒絕、未確認的付款或限制結果。
- 絕不要解釋 token 或內部連線機制。說明情況和下一步。
