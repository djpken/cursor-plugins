<a id="build-the-change-and-clean-the-diff"></a>

# 建置變更並清理 diff

這些建置 playbook 共享同一套紀律。陳述你觀察到的現象，並讓 playbook 要求證據。此頁說明每個常見建置任務的提示詞應包含哪些內容，以及保持 diff 可審查的清理習慣。

<a id="prompt-each-build-playbook-with-what-you-know"></a>

## 根據已知資訊提示每個建置 playbook

Bug 提示詞應陳述症狀，並要求先進行重現：

```text
/poteto-mode this command emits two records after a retry. repro first, then fix and verify.
```

功能提示詞應陳述行為以及哪些部分不得變更：

```text
/poteto-mode add a --json flag. text output stays byte-identical. verify both forms.
```

重構提示詞應在結構變動前固定行為：

```text
/poteto-mode move parsing into one module, zero behavior change. record the current output first and prove it's unchanged after.
```

效能提示詞應陳述測量結果，而非印象：

```text
/poteto-mode startup takes 1.8s on this fixture. trace it, fix the measured cause, show me before and after.
```

這些提示詞各自對應其 playbook（[Bug fix](../../skills/poteto-mode/playbooks/bug-fix.md)、[Feature](../../skills/poteto-mode/playbooks/feature.md)、[Refactoring](../../skills/poteto-mode/playbooks/refactoring.md)、[Perf issue](../../skills/poteto-mode/playbooks/perf-issue.md)），playbook 會提供你未輸入的步驟：修復前先重現、實作前先命名資料形狀、重構前先固定行為、最佳化前先分析效能。

若要持續改善單一指標，可使用 [Hillclimb playbook](../../skills/poteto-mode/playbooks/hillclimb.md)。提供指標、目標以及嘗試次數的下限，它會以凍結的測量框架一次驗證一個假設。它會保留有效的改進，並還原其他所有變更。

這兩個效能 playbook 都會對其數字執行 [`/benchmark-checklist`](../../skills/benchmark-checklist/SKILL.md)。Perf issue 會驗證其基準線及其後的每個數字，Hillclimb 則在凍結前驗證其框架。[Verify and ship](./06-verify-and-ship.md#vet-a-measured-number-with-benchmark-checklist) 說明何時應自行輸入此指令。

有時你希望在任何修復之前先找出原因。對於即時症狀，例如記憶體洩漏、閒置 CPU 旋轉或視覺故障，[Runtime forensics playbook](../../skills/poteto-mode/playbooks/runtime-forensics.md) 會對執行中的程式進行儀器化。對於你已擷取的 profile，[Trace forensics playbook](../../skills/poteto-mode/playbooks/trace-forensics.md) 會讀取該產出物並將熱框架對應到原始碼。兩者都返回診斷結果，而非修復方案：

```text
/poteto-mode here's a cpuprofile from the slow startup. tell me where the time goes and which source lines own it. no fix yet.
```

<a id="write-the-failing-test-first-with-tdd"></a>

## 先用 `/tdd` 撰寫失敗的測試

當 bug 有低成本的本地測試路徑時，整個提示詞可以只有兩個詞：

```text
/tdd implement
```

在此情境下，這就足夠了。[`/tdd`](../../skills/tdd/SKILL.md) 會撰寫一個因預期原因而失敗的最小測試，然後進行修復，最後重新執行測試。如果測試需要廣泛的框架設定或脆弱的 mock，該 skill 會說明並改用最近的可執行檢查。不要強行撰寫測試，如果真實命令是更強的證據。

<a id="load-the-typescript-rules-by-name"></a>

## 依名稱載入 TypeScript 規則

[`typescript-best-practices`](../../skills/typescript-best-practices/SKILL.md) 將型別系統原則轉化為具體規則：鑑別聯集、邊界處的 `unknown`、窮舉變體、由 schema 衍生的型別。它不會自行載入，因此當任務涉及 `.ts` 或 `.tsx` 檔案時，請輸入 `/typescript-best-practices`。

<a id="clean-before-you-commit"></a>

## 提交前清理

[Opening a PR playbook](../../skills/poteto-mode/playbooks/opening-a-pr.md) 會在每次 commit 前對 diff 執行 `/deslop`，並對 PR 描述和 commit 內容套用 [`/unslop`](../../skills/unslop/SKILL.md)。`/deslop` 包含在 `cursor-team-kit` plugin 中，而非 pstack。如果你沒有它，請用白話要求相同結果：移除敘述性註解、無依據的防護、無效的相容路徑以及無關的編輯。

對於散文，`/unslop` 接受目標以及你擁有的任何額外規則：

```text
/unslop the readme changes, no emdashes
```

你會發展出自己的簡寫。該 skill 能從簡短的提示詞（如 `unslop that, tighten it`）中正確讀取意圖。

<a id="strip-the-comments-with-no-comments"></a>

## 用 `/no-comments` 移除註解

註解需要獨立的處理，且不能由撰寫它們的 agent 執行。作者會像保護自己的註解一樣保護其註解。因此在審查前，請交給新鮮的眼睛：

```text
/no-comments the diff
```

[`/no-comments`](../../skills/no-comments/SKILL.md) 會啟動 [Comment Sicko](../../agents/comment-sicko.md)，這是一個唯讀審查者，擁有一份簡短的保留清單：授權標頭、公開 API 的檔案註解、解釋程式碼無法表達之內容的連結、由無法重塑的外部依賴強制執行的行為。其他一切都移除。你自己程式碼中的意外不會獲得此豁免。註解會以重構旗標的形式返回，`/no-comments` 會在根本原因處修復它接受的旗標。當註解聲稱存在限制（例如「請勿移除」）時，該 skill 會建議將此聲稱編碼為型別、測試或 lint。無論如何，註解都會被移除。

保持分工清晰是值得的。`/deslop` 清理程式碼中的雜訊，`/unslop` 清理散文中的雜訊，`/no-comments` 將註解交給未撰寫它們的審查者。

**陷阱：** 清理不是可選的潤飾。包含敘述性註解和防禦性冗餘的 diff 會讓審查者覺得未完成，而多餘的程式碼正是下一個 bug 隱藏之處。如果 diff 感覺膨脹，請在 commit 前說 `deslop it`，而不是在審查指出問題後才說。

下一步：[Verify and ship](./06-verify-and-ship.md)。
