---
name: tailscale-setup
description: "逐步引導使用者將 Grok Bot 連線到他們的 Tailscale tailnet：在 tailnet 政策檔案中定義 tag:grokbot，僅授予它應訪問的 MCP 主機和埠，從 Tailscale 外掛登入，批准裝置，然後新增 tailnet MCP 伺服器。當使用者要求設定、排查或斷開 Grok Bot 的 Tailscale 連線時使用此內容。"
---

<a id="tailscale-setup-for-grok-bot"></a>

# Grok Bot 的 Tailscale 設定

Grok Bot 作為一個標記為 `tag:grokbot` 的裝置加入使用者所屬的 tailnet。它只能訪問 tailnet 政策檔案授予該標記的主機和埠，因此請在登入之前設定政策。

在團隊的公司 tailnet 上，由 Grok Bot 團隊管理員在 Tailscale 外掛中啟用，每位成員從團隊行連線自己的 Grok Bot 裝置，不需要 `tag:grokbot` 政策。如果在個人行登入在公司 tailnet 上失敗並出現 `tag:grokbot` 錯誤，請使用公司 tailnet。

<a id="1-define-the-tag-and-grant-access"></a>

## 1. 定義標記並授予訪問許可權

在 Tailscale 管理控制檯中，開啟 **存取控制** 並編輯政策檔案：

- 將 `tag:grokbot` 新增到 `tagOwners`，列出將要登入的使用者（或 `autogroup:admin`）。
- 新增從 `tag:grokbot` 到僅 Grok Bot 應訪問的 MCP 主機和埠授予許可權。不要授予其他內容。

```hujson
{
  "tagOwners": {
    "tag:grokbot": ["autogroup:admin"],
  },
  "grants": [
    {
      "src": ["tag:grokbot"],
      "dst": ["mcp-server.tail1234.ts.net"],
      "ip":  ["tcp:443"],
    },
  ],
}
```

將這些鍵合併到現有政策中，而不是替換它。在 `dst` 中使用使用者真實的主機名稱、`100.x` 地址或標記，以及在 `ip` 中使用其 MCP 伺服器監聽埠。如果政策仍包含預設的允許所有規則，`tag:grokbot` 可以訪問每個裝置；請指出這一點並建議縮小範圍。

<a id="2-connect-grok-bot"></a>

## 2. 連線 Grok Bot

1. 在 Grok Bot 中開啟 **Tailscale** 外掛並點選 **驗證**。
2. 登入 Tailscale 並批准新裝置。
3. 如果 tailnet 啟用了裝置批准，請在管理控制檯的 **機器** 頁面批准裝置。
4. 如果 tailnet 使用 Tailnet Lock，請從簽名節點（`tailscale lock sign <node-key>`）簽名裝置。它無法訪問任何內容直到被簽名。

<a id="3-add-mcp-servers"></a>

## 3. 新增 MCP 伺服器

以常規方式新增每個 MCP 伺服器，使用其 `*.ts.net` 名稱或 `100.x` 地址作為 URL 主機。對該主機請求會透過 tailnet 傳輸。

<a id="who-can-use-the-tailnet"></a>

## 誰可以使用 tailnet

只有來自機器人所有者及關聯隊友的訊息，以及所有者排定的例行任務，可以使用 tailnet。其他向機器人傳送訊息的人無法使用。

<a id="disconnect"></a>

## 斷開連線

在 Grok Bot 的 Tailscale 外掛頁面點選 **移除**。

<a id="troubleshooting"></a>

## 排查問題

- **檢查主機是否可訪問：** 使用 Tailscale 外掛的主機檢查工具，而不是從機器人電腦使用 curl，後者僅使用個人連線。若要使用伺服器，將其新增為 MCP 伺服器。
- **無法訪問主機：** 檢查從 `tag:grokbot` 的授予是否涵蓋該主機和埠，以及裝置是否批准（並簽名，若使用 Tailnet Lock）。
- **登入無法應用標記：** 登入使用者不是 `tagOwners` 中 `tag:grokbot` 的擁有者。
