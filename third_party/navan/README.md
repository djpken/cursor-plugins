# Navan

Cursor 外掛，透過 Navan 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Navan](https://navan.com)。

查詢費用與支出趨勢，分析航班、飯店及地面交通的旅遊預訂，詢問政策、核准流程與標記/拒絕原因，並查詢登入 Navan 使用者之卡片詳細資訊。

<a id="prerequisite"></a>

## 前置條件

Navan 管理員必須先為您的組織啟用 MCP：**Navan → 設定 → 整合 → MCP** 並開啟該功能。在此之前，任何 MCP 客戶端的連線都會失敗。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Navan**。
3. 點選 **安裝**，然後完成 Navan SSO 登入提示。

或在聊天中執行 `/add-plugin navan`。

## MCP

```json
{
  "mcpServers": {
    "navan": {
      "type": "http",
      "url": "https://mcp.navan.com/mcp"
    }
  }
}
```

驗證採用 OAuth 2.0，透過 Navan 標準 SSO 登入。第一個請求會開啟瀏覽器視窗以登入並核准許可權範圍。無需設定 API 金鑰或客戶端 ID。使用期間會自動更新會話。若超過 100 天未使用或距離驗證已超過 365 天，則需再次登入。

<a id="notes"></a>

## 注意事項

- 工具呼叫會在登入使用者之上下文與角色（員工、經理、核准者、財務管理員）下執行，且無法超出該使用者之 Navan 許可權。
- 對使用者無權檢視資料的查詢會返回空結果並附上 `403_BY_POLICY` 註記，而非洩漏資料。
- 伺服器無法繞過核准流程、政策規則或重複偵測，且無法提升許可權。
- 每個工具呼叫都會記錄在 Navan 稽核軌跡中，包含使用者身份、客戶端名稱/版本、工具引數及回應狀態。
- 保持資料量精簡：傳遞特定日期範圍、僅篩選所需欄位，並優先使用摘要工具（例如 `summarize_spend`），再深入檢視原始資料行。
- Navan MCP 不適用於高量或定期執行的工作負載。請使用 [Navan REST API](https://developer.navan.com/) 處理這些需求。

<a id="verify"></a>

## 驗證

連線後，詢問代理：

> "使用 Navan 列出我最近五筆費用。"

正確連線的客戶端會在幾秒內返回結構化表格。若看到許可權提示，請核准。這是 Navan 的範圍同意流程。

<a id="docs"></a>

## 檔案

- Navan MCP 指南：https://developer.navan.com/mcp/
- 伺服器 URL：https://mcp.navan.com/mcp
