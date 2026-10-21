# WhatsApp Business

Cursor 外掛透過 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器（由 Cursor 提供）將代理連線到 [WhatsApp Business](https://business.whatsapp.com)，這是 Meta 的企業訊息平臺。

使用 WhatsApp Business 帳戶：從您的企業電話號碼傳送訊息、管理訊息模板，以及查詢聯絡人和電話號碼。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor Settings → Plugins**。
2. 搜尋 **WhatsApp Business** 並點選 **Install**。
3. 輸入您的 WhatsApp Business 帳戶 ID、電話號碼 ID 和存取令牌（請參閱 [Before you connect](#before-you-connect)）。

或在聊天中執行 `/add-plugin whatsapp-business`。

## MCP

此外掛不包含任何 `mcp.json`，這是刻意設計。

此儲存庫中的其他每個聯結器，不是指向供應商的託管端點，也不是透過 stdio 在本地執行套件，因此各自帶有伺服器定義。WhatsApp Business 則不同：Cursor 執行伺服器，外掛僅作為選用功能，為您的帳戶啟用它。沒有需要執行指令，也沒有需要設定的 MCP 端點。

一個值得知道的影響：因為伺服器未在此宣告，您不會在本地 `mcp.json` 中找到它，也不能將其他 MCP 客戶端指向它。它只能從 Cursor 存取。

## Before you connect

每個安裝外掛的人需輸入自己的三個值。這些是每個成員的設定，團隊管理員無法為所有人設定。

<a id="1-create-a-system-user-token-in-meta"></a>

### 1. 在 Meta 建立系統使用者令牌

1. 在 [Meta Business Suite](https://business.facebook.com)，開啟 **Business settings → Users → System users**，新增系統使用者，或選擇現有的系統使用者。
2. 在 **Assign assets** 下，授予該系統使用者存取您的 WhatsApp Business 帳戶。
3. 點選 **Generate new token**，選擇與您的 WhatsApp Business 帳戶連結的 Meta 應用，並選取 `whatsapp_business_messaging` 和 `whatsapp_business_management` 許可權。
4. 複製令牌。Meta 僅顯示一次。

<a id="2-find-your-ids-in-whatsapp-manager"></a>

### 2. 在 WhatsApp Manager 中找到您的 ID

- **WhatsApp Business Account ID:** 在 [WhatsApp Manager](https://business.facebook.com/wa/manage/home/) 中顯示於帳戶名稱旁。
- **Phone number ID:** 在 **Account tools → Phone numbers** 下的長數字 ID。請使用 ID，而非電話號碼本身。

<a id="3-enter-the-values-in-cursor"></a>

### 3. 在 Cursor 中輸入這些值

三個欄位皆為必填：

| 欄位 | 值 |
| --- | --- |
| `WHATSAPP_BUSINESS_ACCOUNT_ID` | 來自 WhatsApp Manager 的 WhatsApp Business 帳戶 ID。 |
| `WHATSAPP_BUSINESS_PHONE_NUMBER_ID` | 來自 **Account tools → Phone numbers** 的電話號碼 ID。代理從此號碼傳送訊息。 |
| `WHATSAPP_BUSINESS_ACCESS_TOKEN` | 步驟 1 的系統使用者令牌。視為秘密，絕不會再次顯示。 |

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 能力 |
| --- | --- |
| Messages | 從您的企業電話號碼傳送文字和模板訊息 |
| Templates | 列出和管理訊息模板 |
| Contacts | 查詢您的企業訊息聯絡人 |
| Phone numbers | 讀取您的 WhatsApp Business 帳戶上的電話號碼 |

伺服器是工具名稱和結構的來源。

## Notes

- 存取權遵循系統使用者令牌。聯結器只能存取該系統使用者被授予的 WhatsApp Business 帳戶，並受限於令牌的許可權範圍。
- WhatsApp 僅允許在客戶最後訊息後 24 小時內傳送自由格式訊息。超出該時間範圍，代理必須傳送已核准的模板。
- 訊息從您的真實企業號碼發出，可能產生 Meta 的訊息費用。請審查代理傳送的內容。
- 在 Cursor 中斷連線會刪除您儲存的帳戶 ID、電話號碼 ID 和存取令牌。在 Meta Business Settings 中撤銷令牌會終止 Cursor 對您的帳戶的存取。

## Docs

- WhatsApp Business Platform: https://business.whatsapp.com
- WhatsApp Cloud API: https://developers.facebook.com/docs/whatsapp/cloud-api
- Model Context Protocol: https://modelcontextprotocol.io

Logo 是 WhatsApp 的官方標誌，來自 `whatsapp.com`。

## License

MIT
