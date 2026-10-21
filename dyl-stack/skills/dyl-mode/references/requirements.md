<a id="requirements"></a>

# 需求

dyl-stack 依賴其他 plugin。在開始任何工作之前，請先檢查您的 skill 所需的專案。如果缺少其中一項，請停止作業，指出缺少的專案，並告知使用者執行 `/add-plugin <name>`。請勿自行補上缺少的部分。

| Plugin | 安裝時機 | 存取其檔案 |
|---|---|---|
| `pstack` | `setup-pstack` 在您的 skill 清單中 | 大多數 pstack skills 僅限斜線指令使用，且不會顯示在清單中。它們位於 `setup-pstack` 旁邊：`<setup-pstack dir>/../poteto-mode/SKILL.md`、`../poteto-mode/playbooks/<name>.md`、`../principle-<name>/SKILL.md`、`../bro/SKILL.md`、`../unslop/SKILL.md`。 |
| `cursor-team-kit` | `control-ui`、`verify-this` 和 `deslop` 在您的 skill 清單中 | 它們所列出的路徑。 |
| `thermos` | `thermo-nuclear-review-subagent` 和 `thermo-nuclear-code-quality-review-subagent` 是可用的 Task subagent 型別 | 透過 subagent 型別啟動它們。 |
| `figma` | `figma-design-to-code` 在您的 skill 清單中，且 Figma MCP 工具呼叫成功 | 其列出的路徑。 |
