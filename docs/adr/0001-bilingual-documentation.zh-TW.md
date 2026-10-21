# 繁體中文文件與上游原檔並存

此 fork 使用 `zh-TW` 作為預設分支，並以 `*.zh-TW.md` 對應檔提供台灣繁體中文書面文件。英文原檔是核對依據。英文 README 只新增導覽提示，連向中文 README 與文件索引。

翻譯 README、每份 `SKILL.md`、其 Markdown 參考文件與閱讀指南。frontmatter 除了 `description` 外，其他欄位包含 `name` 都逐位元組保留。程式碼區塊、inline code、指令、路徑、URL 與 plugin manifest 保持原樣。不翻譯 `AGENTS.md`、`CLAUDE.md`、`RELEASE-NOTES.md` 與 changelog。

對應檔讓執行中的 skill 來源保持穩定，也便於合併上游更新。參考連結保留英文目的地，方便核對原文；中文索引直接連向譯文。中文標題保留英文標題的明確錨點，讓既有片段連結繼續有效。

`harness/pstack` 是產生檔目錄。翻譯對應的 `pstack` 來源，再執行 `node scripts/generate-pstack-harness-trees.mjs`；不在產生檔目錄獨立維護翻譯，也不為了在地化而修改 generator。

翻譯由機器輔助完成。發布前驗證來源涵蓋範圍、呼叫名稱、frontmatter、程式碼、連結目的地與台灣書面語。英文來源變更時，同步更新中文對應檔；兩者意思有落差時，以英文來源為準。
