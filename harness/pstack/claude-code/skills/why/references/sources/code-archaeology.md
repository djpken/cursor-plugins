<a id="code-archaeology-git--in-repo"></a>

# 程式碼考古（git + 儲存庫內）

<a id="what-this-source-contains"></a>

## 此來源包含什麼

- 提交歷史（訊息、日期、作者、差異）
- PR 描述、審閱評論與討論串（透過 `gh`）
- 行內程式碼註解、TODO、FIXME、棄用說明
- ADR（架構決策記錄），若儲存庫保留這些記錄
- 測試，其名稱與斷言常編碼了促成變更的邊界案例
- 同一提交中修改的相關檔案（共同變更訊號）
- 儲存庫中的 CHANGELOG 條目與發行說明
- 提交訊息與 PR 正文中的問題/票單 ID

最可信的來源，直接與程式碼相關，且最完整。所有經過儲存庫的內容都應在此處。

<a id="how-to-search-it"></a>

## 如何搜尋

擴充初始提交列表：

```bash
# History through renames
git log --follow --oneline -- <file>

# Pickaxe: commits that added or removed this exact text
git log -S '<exact_string_from_code>' -- <file>

# Or for patterns:
git log -G '<regex>' -- <file>

# Who wrote each line and when
git blame -L <start>,<end> <file>

# One commit's full diff
git show <hash>

# Commits between two points affecting this file
git log <old>..<new> -p -- <file>
```

尋找外部檔案：

```bash
# ADRs often live in docs/adr/ or similar
rg -l -i 'architecture.decision' --glob '*.md'

# TODOs and FIXMEs near the target
rg -n -C2 '(TODO|FIXME|HACK|XXX|NOTE)' <target_file>

# Related tests
rg -l '<symbol>' --glob '*test*'
```
