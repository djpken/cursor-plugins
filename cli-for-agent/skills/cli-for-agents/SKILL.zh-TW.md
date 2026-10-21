---
name: cli-for-agents
description: >-
  設計或審查 CLI，讓程式碼代理能可靠地執行：非互動式
  旗標、分層的 --help 並附範例、stdin/管線、快速且可採取行動的錯誤訊息、
  冪等性、dry-run，以及可預測的結構。在建立 CLI、
  新增指令、撰寫 --help，或當使用者提到代理、終端機、
  或適合自動化的 CLI 時使用。
---

<a id="cli-for-agents"></a>

# 供代理使用的 CLI

以人類為導向的 CLI 常會阻礙代理：互動式提示、龐大的前置檔案，以及沒有可複製貼上範例的說明文字。請優先選擇能無頭執行並在管線中組合的樣式。

<a id="non-interactive-first"></a>

## 非互動式優先

- 每個輸入都應能表達為旗標或旗標值。不要要求方向鍵、選單或限時提示。
- 如果缺少旗標，**再**退回互動模式，而不是相反。

**錯誤：** `mycli deploy` → `? Which environment? (use arrow keys)`<br>
**正確：** `mycli deploy --env staging`

<a id="discoverability-without-dumping-context"></a>

## 可發現性，但不傾倒上下文

- 代理會逐步發現子指令：`mycli`，然後 `mycli deploy --help`。不要在每次執行時列印整本手冊。
- 讓每個子指令擁有自己的檔案，以便未使用的指令不會佔用上下文。

<a id="--help-that-works"></a>

## 有效的 `--help`

- 每個子指令都有 `--help`。
- 每個 `--help` 都包含 **範例**，並附上實際的呼叫方式。對於模式匹配而言，範例比純文字說明更有用。

```text
Options:
  --env     Target environment (staging, production)
  --tag     Image tag (default: latest)
  --force   Skip confirmation

Examples:
  mycli deploy --env staging
  mycli deploy --env production --tag v1.2.3
  mycli deploy --env staging --force
```

<a id="stdin-flags-and-pipelines"></a>

## stdin、旗標與管線

- 在合理的情況下接受 stdin（例如 `cat config.json | mycli config import --stdin`）。
- 避免奇怪的引數順序，並避免在缺少值時退回互動式提示。
- 支援連結：`mycli deploy --env staging --tag $(mycli build --output tag-only)`。

<a id="fail-fast-with-actionable-errors"></a>

## 快速失敗並提供可採取行動的錯誤

- 當缺少必要的旗標時：立即結束並顯示清晰的訊息以及**正確的範例呼叫**，而不是掛起。

```text
Error: No image tag specified.
  mycli deploy --env staging --tag <image-tag>
  Available tags: mycli build list --output tags
```

<a id="idempotency"></a>

## 冪等性

- 代理經常重試。同一個成功的指令執行兩次應該是安全的（無操作或明確的「已完成」），而不是產生重複的副作用。

<a id="destructive-actions"></a>

## 破壞性操作

- 新增 `--dry-run`（或等效功能），讓代理可以在提交前預覽計畫。
- 提供 `--yes` / `--force` 以跳過確認，同時為人類保留安全的預設值。

<a id="predictable-structure"></a>

## 可預測的結構

- 在所有地方使用一致的樣式，例如 `resource` + `verb`：如果 `mycli service list` 存在，`mycli deploy list` 和 `mycli config list` 應遵循相同的結構。

<a id="success-output"></a>

## 成功輸出

- 成功時，回傳對機器有用的資料：ID、URL、持續時間。純文字即可；避免僅依賴裝飾性輸出。

```text
deployed v1.2.3 to staging
url: https://staging.myapp.com
deploy_id: dep_abc123
duration: 34s
```

<a id="when-reviewing-an-existing-cli"></a>

## 審查現有 CLI 時

- 檢查專案：非互動式路徑、分層說明、`--help` 上的範例、stdin/管線支援、包含呼叫方式的錯誤訊息、冪等性、dry-run、跳過確認的旗標、一致的指令結構、結構化的成功輸出。
