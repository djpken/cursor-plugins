# Ralph Loop

Ralph Loop 讓 Cursor 以自我參考迴圈運作，每一輪結束後將相同的提示再次送回，直到任務完成。它實作 Geoffrey Huntley 率先提出的 [Ralph Wiggum 技巧](https://ghuntley.com/ralph/)。

<a id="how-it-works"></a>

## 運作方式

兩個 hook 驅動迴圈。一個 `afterAgentResponse` hook 監看每個回應，尋找與完成語句相符的 `<promise>` 標記。一個 `stop` hook 在 Cursor 完成一個回合時觸發。如果尚未偵測到完成語句且尚未達到迭代上限，stop hook 會將原始提示以 `followup_message` 送回，開始下一輪迭代。Cursor 會看到自己在工作樹與 git 歷史中的先前編輯，對它們進行迭代，然後重複。提示永遠不變。程式碼則持續改變。

<a id="installation"></a>

## 安裝

```
/add-plugin ralph-loop
```

<a id="quick-start"></a>

## 快速開始

> 啟動 ralph loop：「建立一個 REST API 來管理待辦事項。CRUD 操作、輸入驗證、測試。完成時輸出 COMPLETE。」 --completion-promise "COMPLETE" --max-iterations 50

Cursor 會實作 API、執行測試、看到失敗、修正它們，並重複直到所有需求滿足。

<a id="skills"></a>

## 技能

**ralph-loop** 啟動迴圈。提供提示與選項：

> 啟動 ralph loop：「重構 cache 層」 --max-iterations 20 --completion-promise "DONE"

- `--max-iterations <N>` 在 N 次迭代後停止（預設：無限制）
- `--completion-promise <text>` 設定表示完成的語句

**cancel-ralph** 移除狀態檔案並停止迴圈。

**ralph-loop-help** 詳細說明技巧與用法。

<a id="writing-good-prompts"></a>

## 撰寫良好提示

定義明確的完成標準。模糊的目標如「把它做好」無法讓 Cursor 有可驗證的依據。

```markdown
Build a REST API for todos.

When complete:
- All CRUD endpoints working
- Input validation in place
- Tests passing (coverage > 80%)
- Output: <promise>COMPLETE</promise>
```

將大型任務拆成階段。透過在提示中包含測試/修正迴圈來鼓勵自我修正。始終傳入 `--max-iterations` 以防止失控迴圈。

<a id="when-to-use-ralph-loop"></a>

## 何時使用 Ralph Loop

適合具有明確、可驗證成功標準的任務：讓測試透過、完成遷移、根據規格建立功能。不適合需要人類判斷或目標模糊的任務。

<a id="learn-more"></a>

## 瞭解更多

- [Geoffrey Huntley 的原始技巧](https://ghuntley.com/ralph/)
- [Ralph Orchestrator](https://github.com/mikeyobrien/ralph-orchestrator)

<a id="license"></a>

## 授權

MIT
