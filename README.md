# Ayjan Minutes

Professional Windows meeting recorder with an intuitive GUI that captures mic + system audio simultaneously, then:
1. **Transcribes** using local `faster-whisper` models (no audio leaves your machine)
2. **Generates AI meeting minutes** as professionally-formatted Word documents (optional)

Records as stereo WAV, transcripts saved as plain text, and structured meeting minutes exported as Word documents with color-coded action items.

<img width="736" height="645" alt="Screenshot 2026-05-10 185815" src="https://github.com/user-attachments/assets/a1fa4830-26e3-4ffd-9131-0ff57e62fcd8" />

## Quick Start

### Download Installer
Download the latest installer from the [Website](https://ayj-systems.github.io/ayjanminutes/)

Run `Ayjan-Minutes-Setup.exe` — it's a small (~15 MB) installer that downloads and installs the full app. During setup you can optionally download the Whisper transcription model so it is ready before your first meeting. No Python or manual setup needed.

On first run, sign in or create a free account to get started.

## Account & Plans

A free account is required to use the app. Sign up with your email or use **Sign in with Google** for one-click login.

| Feature | Free | Pro |
|---|---|---|
| Recording & local transcription | Unlimited | Unlimited |
| AI meeting minutes (Built-in AI) | Monthly limit | Unlimited |

## AI Meeting Minutes

Meeting minutes are generated automatically after transcription. Three provider options are available under **Settings → AI Provider**:

| Provider | Key required | Notes |
|---|---|---|
| **Built-in AI** (default) | No | Works immediately after sign-in |
| Claude (Anthropic) | Yes | Enter your key from [console.anthropic.com](https://console.anthropic.com) |
| OpenAI | Yes | Enter your key from [platform.openai.com](https://platform.openai.com) |

Built-in AI is the default — no API key needed, just sign in and enable summarization in Settings.

To use Claude or OpenAI directly: go to **Settings → AI Provider**, select your provider, and enter your API key. The key is saved securely for future sessions.

## Main Features

### Recording Panel
- **Select Microphone** — Choose from available mics, or use default
- **Select Speaker** — Choose system audio source (captures meeting app audio, browser calls, etc.)
- **Record Button** — Start/stop recording with elapsed time display
- **Live Transcript** — Preview updates in real time as you record, so you're not waiting after you stop
- **Files are saved to:**
  - Audio: `Documents/ayjan_minutes/recordings/meeting_TIMESTAMP.wav`
  - Transcript: `Documents/ayjan_minutes/transcripts/raw/meeting_TIMESTAMP.txt`
  - Minutes: `Documents/ayjan_minutes/meeting_minutes/meeting_TIMESTAMP_minutes.docx` + `.txt`

### Recording Library
- View all past recordings with timestamps
- Select recordings to re-transcribe or generate minutes from
- Delete recordings to save space
- Configure max recordings to keep (auto-cleanup removes oldest)

### Transcription
- Live transcription during recording — minimal wait after stopping
- Choose model size: tiny, base, small, medium, large-v3, turbo
- Auto/GPU/CPU device selection
- Automatic output saved to `Documents/ayjan_minutes/transcripts/raw/`

### Meeting Minutes
- Auto-generate formatted Word documents from transcripts
- Formatted with meeting info, agenda items, action items, and color-coded owners
- Enable/disable in **Settings → Enable Summarization**

### Attendee Management
- Manage a list of possible attendee names under the **Names** tab
- The AI uses these names to correct transcription spelling errors (e.g. "Ning" → "Naing")
- Names are treated as *possible* attendees — only names that appear in the transcript are used
- Add/remove names directly from the Names tab, or edit `assets/possible_names.csv`

### Settings Tab
- Theme toggle (light/dark mode)
- Microphone and speaker selection
- Model size configuration
- AI provider and API key management
- Summarization enable/disable
- Account info and upgrade

## Output

After recording, files are saved to your Documents folder:
- **Audio**: `Documents/ayjan_minutes/recordings/meeting_20260312_222558.wav` (stereo, 48kHz)
- **Transcript**: `Documents/ayjan_minutes/transcripts/raw/meeting_20260312_222558.txt` (plain text)
- **Meeting Minutes** (if enabled):
  - `Documents/ayjan_minutes/meeting_minutes/meeting_20260312_222558_minutes.txt` (markdown)
  - `Documents/ayjan_minutes/meeting_minutes/meeting_20260312_222558_minutes.docx` (formatted Word doc)

### Meeting Minutes Format

The Word document includes:
- **Header Info**: Project, Date, Time, Location, Attendees, Apologies, Objective
- **Agenda Items**: Table with Issues, Decisions, and Action Items for each item
- **Questions**: Open questions raised during the meeting
- **Next Meeting**: Scheduled date/time if mentioned
- **Action Items Summary**: Color-coded by owner (🟢 Internal, 🟡 External, 🔵 Cross-party)

## faster-whisper Models

| Model    | Size   | Speed | Accuracy  |
|----------|--------|-------|-----------|
| tiny     | 39 MB  | Fast  | Lower     |
| base     | 140 MB | Fast  | Good      |
| small    | 244 MB | ~1×   | Better    |
| medium   | 769 MB | ~2×   | Very Good |
| large-v3 | ~1.5 GB| ~4×   | Best      |
| turbo    | ~809 MB| Fast  | Very Good |

**The installer downloads the model during setup** (if you check the option). If you skip it, the model downloads automatically on your first transcription.

**Cache location:**
- Windows: `%USERPROFILE%\.cache\ayjan_minutes\models\`
- Models are cached after the first download (one-time only)
- Safe to delete the folder to free space — the model re-downloads on next use

## GPU Acceleration

By default, transcription runs on CPU. For NVIDIA GPU acceleration (3–5x faster on medium/large models):

**Step 1: Install CUDA Toolkit 12.x**

Download from [NVIDIA's CUDA Toolkit page](https://developer.nvidia.com/cuda-toolkit-archive):
- Select **Windows**, **x86_64**, **12.x**, **exe (local)**
- Run the installer with default settings

**Step 2: Restart the app**

Ayjan Minutes detects your GPU automatically. The device indicator in the Transcription panel will show **CUDA** once it's working.

**Device options (configurable in Settings):**
- **Auto** (default) — Uses GPU if available, falls back to CPU automatically
- **CUDA** — Forces GPU only
- **CPU** — Forces CPU (ignores GPU)

**Performance:**
- **CPU (int8)**: ~1× speed — fine for short recordings and small models (tiny/base)
- **GPU (float16)**: ~3–5× speed — recommended for medium/large models or long recordings

## Troubleshooting

**No audio captured**
- Open the app and check the **Select Microphone** and **Select Speaker** dropdowns
- Verify the selected devices are working in Windows Sound settings

**First transcription is slow**
- The model is downloading on first use — this is a one-time wait
- Subsequent runs use the cached model from `%USERPROFILE%\.cache\ayjan_minutes\models\`
- Switch to a smaller model (tiny/base) in **Settings → Model** for faster results

**Meeting minutes not generating**
- Go to **Settings** and confirm **Enable Summarization** is ON
- For Built-in AI: ensure you're signed in with a valid session
- For Claude/OpenAI: verify your API key is entered under **Settings → AI Provider**

**Can't sign in / forgotten password**
- Use **Forgot password?** on the login screen to reset your password via email
- Check your internet connection — sign-in requires network access

**GPU errors after installing CUDA**
- Restart your computer after installing CUDA Toolkit
- If the error persists, the app will fall back to CPU automatically

## Limitations

- Windows 10/11 only
- English audio only — non-English recordings are detected and skipped with a warning
- Transcription accuracy depends on audio quality (noise, clarity, volume)
