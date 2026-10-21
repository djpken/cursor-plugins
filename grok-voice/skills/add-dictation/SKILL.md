---
name: add-dictation
description: >-
  當使用者執行 /add-dictation 或希望將語音轉換為文字時使用
  Grok 語音轉文字：一個麥克風按鈕，可將口述內容輸入至編輯器、即時
  字幕，或轉錄已錄製的音訊（檔案、上傳、URL），並包含單字
  時間戳記、講者分離、字幕、會議記錄。STT、轉錄、
  轉錄文字。若要使用會回應的語音代理，請使用 /add-voice.
---

<a id="add-dictation"></a>

# 新增口述功能

將 Grok 語音轉文字新增至現有應用程式：一個麥克風按鈕，可將口述內容輸入至編輯器、即時字幕，或已錄製音訊的轉錄文字。在 `/add-dictation` 上執行，輸入 **Dictate**，或明確的「轉錄」意圖。Cursor 沒有麥克風；請連線 **應用程式**，而非 IDE。

<a id="docs"></a>

## 檔案

- https://docs.x.ai/developers/model-capabilities/audio/speech-to-text
- 模型選擇（請先閱讀）：https://docs.x.ai/developers/model-capabilities/audio/speech-to-text#model-selection
- 定價（僅引用檔案）：https://docs.x.ai/developers/pricing

<a id="model"></a>

## 模型

在任何 STT 呼叫之前，請閱讀模型選擇章節，並將**最新列出**的模型作為 `model` 傳遞。請勿捏造 ID 或重複使用此 skill 中過時的 ID。REST：在 `file` 之前的表單欄位。串流：查詢引數。官方範例目前傳送 `grok-voice-transcribe-2.0`。今日列出：`grok-voice-transcribe-2.0`（最佳）、`grok-voice-transcribe-1.0`（原始版本；當省略 `model` 時的 API 預設值）。

<a id="pick-the-path"></a>

## 選擇路徑

| 需求 | 路徑 |
| --- | --- |
| 點選、說話、點選、文字出現。上傳的檔案。URL。 | **批次** `POST https://api.x.ai/v1/stt`（預設） |
| 說話時文字出現：字幕、長篇口述、按下說話 | **串流** `wss://api.x.ai/v1/stt` 透過後端中繼 |

批次是編輯器麥克風按鈕的預設選項：單一請求、無 socket、金鑰永不離開伺服器。僅當 UX 需要暫定文字時才使用串流。

<a id="auth"></a>

## 驗證

- Bearer `XAI_API_KEY`，僅限伺服器端。STT 檔案未記載任何臨時金鑰流程，且瀏覽器無法設定 WebSocket 標頭，因此瀏覽器串流需透過您的後端中繼。請勿捏造金鑰流程。
- 切勿將金鑰放入客戶端 bundle。請勿在聊天中貼上金鑰。

<a id="steps"></a>

## 步驟

1. **對應應用程式**
   - 編輯器或輸入元件、文字應放置的位置（在遊標處插入 vs 取代）、伺服器框架、套件管理器。
   - **麥克風圖示屬於口述功能**。如果已安裝 `/add-voice`，其波形主要按鈕保持不變；在旁邊新增麥克風作為次要幽靈按鈕。
   - 現有的麥克風擷取？如果 `/add-voice` 已執行，其 PCM 擷取可供應串流 STT；將其速率作為 `sample_rate` 傳遞。16 kHz 是模型的原生速率；其他支援的速率（8000、16000、22050、24000、44100、48000）會在伺服器端重新取樣。

2. **批次路徑（預設）**
   - 客戶端：`MediaRecorder` → `Blob` → `POST` 至您自己的路由。端點會自動偵測容器（WAV、MP3、OGG、Opus、FLAC、AAC、MP4、M4A、MKV、WebM），因此請傳送 `MediaRecorder` 產生的任何內容。
   - 伺服器：作為 `multipart/form-data` 轉發。選項欄位優先，**`file` 最後**；`file` 之後的欄位可能被忽略。`file` 或 `url`，最大 500 MB。`model` 來自檔案（最新列出）。

