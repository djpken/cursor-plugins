---
name: debug-voice
description: >-
  當使用者執行 /debug-voice、表示語音模式存在缺陷、要求檢視或
  擷取 Grok 即時語音會話中發生的情況，或語音整合
  尚未具備除錯記錄時使用。先提出計畫，然後在應用程式自身的語言與
  慣例中安裝僅供開發使用的記錄管線（客戶端記錄器 → 本地 NDJSON），
  接著執行修正迴圈：將使用者的報告與記錄
  特徵比對，修正一項問題，重新測試。
---

<a id="debug-voice"></a>

# 語音除錯

讓語音會話在事後可讀，然後依據證據進行修正。Agent 無法聽見應用程式；記錄是它的耳朵，使用者是它的裁判。不記錄音訊、不記錄 token，絕不在生產環境啟用。

適用於任何技術棧。管線是一個小型合約（見下文）；請使用應用程式現有的技術來實作。

<a id="workflow"></a>

## 工作流程

1. **對映** 應用程式（唯讀）。
2. **計畫**：撰寫變更清單，展示出來，**停止**。在使用者確認一致之前，不進行任何編輯。
3. **安裝** 已同意的元件，遵循應用程式的語言、框架與慣例。
4. **驗證** 接收端（sink），將應用程式交給使用者。
5. **修正迴圈** 依據記錄進行。

<a id="1-map"></a>

## 1. 對映

找出並記錄以下路徑：

- 語音客戶端：接收與傳送即時事件的位置、麥克風擷取、音訊播放、token 擷取。
- 伺服器：框架、路由宣告方式、共用伺服器程式碼位置、環境變數讀取方式、此處「生產環境」的定義。
- 慣例：語言、模組系統、格式化器、指令碼或任務位置（`package.json`、`Makefile`、`pyproject`、`justfile`）、`.gitignore`。
- 客戶端型別：瀏覽器、行動裝置、桌面、CLI。非瀏覽器客戶端仍會 POST 相同的 JSON；單一程式應用程式可跳過 HTTP，直接附加到檔案。
- 音訊增量（audio deltas）處理位置。必須計數，絕不記錄。

<a id="2-plan-then-stop"></a>

## 2. 計畫，然後停止

填入實際路徑與應用程式語言，發布出來，並等待確認或刪減後的清單。在此之前不要編輯檔案。

```markdown
## Debug voice: plan

Add
- <path>: client logger (batch, redact, flush) in <language>
- <path>: dev-only sink `POST /api/voice/log` → `.voice-logs/<sessionId>.ndjson`
- <path> (optional): summary command `<cmd>`; otherwise read the NDJSON with jq

Modify
- <voice client file>: hook points start, token, mic, env, ws.*, client/server events,
  audio.in (2 s windows), audio.out.first, audio.out, play.stop, stop
- <token route>: append `server.token { ok, status, ms, upstream }` (never the token)
- <UI file>: session id in the voice status line and in voice error messages
- .gitignore: `/.voice-logs`
- <scripts file>: a `voice:logs` task (only if the summary command is wanted)

Logged: event names and non-audio fields, timings, byte counts, mic RMS.
Never: tokens, API keys, raw audio, strings over 400 chars.
Off in production unless `VOICE_LOG=1`.

Reply "go", or strike lines you do not want.
```

<a id="3-install-the-contract"></a>

## 3. 安裝：合約

與應用程式保持一致。使用與周圍程式碼相同的語言、相同的路由風格、相同的格式化器。根據下方合約撰寫元件；不要為記錄引入第二種語言或工具鏈。

**會話 ID**：來自 UUID 的 8 個小寫十六進位字元。接收端接受 `^[a-z0-9]{4,64}$`；它將成為檔案名稱。

**條目**（每行一個 JSON 物件）：

| 欄位 | 客戶端 | 伺服器 |
| --- | --- | --- |
| `t` | 記錄器啟動後的毫秒數 | 不存在；讀取器透過 `ts` 對齊 |
| `ts` | epoch 毫秒 | epoch 毫秒 |
| `kind` | `start`, `server`, `client`, `error`, `audio.in`, … | `server.token`, … |
| `src` | 不存在 | `"server"` |
| 其餘 | 鉤子的欄位，已遮蔽 | 鉤子的欄位 |

**遮蔽（在緩衝前於客戶端端套用）**：對音訊事件型別（`response.output_audio.delta`、`response.audio.delta`、`input_audio_buffer.append`），將 `delta` / `audio` 替換為 `bytes` = 解碼後的 base64 長度；超過 400 字元的字串截斷為 400 + `…[N chars]`；深度超過 4 的物件 → `"[depth]"`；超過 50 個專案的陣列截斷。

**客戶端記錄器**：緩衝條目；每 1 秒或每 200 個條目以 `POST <sink> {"sessionId","entries":[…]}` 格式重新整理；停止時使用 keepalive 重新整理（或平臺等價的「在導航後存活」機制）；吞掉所有傳輸錯誤，記錄絕不可向語音路徑丟擲異常。在開發環境中同時將條目映象輸出到主控臺。

