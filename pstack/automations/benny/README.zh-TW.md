# benny

benny 為您提供兩個用於 Slack 問題報告的 Cursor 自動化流程。其中一個負責對每份報告進行分級，另一個則重現已確認的錯誤，並可能準備一份小型的修復草稿。

此目錄中的檔案是處於休眠狀態的設定與自動化來源。它們不會顯示為斜線技能。

<a id="set-it-up"></a>

## 進行設定

1. 將 Cursor 指向 [`FOR_AGENTS.md`](./FOR_AGENTS.md)，並命名目標儲存庫。
2. 讓設定流程將整個目錄合併至 `.cursor/automations/benny/` 的目標位置。它必須保留僅存在於目標端的檔案，並審查衝突，而不是覆寫本機編輯。
3. 讓設定流程在目標儲存庫的 `.cursor/settings.json` 中啟用 pstack，以處理共用相依性：

```json
{
	"plugins": {
		"pstack": { "enabled": true }
	}
}
```

4. 將使用者擁有的設定保留在複製的套件之外，例如放在 `.cursor/benny/`。請調整 [`configuration.example.yaml`](./templates/configuration.example.yaml) 與 [`feature-map.example.md`](./skills/reproduce-and-fix-issues/references/feature-map.example.md)。
5. 在啟用任何自動化之前，請先提交 `.cursor/settings.json`、`.cursor/automations/benny/`，以及任何不含機密的設定。
6. 在編輯器中審查每個新的自動化草稿，或更新現有的自動化。然後傳送一份無害的測試報告，並驗證每個來源頻道的貼文都保留在原始討論串中。