```ts
// server (any runtime with fetch + FormData)
export async function transcribe(blob: Blob, filename: string) {
  const form = new FormData();
  form.append("model", "grok-voice-transcribe-2.0"); // latest from docs; re-read model-selection
  form.append("format", "true");     // written-form numbers/currency; requires language
  form.append("language", "en");
  // form.append("keyterm", "Acme"); // repeat per term, ≤100 terms × 50 chars
  form.append("file", blob, filename); // last
  const res = await fetch("https://api.x.ai/v1/stt", {
    method: "POST",
    headers: { Authorization: `Bearer ${process.env.XAI_API_KEY}` },
    body: form,
  });
  if (!res.ok) throw new Error(`STT ${res.status}`); // 400 bad input, 413 >500 MB, 429 back off, 502 url fetch failed, 503 retry
  return (await res.json()) as {
    text: string; language: string; duration: number;
    words?: { text: string; start: number; end: number; speaker?: number }[];
    channels?: { index: number; text: string; words: unknown[] }[];
  };
}
```

```ts
// client
const mime = MediaRecorder.isTypeSupported("audio/webm;codecs=opus") ? "audio/webm;codecs=opus" : "audio/mp4";
const rec = new MediaRecorder(stream, { mimeType: mime });
const parts: BlobPart[] = [];
rec.ondataavailable = (e) => parts.push(e.data);
rec.onstop = async () => {
  const fd = new FormData();
  fd.append("file", new Blob(parts, { type: mime }), "dictation");
  const { text } = await (await fetch("/api/dictation", { method: "POST", body: fd })).json();
  insertAtCursor(text);
};
rec.start(); // second tap: rec.stop()
```

3. **串流路徑**
   - 中繼：伺服器持有金鑰，升級瀏覽器 socket，向上轉發二進位框架和客戶端控制訊息，向下傳送 JSON 事件。在伺服器端建立查詢字串。`model` 來自檔案（最新列出）。

```ts
import { WebSocketServer, WebSocket } from "ws";

new WebSocketServer({ port: 8788 }).on("connection", (client) => {
  const q = new URLSearchParams({ model: "grok-voice-transcribe-2.0", sample_rate: "16000", encoding: "pcm", interim_results: "true", language: "en" });
  const up = new WebSocket(`wss://api.x.ai/v1/stt?${q}`, { headers: { Authorization: `Bearer ${process.env.XAI_API_KEY}` } });
  up.on("message", (d) => client.send(d.toString()));                       // transcript.* and error events
  client.on("message", (d, isBinary) => up.readyState === WebSocket.OPEN && up.send(d, { binary: isBinary })); // audio + finalize/audio.done
  const end = () => { client.close(); up.close(); };
  up.on("close", end); up.on("error", end); client.on("close", end);
});
```

   - 瀏覽器擷取：PCM16 小端、單聲道、16 kHz、**100 ms 框架 = 3,200 位元組**、原始二進位、無 base64。在傳送前等待 `transcript.created`。`MediaRecorder` 輸出是容器，而非原始框架；請勿串流它。

```ts
const ws = new WebSocket(relayUrl); ws.binaryType = "arraybuffer";
const ctx = new AudioContext({ sampleRate: 16000 }); // if ctx.sampleRate !== 16000, downsample in the worklet
await ctx.audioWorklet.addModule("/pcm16-worklet.js"); // Float32 → Int16LE, posts one 3,200-byte frame per 100 ms
const node = new AudioWorkletNode(ctx, "pcm16");
ctx.createMediaStreamSource(stream).connect(node);
let ready = false;
node.port.onmessage = (e) => ready && ws.readyState === WebSocket.OPEN && ws.send(e.data);

