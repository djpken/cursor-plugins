<a id="feature-map-example"></a>

# 功能地圖範例

對映 Benny 可能重現的所有使用者端功能。在操作應用程式之前，請先閱讀相關章節。此地圖應從使用者觀點出發。內部機制與目前的程式碼路徑應在執行階段發現，而非在此固定下來。

將此檔案複製到 `.cursor/automations/benny/` 之外，例如複製到 `.cursor/benny/feature-map.md`，並將 `control.feature_map_path` 設定為該副本。Pack 更新不得覆寫此檔案。

<a id="per-feature-template"></a>

## 單一功能範本

### `<feature name>`

`<one-line user-visible purpose>`

<a id="how-a-user-gets-there"></a>

#### 使用者如何到達此處

- 點選路徑：`<screen> -> <menu, tab, or panel> -> <control>`
- 鍵盤快捷鍵：`<shortcut or none>`

<a id="how-the-control-adapter-drives-it"></a>

#### 控制介面卡如何驅動此功能

- `<adapter action>` 搭配 `<inputs>` 應該 `<visible result>`。
- 重設：`<how the adapter returns to a fresh state>`。

<a id="stable-selectors"></a>

#### 穩定的選擇器

- `<role and accessible name>`
- `<ARIA relationship>`
- `<data-component or purpose-named data attribute>`

切勿使用產生的 CSS 或 StyleX 類別、動態雜湊值、子元素索引，或不穩固的 DOM 位置。

<a id="states-to-exercise"></a>

#### 需測試的狀態

- 預設、滑鼠懸停、焦點可見、啟用、停用
- 載入中、空白、錯誤
- 已選取、開啟、展開
- `<relevant feature-specific variants>`

標記不適用於此功能的狀態。

<a id="preconditions-and-setup"></a>

#### 前置條件與設定

- 驗證：`<account state>`
- 資料：`<fixture>`
- 許可權：`<role>`
- 旗標：`<flag or none>`
- 服務：`<required availability>`

<a id="evidence-and-cross-check"></a>

#### 證據與交叉驗證

- 螢幕截圖：`<app identity, feature, and discriminating state>`
- 影片：`<entry path, interaction, and final state>`
- 交叉驗證：`<read-only state or value that confirms the UI>`

<a id="gotchas"></a>

#### 注意事項

- `<known dead end or wrong surface>`
- `<safe environment translation>`

<a id="fictional-example"></a>

## 虛構範例

這些功能屬於一個虛構的任務應用程式。它們僅為範例，並非 Benny 的必要功能。

<a id="sign-in"></a>

### 登入

讓使用者進入任務應用程式。

<a id="how-a-user-gets-there-1"></a>

#### 使用者如何到達此處

- 開啟應用程式並選擇 `Sign in`。無快捷鍵。

<a id="how-the-control-adapter-drives-it-1"></a>

#### 控制介面卡如何驅動此功能

- `open_app`、`click Sign in`、`fill credentials` 和 `click Continue` 應該開啟專案清單。
- 透過登出並清除一次性工作階段來重設。

<a id="stable-selectors-1"></a>

#### 穩定的選擇器

- 按鈕 `Sign in`、文字方塊 `Email` 和 `Password`、`data-component="sign-in-form"`

<a id="states-to-exercise-1"></a>

#### 需測試的狀態

- 預設、焦點可見、提交中、停用、載入中、錯誤

<a id="preconditions-and-setup-1"></a>

#### 前置條件與設定

- 一次性帳戶與可用的驗證服務

<a id="evidence-and-cross-check-1"></a>

#### 證據與交叉驗證

- 記錄從登入頁面到專案清單的過程。檢查唯讀工作階段狀態。

<a id="gotchas-1"></a>

#### 注意事項

- 行銷頁面是錯誤的介面。缺少驗證服務會導致阻塞。

<a id="item-list-and-detail"></a>

### 專案清單與詳細資訊

讓使用者瀏覽專案並開啟其中一個。

<a id="how-a-user-gets-there-2"></a>

#### 使用者如何到達此處

- 開啟 `Items` 分頁，然後選擇一行。

<a id="how-the-control-adapter-drives-it-2"></a>

#### 控制介面卡如何驅動此功能

