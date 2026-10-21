# Tailscale

Grok Bot 外掛，讓代理可以存取您私人 [Tailscale](https://tailscale.com) 網路（tailnet）上的 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器。

讓 Grok Bot 存取您私人 Tailscale 網路上的 MCP 伺服器。在您的個人 tailnet 中，它會以一個 tag:grokbot 裝置加入；在團隊的公司 tailnet 中，由團隊管理員啟用後，它會使用每位成員各自的裝置。

<a id="who-can-use-it"></a>

## 誰可以使用

- Grok Bot **0.49** 或更新版本。
- 一個具有編輯 tailnet 政策檔案許可權的 Tailscale 帳戶。
- 不適用於 Cursor。Cursor 不得列出或安裝此外掛。

## MCP

此外掛不提供任何 `mcp.json`。Grok Bot 的 Tailscale 連線由 Cursor 後端提供，安裝外掛即表示同意並啟用此功能。您想要在 tailnet 上存取的其他 MCP 伺服器，需透過其 `*.ts.net` 名稱或 `100.x` 地址另行新增。

<a id="setup"></a>

## 設定

1. 在 tailnet 政策檔案中，定義 `tag:grokbot`，並僅授予它存取 Grok Bot 應可達的 MCP 主機和埠。
2. 在 Grok Bot 的外掛中開啟 **Tailscale**，點選 **Authenticate**，並登入 Tailscale。
3. 如果裝置核准功能已啟用，請在 Tailscale 管理控制檯中核準新裝置。在具有 Tailnet Lock 的 tailnet 上，也需簽署該裝置。

`tailscale-setup` 技能會逐步說明每個步驟，包括政策片段範例。

<a id="notes"></a>

## 注意事項

- 只有來自 bot 擁有者和連結的團隊成員的訊息，以及擁有者排定的例行任務，才能使用 tailnet。
- Grok Bot 只能存取您授予 `tag:grokbot` 允許的範圍，無法存取其他內容。
- 若要斷開連線，請在 Grok Bot 的 Tailscale 外掛頁面上點選 **Remove**。

<a id="docs"></a>

## 檔案

- Tailscale 授予權：https://tailscale.com/docs/features/access-control/grants
- 標籤：https://tailscale.com/docs/features/tags
- 裝置核准：https://tailscale.com/docs/features/access-control/device-management/device-approval
- Tailnet Lock：https://tailscale.com/docs/features/tailnet-lock

Logo 是 Tailscale 的官方標誌，來自 Tailscale 媒體資料包。

<a id="license"></a>

## 授權

MIT