let committed = "", locked = "", live = "";
ws.addEventListener("message", (e) => {
  const ev = JSON.parse(e.data);
  if (ev.type === "transcript.created") ready = true;
  else if (ev.type === "transcript.partial") {
    if (ev.speech_final) { committed += ev.text + " "; locked = ""; live = ""; } // complete stitched utterance
    else if (ev.is_final) { locked += ev.text + " "; live = ""; }                // chunk final: text will not change
    else live = ev.text;                                                          // interim: may change
    render(committed + locked + live);
  } else if (ev.type === "transcript.done") ws.close();                          // after audio.done
  else if (ev.type === "error") showError(ev.message);                           // most errors close the socket
});
// stop: ws.send(JSON.stringify({ type: "audio.done" }))
// push-to-talk release: ws.send(JSON.stringify({ type: "Finalize" })) then keep streaming (docs show both `finalize` and `Finalize`; the examples use `Finalize`)
```

4. **選項**（串流為查詢引數，批次為表單欄位）

| 想要 | 設定 |
| --- | --- |
| 最新 STT | 閱讀檔案 #model-selection，傳遞該 `model`。快照：`grok-voice-transcribe-2.0`。省略 → `grok-voice-transcribe-1.0`。批次：表單欄位。串流：查詢引數。 |
| 說話時顯示文字 | `interim_results=true` |
| 「one hundred dollars」 → `$100` | 串流：`language=en`；批次：`format=true` + `language=en` |
| 產品名稱、術語 | 重複 `keyterm=` |
| 口述數字時未在句子中途截斷 | `smart_turn=0.7&smart_turn_timeout=3000` |
| 較快或較慢的語句結束 | `endpointing=` ms，預設 400 |
| 誰說了什麼（會議） | `diarize=true` → `words[].speaker` |
| 代理和客戶在單獨的通道上 | `multichannel=true&channels=2`（僅限 PCM，非 Opus） |
| 保留「um」、「uh」 | `filler_words=true`（預設移除） |
| 低頻寬或行動裝置 | `encoding=opus`，每個框架精確一個原始 Opus 封包，省略 `sample_rate` |
| 原始音訊至批次 | `audio_format=pcm|mulaw|alaw` + `sample_rate` |
| 安靜或電話音訊 | 降低 `vad_threshold`（串流預設 0.08，批次 0.5） |

5. **Python 對應（僅當伺服器是 Python 時）**

```python
import os, requests
r = requests.post(
    "https://api.x.ai/v1/stt",
    headers={"Authorization": f"Bearer {os.environ['XAI_API_KEY']}"},
    data=[("model", "grok-voice-transcribe-2.0"), ("format", "true"), ("language", "en")],
    files={"file": ("dictation.webm", blob, "audio/webm")},  # requests sends data fields before files
)
r.raise_for_status(); text = r.json()["text"]
# streaming: websockets.connect(url, additional_headers={"Authorization": f"Bearer {key}"}); await ws.send(pcm_bytes)
```

6. **冒煙測試**
   - 批次：來自檔案的最新 `model`（目前為 `grok-voice-transcribe-2.0`）：`curl -X POST https://api.x.ai/v1/stt -H "Authorization: Bearer $XAI_API_KEY" -F model=grok-voice-transcribe-2.0 -F language=en -F file=@short.wav` → 200 並包含 `text`。使用 `-F format=true` 且無 `language` 的相同呼叫 → 400。
   - 串流：口述兩句話，中間有暫停。預期會有暫定文字，然後是最終文字；在語句邊界沒有重複或消失的單字（如果單字消失，拼接的 `speech_final` 文字未包含區塊最終文字：請附加而非取代 `locked`）。`audio.done` → `transcript.done`，socket 關閉。
   - 在客戶端 bundle 中搜尋 `XAI_API_KEY`；它不應存在。
   - 使用 `/debug-voice` 從紀錄除錯；將其掛鉤點切換為 `transcript.*` 事件。

<a id="out-of-scope"></a>

## 超出範圍

- 會回應的語音（`/add-voice`）、朗讀文字（`/add-read-aloud`）
- 捏造檔案未記載的 STT 金鑰流程、端點、模型 ID 或事件名稱