**接收端**：`POST /api/voice/log`，JSON 主體。除非是開發環境或 `VOICE_LOG=1`，否則為 `404`。若 `sessionId` 未透過正規表示式驗證或 `entries` 不是陣列，則回傳 `400`。每次請求最多附加 500 個條目，丟棄超過 16,000 字元的行，寫入 `.voice-logs/<sessionId>.ndjson`，並建立目錄。回應 `204`。

任何伺服器的偽程式碼：

```
handle POST /api/voice/log:
  if production and VOICE_LOG != "1": return 404
  body = parse json or return 400
  if not regex(body.sessionId) or not list(body.entries): return 400
  mkdir .voice-logs; append join(json(e) for e in body.entries[:500] if len < 16000) to .voice-logs/{sessionId}.ndjson
  return 204
```

客戶端記錄器的偽程式碼：

```
logger(sessionId, sink):
  buffer = []; started = now()
  log(kind, data): buffer.push({ ...redact(data), t: now() - started, ts: epoch_ms(), kind }); schedule flush (1 s timer, or immediately at 200 entries)
  server(event, extra): log("server", { ...redact_event(event), ...extra })   # never per audio delta
  client(event):        log("client", redact_event(event))                    # never per audio chunk
  error(where, err, extra): log("error", { where, name, message, ...extra })
  flush(final=false): POST sink {"sessionId","entries": buffer}; buffer = []; ignore all errors; keepalive when final
  close(): flush(final=true)
```

<a id="hook-points"></a>

## 鉤子點

`kind` 及欄位；結構在所有語言中相同。

| 時機 | `kind` 及欄位 |
| --- | --- |
| 會話開始 | `start { url, target_rate }` |
| Token 擷取成功 / 失敗 | `token.ok { ms }` / `error { where: "token", name, message, ms }` |
| 麥克風許可權授予 / 拒絕 | `mic.ok { ms, label, settings }` / `error { where: "mic", … }` |
| 音訊圖形就緒 | `env { ua, mic_rate, mic_state, play_rate, play_state, capture_frames, target_rate }` |
| Socket | `ws.connecting`、`ws.open { ms }`、`ws.error`、`ws.close { code, reason, wasClean, by }` |
| 傳送的每個事件（音訊區塊除外） | `client { …redacted event }` |
| 接收的每個事件（音訊增量除外） | `server { …redacted event, phase }` |
| 階段變更（去重） | `phase { phase }` |
| 麥克風區塊，每 2 秒聚合 | `audio.in { chunks, bytes, rms_max, rms_avg, pending, mic_state, phase }` |
| 開啟時傳送的預開啟緩衝 | `audio.flush { chunks }` |
| 回應中的第一個音訊增量 | `audio.out.first { response_id, bytes, since_response_created_ms, since_speech_stopped_ms, play_state }` |
| `response.done` | `audio.out { response_id, status, deltas, bytes, audio_ms, wall_ms, max_gap_ms, queued_ms, underruns, drain_ms_max }` |
| 打斷（Barge-in）停止 | `play.stop { reason, dropped_ms }` |
| 使用者停止 | `stop { by: "client", phase }`，然後重新整理 |
| Token 路由（伺服器） | `server.token { ok, status, ms, upstream }` |

在訊息處理器中：如果事件是音訊增量，計數位元組與間隔並播放；否則以 `server` 記錄，包含目前階段，然後執行現有處理。從 `input_audio_buffer.speech_stopped` 中保留 `speechStoppedT`，從 `response.created` 中保留 `createdT`；在第一個增量時報告兩者距離。在播放器中，當回應中途下一個排程時間已過去時，計數一次 underrun，追蹤最大排空量，並在 `response.created` 時重置。

UI：在語音狀態列中顯示 ID（`Listening · session ab12cd34`），並將 `(voice session <id>)` 附加到語音錯誤中，以便使用者能指名該次執行。

<a id="4-verify"></a>

## 4. 驗證

```bash
curl -s -o /dev/null -w "%{http_code}\n" -X POST localhost:<port>/api/voice/log \
  -H 'Content-Type: application/json' \
  -d '{"sessionId":"smoke001","entries":[{"t":0,"ts":0,"kind":"start"}]}'   # 204
curl -s -o /dev/null -w "%{http_code}\n" -X POST localhost:<port>/api/voice/log \
  -H 'Content-Type: application/json' -d '{"sessionId":"../x","entries":[]}' # 400
cat .voice-logs/smoke001.ndjson && rm .voice-logs/smoke001.ndjson
```

然後交接：使用者在實際應用程式中測試，並報告他們說了什麼、聽到了什麼、何時出錯，以及會話 ID。

<a id="reading-a-log"></a>

## 讀取記錄

以下任一方式皆可；均不需要應用程式的工具鏈：

