<a id="routing-map-example"></a>

# 路由地圖範例

將此檔案複製到 `.cursor/automations/benny/` 之外，例如複製到 `.cursor/benny/routing.md`，並取代所有佔位符。將 `routing.map_path` 指向該複製檔。Pack 更新時不得覆寫此檔案。

分診 skill 將此視為資料。路由需要來自報告或原因追蹤的證據。僅憑關鍵字比對並不足夠。

```yaml
routes:
  - name: "billing-example"
    match:
      product_areas:
        - "billing-area-placeholder"
      code_paths:
        - "billing-code-path-placeholder"
      error_signatures:
        - "billing-error-placeholder"
    destination:
      slack_channel: "billing-channel-placeholder"
      tracker_team: "billing-team-placeholder"
    owners:
      - "billing-owner-placeholder"
    allow_feature_owner_ping: false

  - name: "desktop-example"
    match:
      product_areas:
        - "desktop-area-placeholder"
      code_paths:
        - "desktop-code-path-placeholder"
      error_signatures:
        - "desktop-error-placeholder"
    destination:
      slack_channel: "desktop-channel-placeholder"
      tracker_team: "desktop-team-placeholder"
    owners:
      - "desktop-owner-placeholder"
    allow_feature_owner_ping: false

fallback:
  destination: ""
  owners: []
  allow_feature_owner_ping: false

ping_policy:
  default: "off"
  allow:
    - "configured-feature-owner"
    - "confirmed-regression-author"
  deny:
    - "broad-on-call-group"
    - "unverified-owner"
```

<a id="rules"></a>

## 規則

- 除非有一個團隊接受所有未匹配的報告，否則請將 `fallback.destination` 留空。
- 使用穩定的產品領域、程式碼路徑與錯誤特徵。
- 請勿在公開複製檔中包含私人資料。
- 請勿將原始的使用者或頻道 ID 貼上到即將發布的範例中。
- 在目標團隊同意之前，請保持功能負責人通知功能關閉。
- 重新路由會告知報告者應前往哪裡。自動化系統絕不會交叉發布。