- `select_tab Items` 和 `click <fixture item>` 應該開啟其詳細資訊。
- 透過關閉詳細資訊並清除選取狀態來重設。

<a id="stable-selectors-2"></a>

#### 穩定的選擇器

- 名為 `Items` 的分頁與清單、以 fixture 命名的行、`data-component="item-detail"`

<a id="states-to-exercise-2"></a>

#### 需測試的狀態

- 載入中、空白、錯誤、已選取、開啟、展開

<a id="preconditions-and-setup-2"></a>

#### 前置條件與設定

- 具名的 fixture 專案、讀取許可權、可用的專案服務

<a id="evidence-and-cross-check-2"></a>

#### 證據與交叉驗證

- 顯示選取狀態與對應的詳細資訊標題。檢查已選取專案的 ID。

<a id="gotchas-2"></a>

#### 注意事項

- 搜尋結果可能看起來相似，但使用不同的路徑。

<a id="item-editor"></a>

### 專案編輯器

讓使用者建立或編輯專案。

<a id="how-a-user-gets-there-3"></a>

#### 使用者如何到達此處

- 從詳細資訊選擇 `Edit`，或從清單選擇 `New item`。

<a id="how-the-control-adapter-drives-it-3"></a>

#### 控制介面卡如何驅動此功能

- `click Edit`、`fill <field>` 和 `click Save` 應該更新詳細資訊。
- 透過還原 fixture 來重設。

<a id="stable-selectors-3"></a>

#### 穩定的選擇器

- 按鈕 `Edit`、`New item`、`Save`、表單 `Item editor`、與標籤連結的欄位

<a id="states-to-exercise-3"></a>

#### 需測試的狀態

- 預設、焦點可見、已修改、驗證中、停用、儲存中、錯誤、成功

<a id="preconditions-and-setup-3"></a>

#### 前置條件與設定

- 可編輯的 fixture、寫入許可權、可用的儲存服務

<a id="evidence-and-cross-check-3"></a>

#### 證據與交叉驗證

- 顯示欄位變更至更新後的詳細資訊。以唯讀方式檢查儲存的專案值。

<a id="gotchas-3"></a>

#### 注意事項

- 不要注入表單狀態。唯讀的詳細資訊欄位不是編輯器。

<a id="settings"></a>

### 設定

讓使用者變更個人偏好設定。

<a id="how-a-user-gets-there-4"></a>

#### 使用者如何到達此處

- 開啟個人檔案選單，然後選擇 `Settings`。

<a id="how-the-control-adapter-drives-it-4"></a>

#### 控制介面卡如何驅動此功能

- `open_menu Profile`、`click Settings` 和 `toggle <preference>` 應該更新控制項。
- 透過還原起始偏好設定來重設。

<a id="stable-selectors-4"></a>

#### 穩定的選擇器

- 按鈕 `Profile`、選單專案 `Settings`、區域 `Settings`、以用途命名的偏好設定屬性

<a id="states-to-exercise-4"></a>

#### 需測試的狀態

- 關閉、開啟、已選取、焦點可見、停用、載入中、錯誤

<a id="preconditions-and-setup-4"></a>

#### 前置條件與設定

- 已登入的測試帳戶、已知的偏好設定、可用的偏好設定服務

<a id="evidence-and-cross-check-4"></a>

#### 證據與交叉驗證

- 顯示選單路徑與最終控制項狀態。以唯讀方式檢查偏好設定值。

<a id="gotchas-4"></a>

#### 注意事項

- 作業系統設定是不同的介面。

<a id="completeness-checklist"></a>

## 完整性檢查清單

- 每個可重現的使用者端功能都有對應的章節。
- 每個章節都指定了使用者路徑、介面卡動作與重設方式。
- 選擇器使用角色、名稱、ARIA、穩定的元件標記，或以用途命名的屬性。
- 沒有選擇器使用產生的類別或 DOM 位置。
- 涵蓋相關的互動、載入、空白、錯誤、已選取與展開狀態。
- 驗證、fixture、許可權、旗標與服務均明確列出。
- 螢幕截圖、影片與底層交叉驗證需求均明確列出。
- 列出錯誤的介面、死路以及安全環境的對應專案。
- 實作細節保留為執行階段的發現專案。