```bash
f=.voice-logs/<id>.ndjson
jq -r 'select(.kind|IN("start","token.ok","mic.ok","env","ws.open","ws.close","server.token","stop","error")) | "\(.t // .ts)ms \(.kind) \(.type // "") \(.message // "")"' $f   # milestones and errors
jq -r 'select(.kind=="server") | .type' $f | sort | uniq -c | sort -rn                      # server event counts
jq -c 'select(.kind|IN("audio.out.first","audio.out"))' $f                                  # per-turn latency, gaps, underruns
jq -c 'select(.kind=="audio.in")' $f                                                        # mic windows, rms
```

或直接讀取檔案；它是按時間順序排列的每行一個物件。伺服器條目僅有 `ts`；使用 `start` 條目的 `ts` 將其對齊到客戶端時鐘。如果使用者想要摘要指令，請用應用程式的語言撰寫一個，輸出：里程碑、伺服器事件計數、每個 `audio.out` 回合的一行及其 `audio.out.first` 延遲、麥克風視窗總計、錯誤與關閉，以及最後 25 個條目。

<a id="5-fix-loop"></a>

## 5. 修正迴圈

1. **儀器化**（步驟 1–4），如果應用程式尚未具備 `.voice-logs` 管線。
2. **使用者測試** 在實際應用程式中並描述該次執行。
3. **讀取記錄** 圍繞失敗的 `t`。
4. **比對** 症狀 → 特徵 → 修正（見下表）。沒有匹配的特徵：先增加記錄，重新測試，然後修正。
5. **修正一項問題**，重新測試，確認在新記錄中該特徵已消失。
6. **記錄下來**：在「從會話中確認」下方附加一行已確認的條目。如果修正改變了語音的建構方式，也更新 `/add-voice`。

<a id="symptom--log-signature--fix-starter-rows"></a>

## 症狀 → 記錄特徵 → 修正（起始行）

| 症狀（使用者） | 特徵（記錄） | 修正 |
| --- | --- | --- |
| 靜音，但出現文字記錄 | `env.play_state` 或 `audio.out.first.play_state` = `suspended` | 在使用者手勢內建立並恢復播放音訊上下文；每個會話一個上下文，而非每個回合一個 |
| 助理中斷自己 | `speech_started` 帶有 `phase=speaking`；`audio.in.rms_max` 僅在播放期間上升 | 回聲。用耳機確認（如果停止，就是回聲）。保持回聲消除開啟，降低喇叭音量，或在 `speaking` 期間閘控麥克風傳送 |
| 卡頓、斷續 | `audio.out.underruns` > 0，`drain_ms_max` 偏高，`max_gap_ms` 遠高於區塊長度 | 在第一個區塊播放前安排一小段預留時間（150–250 ms）；不要每個回合都重建音訊內容 |
| 爆音、音高或速度錯誤 | `session.update` 速率 ≠ 緩衝區速率；`bytes` 為奇數 | 全域使用單一速率（`audio.input/output.format.rate`、播放器緩衝區）；採用偶數位元組對齊 |
| 無法連線，或立即關閉 | `ws.close` 早於 `session.updated`；`server.token.ok=false` | 每次點選產生一個權杖（300 秒），協定為 `xai-client-secret.<token>`，URL 中包含 `model`；讀取 `server.token.status` 與 `upstream` |
| 麥克風無反應 | 每個視窗中 `audio.in.rms_max` ≈ 0；`mic.ok.label` 異常 | 裝置錯誤或作業系統許可權問題；檢查 `mic.ok.settings`、`label`、`mic_state` |
| 沒有使用者文字記錄 | 沒有 `conversation.item.input_audio_transcription.updated` | 在 `session.update` 中設定 `audio.input.transcription.model: "grok-transcribe"` |
| 第一個字延遲 | `audio.out.first.since_speech_stopped_ms` 偏高 | 嘗試 `reasoning.effort: "none"`；縮短 `instructions`；檢查 `token.ok.ms` 與 `ws.open.ms` 的連線成本 |
| 使用者文字在回覆之後才出現 | `...transcription.updated` `t` > `response.created` `t` | 在 `input_audio_buffer.committed`（`item_id`）時建立使用者列，在 `updated` 時填入內容 |
| 開頭幾個字被截斷 | `ws.open.ms` 較大，且 `audio.flush.chunks` 達到緩衝區上限 | 在套接字之前啟動麥克風，緩衝早期音訊，提高開啟前的上限 |

<a id="confirmed-from-sessions"></a>

## 已從工作階段確認

在修正於全新紀錄中驗證後附加。格式：`YYYY-MM-DD · symptom · signature · fix · file(s)`。

<a id="rules"></a>

## 規則

- 先規劃；在使用者對變更清單達成一致前，不要進行任何編輯。
- 絕不記錄權杖、金鑰或原始音訊。音訊僅記錄位元組數。
- 僅限開發環境。除非 `VOICE_LOG=1`，否則生產環境中該端點會回傳 404。`.voice-logs/` 已被 gitignore 忽略。
- 紀錄記錄絕不向語音路徑丟擲例外。
- 彙總音訊；絕不逐區塊記錄紀錄。
- 每次重測只改一處，以便紀錄顯示哪項修正有效。
- 不要杜撰 xAI 事件名稱；在新增簽名前，請在 https://docs.x.ai/developers/model-capabilities/audio/speech-to-speech 中確認。
