<a id="the-pstack-guide"></a>

# pstack 指南

當您停止微觀管理 agent 時，pstack 的運作效果最佳。您只需描述想要達成的目標，以及如何判斷任務已完成。`/poteto-mode` 會選擇 playbook，在步驟需要時執行其他 skills，並向您展示證據。本指南透過實際的 prompts 教導您養成這種習慣。

您將學到以下內容：

1. [設定 pstack](./01-setup.md)。安裝 plugin 並選擇您的 models。
2. [透過 `/poteto-mode`](./02-poteto-mode.md) 路由工作。提供一個目標，並觀察它如何選擇 playbook。
3. [理解程式碼](./03-understand.md)。進行唯讀調查，然後在編輯任何內容之前執行 `/how`、`/why`、`/teach` 和 `/recall`。
4. [設計變更](./04-design.md)。在程式碼鎖定結構之前，執行 `/architect`、`/arena`、`/swarm`、`/interrogate`、原型和計畫。
5. [建置並清理變更](./05-build-and-clean.md)。使用建置 playbooks、`/tdd`、`/unslop` 和 `/no-comments`。
6. [驗證並發布](./06-verify-and-ship.md)。在實際應用程式上證明行為，正式驗證測試很少涵蓋的部分，使用 `/benchmark-checklist` 審核數字，然後開啟一個專注的 PR 並推動其合併。
7. [在您睡覺時執行工作](./07-overnight.md)。在 loops 之前建立信任、過夜合約、可稽核的決策記錄，以及能擴充套件至單一 agent 之外的 Projects 和 automations。
8. [使用原則名稱進行引導](./08-principles.md)。這 24 個名稱可在任務中途重新引導 agent。
9. [使其成為您自己的](./09-make-it-yours.md)。您自己的 mode、用於重複錯誤的 `/correct`，以及如何測試 skill 變更。
10. [食譜與陷阱](./10-recipes-and-pitfalls.md)。可複製的 prompts 和應避免的錯誤。

第一次請依序閱讀各頁面。之後，每個頁面都可獨立閱讀。

當您卡住，或無法判斷哪個 skill 適合時，輸入 [`/poteto-help`](../../skills/poteto-help/SKILL.md) 並附上您的問題：

```text
/poteto-help which skill should i use to review this branch?
```

它會回答問題，提供一個可傳送的 prompt，並連結答案來源的 skill 或指南頁面。它不會開始執行工作，因為 pstack 執行會消耗實際 tokens，所以請在您準備好時再傳送 prompt。它只會在您輸入時執行。

<a id="if-you-only-remember-one-thing"></a>

## 如果您只記得一件事

用您自己的話，給 agent 一個目標和檢查方式：

```text
/poteto-mode the export writes duplicate rows when a retry lands mid-run. repro first, then fix and verify.
```

您不需要指定 playbook 或列出 skills。「先重現」和可檢查的結果就是 `/poteto-mode` 所需的所有路由訊號。它會匹配 Bug fix playbook，將步驟複製到 todo list，並在每個步驟觸發時呼叫正確的 skills。

下一步：[設定 pstack](./01-setup.md)。
