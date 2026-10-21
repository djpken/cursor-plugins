<a id="create-a-note"></a>

# 建立備忘錄

建立備忘錄讓使用者可以從瀏覽器或 CLI 儲存一份帶有標題的備忘錄、取消未完成的草稿，並從第二個使用者介面確認已儲存的備忘錄。

<a id="sub-features"></a>

## 子功能

- `create-open` 從每個瀏覽器入口開啟空白編輯器。
- `create-save` 持久化標題和內容。
- `create-cancel` 捨棄未完成的瀏覽器草稿。
- `create-cli` 從終端機建立相同形狀的備忘錄。

<a id="how-to-get-to-it-user-pov"></a>

## 如何到達此處（使用者視角）

- 在瀏覽器工具列中選擇 `New note` 按鈕。
- 在瀏覽器中按下 `n`，前提是焦點不在可編輯欄位內。
- 在終端機中執行 `notes create --title <title> --body <body>`。

<a id="driving-it-with-control-notes"></a>

## 使用控制備忘錄進行操作

前置條件：

- Notes 在 `http://127.0.0.1:4173` 處於健康狀態。
- 沒有備忘錄的標題為 `Release checklist`。
- `control-notes doctor` 報告預期的 URL 和一次性資料目錄。

- **開啟編輯器。** 選擇 `New note`。執行 `control-notes browser click --role button --name "New note"`。出現一個名為 `Note editor` 的表單，焦點位於 `Title` 文字框中。
- **輸入內容。** 輸入標題和內容。執行 `control-notes browser fill --role textbox --name "Title" --value "Release checklist"` 和 `control-notes browser fill --role textbox --name "Body" --value "Tag and publish"`。`Save note` 按鈕變為啟用狀態。
- **儲存備忘錄。** 選擇 `Save note`。執行 `control-notes browser click --role button --name "Save note"`。出現一個名為 `Note saved` 的狀態，且標題顯示為 `Release checklist`。
- **確認持久化。** 返回備忘錄清單並重新開啟該備忘錄。執行 `control-notes browser click --role link --name "All notes"` 和 `control-notes browser click --role link --name "Release checklist"`。編輯器顯示兩個已儲存的值。
- **取消草稿。** 開啟新備忘錄，輸入 `Discard me`，並選擇 `Cancel`。執行 `control-notes browser click --role button --name "New note"`、`control-notes browser fill --role textbox --name "Title" --value "Discard me"` 和 `control-notes browser click --role button --name "Cancel"`。備忘錄清單返回，且沒有 `Discard me` 連結。
- **CLI 入口。** 建立第二個備忘錄。執行 `control-notes cli -- notes create --title "CLI note" --body "Created from terminal" --format json`。退出程式碼為 `0`，且標準輸出包含新的備忘錄 ID 和標題。
- **證明。** 從 `All notes` 重新開啟兩個已儲存的備忘錄。執行 `control-notes browser snapshot --aria --path artifacts/create-note/list.aria.txt` 和 `control-notes browser screenshot --path artifacts/create-note/list.png`。產出物顯示 `Release checklist` 和 `CLI note`。

<a id="gotchas"></a>

## 注意事項

- 當文字框具有焦點時按下 `n`，會輸入該字元，而不是開啟新的編輯器。
- 標題在儲存時會被修剪。請斷言渲染後的標題，而非草稿輸入值。
- 僅有儲存狀態不足以作為證明。請從清單中重新開啟備忘錄。
- 在測試資料清理期間移除 `Release checklist` 和 `CLI note`，但保留其證明產出物。
