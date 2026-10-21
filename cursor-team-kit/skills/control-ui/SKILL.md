---
name: control-ui
description: "建置或調整本機瀏覽器/CDP 測試框架，以驅動並檢查網頁、IDE 或 Electron 介面。適用於本機介面驗證、截圖、無障礙快照、效能分析、視覺差異比較，或重現介面錯誤。"
---

<a id="control-ui"></a>

# 控制介面

使用本機瀏覽器自動化來驗證介面行為並提供證據。若儲存庫中已存在 Playwright、瀏覽器或 Electron 測試框架，請優先重用；否則，請圍繞應用程式的開發伺服器或 Chromium 偵錯埠組建一個暫時的本地測試框架。

<a id="what-it-is-used-for"></a>

## 用途

- 重現依賴真實瀏覽器焦點、鍵盤輸入、捲動、調整大小或渲染的介面錯誤。
- 使用截圖和快照驗證視覺或無障礙變更。
- 在發布前檢查本機網頁、IDE 或 Electron 的行為。
- 擷取主控臺紀錄、網路紀錄、CPU 分析、追蹤記錄或堆積快照。
- 為 `verify-this` 建立變更前後的證據。

<a id="setup-pattern"></a>

## 設定模式

1. 使用儲存庫中記錄的開發命令在本地啟動應用程式。
2. 發現現有的本地測試框架：Playwright 測試、Cypress 規格、Storybook、瀏覽器指令碼、Electron 啟動指令碼或快照工具。
3. 對於網頁應用程式，使用現有的瀏覽器工具連線到本地 URL。
4. 對於 Electron/Chromium，在支援時啟用遠端偵錯埠。
5. 使用穩定的應用程式標記來選擇正確的頁面，而非僅依賴分頁順序。
6. 優先使用無障礙角色、標籤和穩定的 `data-*` 選擇器，而非座標。

<a id="generic-web-harness"></a>

## 通用網頁測試框架

在可能的情況下，使用儲存庫中已安裝的瀏覽器工具。如果儲存庫中已有 Playwright，一個最小的一次性探測指令碼如下所示：

```javascript
import { chromium } from "playwright";

const browser = await chromium.launch();
const page = await browser.newPage({ viewport: { width: 1280, height: 800 } });
await page.goto("http://127.0.0.1:<port>");
await page.getByRole("button", { name: /submit/i }).click();
await page.screenshot({ path: "/tmp/ui-harness-after.png", fullPage: true });
await browser.close();
```

除非使用者要求，否則不要僅為了此探測而將 Playwright 新增為專案依賴。請優先使用現有的開發依賴或環境中已可用的外部瀏覽器工具。

<a id="generic-cdp-harness"></a>

## 通用 CDP 測試框架

對於使用 `--remote-debugging-port=<port>` 啟動的 Electron 或 Chromium 應用程式，透過 CDP 連線：

```javascript
import { chromium } from "playwright";

const browser = await chromium.connectOverCDP("http://127.0.0.1:<debug-port>");
const pages = browser.contexts().flatMap((context) => context.pages());
let page;
for (const candidate of pages) {
  if (await candidate.locator("<app-root-selector>").count()) {
    page = candidate;
    break;
  }
}

if (!page) {
  console.log(await Promise.all(pages.map(async (p) => ({
    title: await p.title(),
    url: p.url(),
  }))));
  throw new Error("No matching app page found");
}

await page.screenshot({ path: "/tmp/ui-harness-cdp.png", fullPage: true });
await browser.close();
```

將 `<app-root-selector>` 替換為目前儲存庫中的穩定標記，例如根應用程式節點、地標或產品特定的 `data-*` 屬性。

<a id="interaction-loop"></a>

## 互動迴圈

1. 在執行操作前擷取頁面快照或截圖。
2. 從最新的頁面結構中選擇目標。
3. 執行恰好一個結構性操作：點選、輸入、按鍵、拖曳、捲動、導航或調整大小。
4. 擷取新的快照/截圖。
5. 驗證預期的狀態變更。
6. 當使用者要求證明時，儲存產出物以進行變更前後的比較。

<a id="cdp-capabilities"></a>

## CDP 功能

僅當高階瀏覽器 API 不足時，才使用原始 CDP：

- 效能：CPU 分析、追蹤記錄、繪製閃爍、FPS 計數器、版面設定位移檢查。
- 記憶體：堆積快照和強制垃圾回收，用於洩漏調查。
- 網路：請求封鎖、節流、停用快取、請求/回應紀錄。
- 渲染：檢視區變更、色彩方案模擬、減少動態效果、無障礙檢查。
- 偵錯：主控臺串流、例外捕捉、DOM 快照。

<a id="page-selection"></a>

## 頁面選擇

當多個應用程式視窗/分頁共用一個偵錯埠時：

- 優先使用受測表面的正向標記，例如應用程式根選擇器。
- 在必要時，使用負向標記以避免錯誤的表面。
- 如果沒有頁面匹配，請列出可用的頁面標題和 URL，而不是猜測。

<a id="guardrails"></a>

## 防護措施

- 在導航或結構變更後，不要依賴過期的元素參考。
- 除非在點選前剛擷取了新的截圖，否則避免使用座標點選。
- 保持測試資料在本地且可丟棄。
- 除非使用者明確同意，否則不要儲存來自隱私敏感工作區的截圖或堆積快照。
- 不要從其他儲存庫硬編碼選擇器、埠號或指令碼路徑。請發現目前儲存庫的本地應用程式標記。
- 完成後清理開發伺服器、偵錯會話和臨時設定檔。
