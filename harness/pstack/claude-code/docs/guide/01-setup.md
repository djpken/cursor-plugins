<a id="set-up-pstack"></a>

# 設定 pstack

在本頁中，您將安裝 plugin，選擇 pstack 使用的模型，並執行第一個任務。設定只需一條指令加上簡短的對話。

<a id="install-the-plugin"></a>

## 安裝 plugin

在 Cursor 對話中，執行：

```text
/add-plugin pstack
```

Cursor 會確認 plugin 已安裝。

在 Claude Code 中，執行 `/plugin marketplace add cursor/plugins`，然後執行 `/plugin install pstack@cursor-plugins`。Claude Code 會在 plugin 指令前加上字首，因此本指南中的每個指令在該環境中都需要加上 `pstack:` 字首，例如 `/pstack:setup-pstack`，它會將模型規則寫入 `~/.claude/rules/pstack-models.md`。[Claude Code adapter](../harness/claude-code.md) 涵蓋了其他差異。

<a id="pick-your-models"></a>

## 選擇您的模型

執行：

```text
/setup-pstack
```

[`/setup-pstack`](../../skills/setup-pstack/SKILL.md) 會偵測您有許可權使用的模型，詢問推理預算，顯示每個角色（code delegates、judgment、the review panels），並詢問您的偏好。請回答這些問題。它會寫入 `~/.cursor/rules/pstack-models.mdc`，這是一條小型規則，每個 pstack skill 都會讀取。

預設設定在 `xhigh` 推理下執行，與 `large` 預算相同。`unlimited` 會將每個模型提升至其最高層級，最高至 `max`。Opus 會提升至 `max`。Grok 的最高層級為 `xhigh`，因此會維持在該層級。`medium` 和 `small` 會降低推理並減少 token 消耗。

您只需覆寫您關心的部分。規則中沒有對應行的角色會保留 skill 的預設值。若要恢復預設值，請刪除該角色的行。重新執行 `/setup-pstack` 會保留模型與預設值不同的角色。當預設值變更時，在變更前寫入的規則仍會固定舊的預設值，因此請刪除這些角色的行，或刪除該檔案，然後再次執行 `/setup-pstack`。

您可能想知道如果使用 Auto 會發生什麼情況。將角色設定為 `inherit-parent` 或 `auto`，pstack 會省略 subagent 的 `model` 欄位，因此 subagent 會繼承您父對話的模型。這兩個值意義相同，且都不是模型 slug。對於 panel 角色，該值是一個清單，每個專案執行一個 subagent，因此清單長度決定了 panel 大小。設定也會設定 `swarm workers`，這是每個 `/swarm` worker 的預設模型，除非 race 為每個 arm 指定了模型。

<a id="accept-the-verification-offers-or-dont"></a>

## 接受驗證建議，或拒絕

在設定結束時，`/setup-pstack` 會尋找一種方式來證明您專案中的應用程式行為，例如 `verify-*` skill 或現有的 harness。如果兩者都找不到，它會提供一次使用 [`/create-verification-skill`](../../skills/create-verification-skill/SKILL.md) 生成一個的機會。

說「是」，它會寫入 `.cursor/skills/verify-<app>/`，這是一個專案本地的 skill，教導 agent 像使用者一樣操作您的應用程式。在交付之前，它會先證明該 skill 有效一次。說「否」，設定就會繼續進行。您可以隨時自行執行 `/create-verification-skill`。[Verify and ship](./06-verify-and-ship.md#create-a-project-verification-skill) 深入涵蓋了此主題。

如果您是 pstack 的新手，請說「是」。能夠檢查自身工作的 agent 會持續進行直到檢查透過為止。無法檢查的 agent 會將每個結果交還給您，讓您手動檢查。在本指南的所有內容中，驗證 skill 的效益最大。

接著，除非專案已經有一個，否則設定會提供一次使用 [`/create-formal-verification-skill`](../../skills/create-formal-verification-skill/SKILL.md) 生成正式驗證 skill 的機會。測試檢查的是有人想到要撰寫的案例。正式驗證 skill 會檢查您程式碼的一個關鍵屬性，例如「一個 job 絕不會同時執行兩次」，在指定範圍內的每個案例中。如果您的程式碼包含並行處理、重試、狀態機或解析器，且 bug 代價高昂而測試很少能觸及導致失敗的案例，請說「是」。否則請說「否」，如果情況改變，之後再執行。[Verify and ship](./06-verify-and-ship.md#prove-a-property-with-a-formal-verification-skill) 涵蓋了此主題。

設定完成後，開啟新的對話。模型規則適用於新的 session。

<a id="keep-the-cost-in-check"></a>

## 控制成本

pstack 會在 subagent 和 review panels 上花費額外的 token。這是嚴謹性的代價。若要減少花費：

- 重新執行 `/setup-pstack`，選擇較小的推理預算或較便宜的模型。在主對話中使用強勁的模型，在 code 角色中使用較便宜、較快的模型，是一個良好的分配方式。
- 將角色設定為 `auto` 或 `inherit-parent`，使其在對話本身的模型上執行。
- 縮短 panel 清單。每個專案執行一個 subagent。
- 將 `/poteto-mode` 保留給需要嚴謹性的工作。小型且明顯的編輯不需要。

<a id="run-your-first-task"></a>

## 執行您的第一個任務

選擇一個真實但小型的專案，並像向同事描述那樣描述它：

```text
/poteto-mode add a --json flag to this command. text output stays byte-identical. verify both.
```

觀察 todo 清單。其前幾項是匹配的 playbook 步驟的複製，在此提示中為 Feature playbook。如果 `/poteto-mode` 跳過某個步驟，該步驟會以 `skip: <reason>` 保留在清單中，讓您可以看到它選擇不執行的內容。

從這裡開始，您可以輸入普通的後續訊息。若要讓 `/poteto-mode` 在整個對話中保持開啟，請從 `/` 選單中選擇它，並使用 Option+Enter (Mac) 或 Alt+Enter (Windows) 而不是 Enter。這會使其成為 [Custom Mode](https://cursor.com/docs/skills#using-a-skill-as-a-custom-mode)，在每個回合中都會保留在 context 中，直到您退出為止。Custom Modes 可在 Agents Window 和 CLI 中使用。普通的 Enter 會將 skill 附加到單則訊息，並隨著對話進行而淡出。

下一步：[Route work through `/poteto-mode`](./02-poteto-mode.md)。
