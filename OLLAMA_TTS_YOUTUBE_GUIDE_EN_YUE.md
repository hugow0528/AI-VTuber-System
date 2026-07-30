# AI-VTuber-System Guide: Ollama (Local AI), TTS, and YouTube (English + Cantonese)

> This guide is based on the current repository state.  
> Current built-in providers are:
> - LLM: `Gemini` / `GPT`
> - TTS: `EdgeTTS` / `OpenAITTS`
> - YouTube: live chat connection (streaming itself is done by OBS)

---

## 1) Can I use Ollama local AI (Gemma) with this project?

### English
Yes, but **not as a native Ollama option in the GUI** right now.  
You can still use Ollama by running it in **OpenAI-compatible API mode** and mapping the model name to one this project already sends (for example `gpt-4o`).

### 廣東話（Cantonese）
可以，但而家個 GUI **未有原生 Ollama 選項**。  
你可以用 Ollama 嘅 **OpenAI 相容 API** 方式，再將模型名對應到本專案會送出嘅名稱（例如 `gpt-4o`）。

---

## 2) Ollama + Gemma setup (step-by-step)

### English
1. **Install Ollama**
   - https://ollama.com/download

2. **Start Ollama service**
   - macOS/Linux:
     ```bash
     ollama serve
     ```
   - Windows: usually starts as a background service after install.

3. **Pull Gemma model**
   ```bash
   ollama pull gemma4:latest
   ```
   If `gemma4` tag is unavailable in your Ollama version, run:
   ```bash
   ollama list
   ```
   and choose an available Gemma tag.

4. **Create model alias expected by this repo (`gpt-4o`)**
   ```bash
   ollama cp gemma4:latest gpt-4o
   ```
   If you use a different Gemma tag, replace source model accordingly.

5. **Set API base URL so OpenAI SDK points to Ollama**
   - macOS/Linux:
     ```bash
     export OPENAI_BASE_URL=http://127.0.0.1:11434/v1
     ```
   - Windows PowerShell:
     ```powershell
     $env:OPENAI_BASE_URL="http://127.0.0.1:11434/v1"
     ```

6. **Set an API key in project config (dummy value is fine for local Ollama)**
   - Edit `AIVT_Config.py`:
     ```python
     openai_api_key = "ollama"
     ```

7. **Set project to use GPT path**
   - In GUI/config: set `[LLM] using = GPT`
   - Keep model as `gpt-4o` (which now points to your local Gemma alias).

8. **Run the app**
   - Your GPT calls should now go to local Ollama (Gemma).

### 廣東話（Cantonese）
1. **安裝 Ollama**
   - https://ollama.com/download

2. **開 Ollama 服務**
   - macOS/Linux：
     ```bash
     ollama serve
     ```
   - Windows：通常安裝完會做背景服務。

3. **下載 Gemma 模型**
   ```bash
   ollama pull gemma4:latest
   ```
   如果你嗰個版本冇 `gemma4` tag，就：
   ```bash
   ollama list
   ```
   揀返有嘅 Gemma tag。

4. **建立本專案可用模型別名（`gpt-4o`）**
   ```bash
   ollama cp gemma4:latest gpt-4o
   ```
   如果你用其他 Gemma tag，改返來源名就得。

5. **設定 API Base URL 去 Ollama**
   - macOS/Linux：
     ```bash
     export OPENAI_BASE_URL=http://127.0.0.1:11434/v1
     ```
   - Windows PowerShell：
     ```powershell
     $env:OPENAI_BASE_URL="http://127.0.0.1:11434/v1"
     ```

6. **設定專案 API Key（本地可用假值）**
   - 編輯 `AIVT_Config.py`：
     ```python
     openai_api_key = "ollama"
     ```

7. **將 LLM 切去 GPT 路徑**
   - GUI/config 設定 `[LLM] using = GPT`
   - model 保持 `gpt-4o`（而家已經對應你本地 Gemma）。

8. **啟動程式**
   - 之後 GPT 請求就會打去本地 Ollama（Gemma）。

---

## 2A) Detailed end-to-end guide: Ollama Gemma for text generation in this app

### English (Full Step-by-Step)

This section is a deeper, practical walkthrough specifically for **text generation** using Ollama + Gemma with this repository.

### Step 0 — Understand the current app routing
- This app currently supports LLM providers: `Gemini` and `GPT`.
- To use Ollama, you must route through the existing `GPT` path (OpenAI-compatible endpoint).
- In short: **App -> OpenAI SDK calls -> Ollama local endpoint**.

### Step 1 — Install Ollama and confirm it works
1. Install from: https://ollama.com/download
2. Check version:
   ```bash
   ollama --version
   ```
3. Start service (if not auto-started):
   ```bash
   ollama serve
   ```
4. In another terminal, health-check:
   ```bash
   curl http://127.0.0.1:11434/api/tags
   ```
   You should get JSON back (even if model list is empty).

### Step 2 — Pull and test Gemma model directly in Ollama
1. Pull Gemma:
   ```bash
   ollama pull gemma4:latest
   ```
   If unavailable on your setup, list available local models after pull attempts:
   ```bash
   ollama list
   ```
2. Quick local test:
   ```bash
   ollama run gemma4:latest "Reply with: Ollama Gemma is ready."
   ```
   If this works, local inference is ready.

### Step 3 — Create model alias expected by this project
The app sends model names like `gpt-4o` in GPT mode.  
Create a local alias so Ollama can resolve that name:

```bash
ollama cp gemma4:latest gpt-4o
```

Verify alias exists:
```bash
ollama list
```
You should see `gpt-4o` in the model list.

### Step 4 — Point OpenAI-compatible client calls to Ollama
Set this in the same shell/session where you launch the app:

- macOS / Linux:
  ```bash
  export OPENAI_BASE_URL=http://127.0.0.1:11434/v1
  ```
- Windows PowerShell:
  ```powershell
  $env:OPENAI_BASE_URL="http://127.0.0.1:11434/v1"
  ```
- Windows CMD:
  ```cmd
  set OPENAI_BASE_URL=http://127.0.0.1:11434/v1
  ```

Optional persistence:
- Linux/macOS: add the `export` line to `~/.bashrc` or `~/.zshrc`
- Windows PowerShell (new sessions):
  ```powershell
  setx OPENAI_BASE_URL "http://127.0.0.1:11434/v1"
  ```

### Step 5 — Configure repository API key field
Edit:
- `AIVT_Config.py`

Set:
```python
openai_api_key = "ollama"
```

For local Ollama, this is usually a placeholder token.

### Step 6 — Set app to GPT mode (required)
In GUI or `GUI_control_panel/GUI_config.ini`:

```ini
[LLM]
using = GPT

[LLM_GPT]
model = gpt-4o
```

Notes:
- `model = gpt-4o` should match your alias created in Step 3.
- Keep timeout/retry reasonable for local inference (for example timeout 30, retry 1~2).

### Step 7 — Preflight API test (before launching app)
Run an OpenAI-compatible chat completion directly against Ollama:

```bash
curl http://127.0.0.1:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ollama" \
  -d '{
    "model": "gpt-4o",
    "messages": [{"role":"user","content":"Say hello from Gemma via Ollama."}],
    "temperature": 0.7
  }'
```

If you get JSON with `choices`, the endpoint mapping is correct.

### Step 8 — Launch app from the same environment
Important: launch the app in the same terminal/session where `OPENAI_BASE_URL` is set.

Then send a user text input in the app and confirm:
- response is generated
- output speed/latency is acceptable
- no fallback errors in console

### Step 9 — Tuning for Gemma local inference
Recommended practical tuning:
- `LLM_GPT.temperature`: `0.6 ~ 0.9`
- `LLM_GPT.max_output_tokens`: start at `256` or `512`
- `LLM_GPT.timeout`: increase to `30~60` if hardware is slower
- `LLM_GPT.retry`: `1~2` for local inference

If responses are cut too early:
- increase `max_output_tokens`

If responses are too slow:
- reduce `max_output_tokens`
- use a smaller Gemma variant

### Step 10 — Common failure cases and fixes

1. **Error: model not found (`gpt-4o`)**
   - Fix:
     ```bash
     ollama cp gemma4:latest gpt-4o
     ollama list
     ```

2. **App still calling cloud OpenAI instead of local**
   - Ensure `OPENAI_BASE_URL` is exported in the launch shell
   - Restart terminal/app after setting env var

3. **Connection refused to `127.0.0.1:11434`**
   - Start service:
     ```bash
     ollama serve
     ```
   - Confirm port is reachable

4. **Blank response in app**
   - Increase timeout
   - Reduce prompt length / max tokens
   - Verify curl test in Step 7 works first

5. **High latency**
   - Use smaller model size
   - Lower output token budget
   - Ensure no other heavy workloads are consuming CPU/GPU

### Step 11 — Minimal rollback path
If you want to switch back quickly:
1. In GUI/config set `[LLM] using = Gemini` (or normal GPT cloud path)
2. Remove/ignore `OPENAI_BASE_URL` in your launch environment
3. Restart app

---

## 3) TTS setup and external TTS options

### English
### Built-in TTS in this repo
1. **EdgeTTS (recommended quick start)**
   - In config: `[TextToSpeech] using = EdgeTTS`
   - Tune voice/pitch/rate/volume under `[EdgeTTS]`.

2. **OpenAITTS**
   - In config: `[TextToSpeech] using = OpenAITTS`
   - Set `openai_api_key` in `AIVT_Config.py`
   - Configure model/voice/speed under `[OpenAITTS]`.

### Can I connect external TTS model?
Yes.
- **Directly supported now:** services compatible with the OpenAI speech API path used by `TextToSpeech/OpenAITTS.py` (best effort).
- **For other engines:** add a custom TTS module and call it in `AI_Vtuber_UI.py` (`tts_request_thread`).

### Recommended external/local TTS engines for VTuber
- **Piper**: very fast local CPU TTS, low latency.
- **Style-Bert-VITS2 / GPT-SoVITS**: expressive voice, better character style.
- **Coqui XTTS v2**: multilingual + voice cloning workflow.

### 廣東話（Cantonese）
### 專案內建 TTS
1. **EdgeTTS（最快上手，建議先用）**
   - config：`[TextToSpeech] using = EdgeTTS`
   - 喺 `[EdgeTTS]` 調 voice / pitch / rate / volume。

2. **OpenAITTS**
   - config：`[TextToSpeech] using = OpenAITTS`
   - `AIVT_Config.py` 設 `openai_api_key`
   - 喺 `[OpenAITTS]` 調 model / voice / speed。

### 可唔可以接外部 TTS 模型？
可以。
- **而家直接最容易：** OpenAI speech API 相容服務（`TextToSpeech/OpenAITTS.py` 條路）。
- **其他引擎：** 需要加自訂 TTS module，再喺 `AI_Vtuber_UI.py` 的 `tts_request_thread` 接入。

### VTuber 外部/本地 TTS 推介
- **Piper**：本地 CPU 快、延遲低。
- **Style-Bert-VITS2 / GPT-SoVITS**：情感同角色感較好。
- **Coqui XTTS v2**：多語言＋聲線複製方案成熟。

---

## 4) YouTube: how to stream and connect

### English
Important: this project reads/responds to YouTube live chat; **actual streaming is done by OBS**.

1. **Create YouTube live event**
   - YouTube Studio → Go Live
   - Copy stream key
   - Get live video ID (from live URL).

2. **Set OBS streaming**
   - OBS → Settings → Stream → YouTube
   - Enter stream key, start stream.

3. **Connect this app to OBS websocket**
   - Edit `AIVT_Config.py`:
     - `OBS_WebSockets_ip`
     - `OBS_WebSockets_port` (default 4455)
     - `OBS_WebSockets_password`

4. **Set YouTube live chat in app**
   - In GUI/config (`GUI_control_panel/GUI_config.ini`, `[Live_Chat]`):
     - `yt_live_id = <your live id>`
     - `yt_channel_name = <your channel name>`
     - Enable toggles like:
       - `yt_response_chatroom`
       - `yt_live_chat_read_chat_now`
       - `yt_response_owner` / `yt_response_vip` / `yt_response_individual`

5. **Run and verify**
   - Start AI-VTuber app
   - Start OBS stream
   - Send a test message in YouTube chat
   - Confirm app receives chat and replies according to your live chat settings.

### 廣東話（Cantonese）
重點：本專案主要係讀/回 YouTube live chat；**真正推流係由 OBS 做**。

1. **開 YouTube 直播活動**
   - YouTube Studio → Go Live
   - 複製 stream key
   - 喺直播 URL 拎 live video ID。

2. **OBS 設定推流**
   - OBS → Settings → Stream → YouTube
   - 輸入 stream key，開始直播。

3. **本程式連 OBS websocket**
   - 改 `AIVT_Config.py`：
     - `OBS_WebSockets_ip`
     - `OBS_WebSockets_port`（預設 4455）
     - `OBS_WebSockets_password`

4. **喺程式設定 YouTube live chat**
   - GUI/config（`GUI_control_panel/GUI_config.ini`，`[Live_Chat]`）：
     - `yt_live_id = <你個 live id>`
     - `yt_channel_name = <你個 channel 名>`
     - 可按需要開：
       - `yt_response_chatroom`
       - `yt_live_chat_read_chat_now`
       - `yt_response_owner` / `yt_response_vip` / `yt_response_individual`

5. **啟動並測試**
   - 開 AI-VTuber
   - 開 OBS 推流
   - 喺 YouTube chat 試打一段字
   - 睇下程式有冇收到同按設定回應。

---

## 5) Troubleshooting

### English
- **Ollama not responding**
  - Check `ollama serve` is running.
  - Test `http://127.0.0.1:11434`.
- **Model not found (`gpt-4o`)**
  - Re-run model alias:
    ```bash
    ollama cp <your-gemma-tag> gpt-4o
    ```
- **No TTS sound**
  - Verify output/input devices in `GUI_config.ini`.
  - Try EdgeTTS first to confirm pipeline.
- **No YouTube chat**
  - Recheck `yt_live_id`.
  - Confirm stream/live status is public and active.

### 廣東話（Cantonese）
- **Ollama 冇反應**
  - 確認 `ollama serve` 有跑緊。
  - 測試 `http://127.0.0.1:11434` 可唔可達。
- **搵唔到模型（`gpt-4o`）**
  - 重新做 alias：
    ```bash
    ollama cp <你嘅-gemma-tag> gpt-4o
    ```
- **TTS 冇聲**
  - 檢查 `GUI_config.ini` 音訊裝置設定。
  - 先用 EdgeTTS 測試整條流程。
- **YouTube chat 收唔到**
  - 再核對 `yt_live_id`。
  - 確認直播係公開同已經開始。

---

## 6) Practical recommendation (quick path)

### English
For stable first run:
1. Use `LLM = GPT` + Ollama alias (`gpt-4o` -> Gemma local)
2. Use `TTS = EdgeTTS`
3. Stream with OBS, and only use this app for chat-read/response + subtitles/VTuber actions

### 廣東話（Cantonese）
想最快穩定跑到：
1. `LLM = GPT` + Ollama alias（`gpt-4o` -> 本地 Gemma）
2. `TTS = EdgeTTS`
3. OBS 負責推流，本程式集中做 chat 回應、字幕同 VTuber 動作
