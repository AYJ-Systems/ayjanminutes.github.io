# AI Note Taker

Professional Windows audio recorder with an intuitive GUI that captures mic + system audio simultaneously, then:
1. **Transcribes** using local `faster-whisper` models (no API calls)
2. **Generates AI meeting minutes** using Claude API (optional)

Records as stereo WAV, transcripts saved as plain text, and structured meeting minutes exported as professionally-formatted Word documents with customizable templates.

## Quick Start

### Option 1: Download Installer (Recommended)
Download the latest stub installer from the [GitHub Releases page](https://github.com/Tranks27/tk_ai_note_taker/releases).

Run `AI-Note-Taker-Setup.exe` — it's a small (~15 MB) installer that downloads and installs the full app. During setup you can optionally download the Whisper transcription model so it is ready before your first meeting. No Python or manual setup needed.

On first run, the GUI will prompt you to enter your API key (optional, for AI meeting minutes). The app saves your key securely for future use.

### Option 2: Run from Source
1. Install Python 3.10+
2. Clone the repository: `git clone https://github.com/Tranks27/tk_ai_note_taker`
3. Follow the [Installation](#installation) steps below
4. Run: `python gui.py`

## Requirements

- Windows 10/11
- Python 3.10+
- **ffmpeg** (optional): Only needed if transcribing non-WAV formats (MP3, M4A, FLAC, etc.). Download from [https://ffmpeg.org/download.html](https://www.gyan.dev/ffmpeg/builds/#release-builds) and keep in `utils/`
- **Claude API key** (optional): Only needed for AI meeting minutes generation. Get from [console.anthropic.com](https://console.anthropic.com)

## Installation

**1. Clone and enter directory:**
```bash
cd ai_note_taker
```

**2. Create virtual environment:**
```bash
python -m venv venv
venv\Scripts\activate  # Windows
# or: source venv/bin/activate  # macOS/Linux
```

**3. Install dependencies:**
```bash
pip install -r requirements.txt
```

**4. Launch the GUI:**
```bash
python gui.py
```

**5. (Optional) Enable meeting minutes:**
On first run, the GUI will prompt you to enter your Claude API key if you want to enable AI-powered meeting minutes. You can also enable/disable this anytime from the Settings tab.

## Setup for Meeting Minutes (Optional)

To generate AI-powered meeting minutes you need an API key from either **Claude** (Anthropic) or **OpenAI**:

**Step 1: Get API Key**
- **Claude**: Go to [console.anthropic.com](https://console.anthropic.com) and create an account, then generate an API key
- **OpenAI**: Go to [platform.openai.com](https://platform.openai.com) and create an API key

**Step 2: Enter your key in the GUI**
- Launch `python gui.py` or open the standalone .exe
- On first run, the app will detect no key and prompt you to enter it
- Select "Save API Key" to store it securely in `.env` for future use

**Step 3: Enable summarization**
- Go to the **Settings** tab in the GUI
- Toggle **Enable Summarization** to ON
- The app will generate meeting minutes for every recording

**Security:** The `.env` file is automatically excluded from git — never commit API keys!

## Usage

### Launch the GUI

```bash
python gui.py
```

The GUI provides an intuitive interface for all recording and transcription features.

### Main Features in GUI

**Recording Panel:**
- **Select Microphone** — Choose from available mics, or use default
- **Select Speaker** — Choose system audio source
- **Record Button** — Start/stop recording with elapsed time display
- **Files are saved to:**
  - Audio: `Documents/ai_note_taker/recordings/meeting_TIMESTAMP.wav`
  - Transcript: `Documents/ai_note_taker/transcripts/raw/meeting_TIMESTAMP.txt` (if enabled)
  - Minutes: `Documents/ai_note_taker/meeting_minutes/meeting_TIMESTAMP_minutes.docx` + `.txt` (if enabled)

**Recording Library:**
- View all past recordings with timestamps
- Select recordings to transcribe or replay
- Delete recordings to save space
- Configure max recordings to keep

**Transcription:**
- Real-time transcription status and progress
- Choose model size: tiny, base, small, medium, large-v3, turbo
- Auto/GPU/CPU device selection with status indicator
- Automatic output saved to `Documents/ai_note_taker/transcripts/raw/`

**Meeting Minutes (Optional):**
- Auto-generate formatted Word documents from transcripts
- Requires Claude API key (configured on first run)
- Formatted with meeting info, agenda items, action items, and color-coded owners

**Attendee Management:**
- Manage list of possible attendee names
- Claude uses these names to correct transcription errors
- Add/remove names directly from settings panel

**Settings Tab:**
- Theme toggle (light/dark mode)
- Device selection
- Model size configuration
- Recording output directory
- Transcription settings
- Summarization enable/disable

## Building the Installer

### Build Stub Installer (Recommended for Distribution)

```bash
python utils/build_stub_installer.py
```

Creates a small (~15 MB) `dist/stub_installer/AI-Note-Taker-Setup-v{version}.exe` installer that downloads the full app and (optionally) the Whisper model during setup. The output version and model size are read automatically from `config.yaml`.

**Prerequisites:**
- `pip install pyinstaller`
- [Inno Setup 6](https://jrsoftware.org/isinfo.php)

**For End Users:**
- Download and run `AI-Note-Taker-Setup-v{version}.exe`
- Downloads and installs the full application
- Optionally downloads the Whisper transcription model during setup
- GUI launches automatically after installation
- On first run, the app will prompt to enter your API key (optional) and save it automatically

**Options:**
```bash
python utils/build_stub_installer.py                      # Auto-reads version + model from config.yaml
python utils/build_stub_installer.py --version v2.1.0     # Pin a specific GitHub release tag
python utils/build_stub_installer.py --iscc "C:\path\to\ISCC.exe"  # Custom Inno Setup path
```

### Build Standalone .exe (Alternative)

```bash
python utils/build_exe.py
```

Creates a `dist/AI Note Taker/` folder with the executable and all libraries pre-extracted. Useful for portable distribution (~500MB-1GB).

## Configuration

Two configuration files work together:

**1. `.env` — API Credentials (secure, not in git)**
```
CLAUDE_API_KEY=sk-ant-...
```
Created automatically when you enter your key at the startup prompt. You can also create it manually from `.env.example`.

**2. `config.yaml` — Application Settings**
```yaml
app:
  theme: dark             # dark or light theme
  version: 2.1.0          # Current app version

devices:
  mic: null               # null = default, or partial name match
  speaker: null           # null = default, or partial name match

recording:
  sample_rate: 48000      # Hz (matches USB mics and meeting apps)
  block_size: 4096        # frames per read
  output_dir: recordings  # where to save WAV files
  max_recordings: 5       # auto-cleanup old recordings

transcription:
  enabled: true           # disable to skip transcription
  model_size: medium      # tiny, base, small, medium, large-v3, turbo
  output_dir: transcripts # where to save transcript .txt files
  device: auto            # auto, cuda, or cpu (requires GPU setup for CUDA)
  max_transcriptions: 5   # auto-cleanup old transcriptions

summarization:
  enabled: true           # Generate AI meeting minutes with Claude or OpenAI
  provider: claude        # AI provider: claude or openai
  model: claude-haiku-4-5 # Model to use (e.g. claude-haiku-4-5, gpt-4o-mini)
  output_dir: meeting_minutes  # where to save meeting minutes
  max_summarizations: 5   # auto-cleanup old summaries
```

**Note:** Both files are loaded automatically by `config_loader.py` at startup. The app auto-removes old files when limits are exceeded to save disk space.

## Output

After recording, files are saved to your Documents folder:
- **Audio**: `Documents/ai_note_taker/recordings/meeting_20260312_222558.wav` (stereo, 48kHz)
- **Transcript**: `Documents/ai_note_taker/transcripts/raw/meeting_20260312_222558.txt` (plain text)
- **Meeting Minutes** (if enabled):
  - `Documents/ai_note_taker/meeting_minutes/meeting_20260312_222558_minutes.txt` (markdown)
  - `Documents/ai_note_taker/meeting_minutes/meeting_20260312_222558_minutes.docx` (formatted Word doc)

### Meeting Minutes Format

The Word document is formatted according to your template and includes:
- **Header Info**: Project, Date, Time, Location, Attendees, Apologies, Objective
- **Agenda Items**: Detailed table with Issues, Decisions, Action Items for each item
- **Questions**: Open questions raised during the meeting
- **Next Meeting**: Scheduled date/time if mentioned
- **Action Items Summary**: Color-coded by owner (🟢 Internal, 🟡 External, 🔵 Cross-party)

## Attendee Name Recognition

When generating meeting minutes, the app passes a list of possible attendee names to Claude so it can correct spelling errors from the Whisper transcription (e.g. "Ning" → "Naing").

Names are stored in `assets/possible_names.csv` and managed via **Names** in the main menu.

Names in the list are treated as *possible* attendees — Claude only uses names that appear in the transcript and does not add unlisted people as attendees.

You can also edit `assets/possible_names.csv` directly (one name per row, with a `name` header).

## faster-whisper Models

| Model    | Size  | Speed | Accuracy |
|----------|-------|-------|----------|
| tiny     | 39M   | Fast  | Lower    |
| base     | 140M  | Fast  | Good     |
| small    | 244M  | ~1x   | Better   |
| medium   | 769M  | ~2x   | Very Good|
| large-v3 | ~1.5G | ~4x   | Best     |
| turbo    | ~809M | Fast  | Very Good|

**The installer downloads the model during setup** (if you check the option). If you skip it, the model downloads automatically on your first transcription.

**Cache location:**
- Windows: `%USERPROFILE%\.cache\ai_note_taker\models\`
- Models are cached after first download (one-time only)
- Safe to delete the folder to free space — the model will re-download on next use

## GPU Acceleration

By default, transcription runs on CPU. For NVIDIA GPU acceleration (3–5x faster), follow these steps:

### Enable GPU Transcription

**Step 1: Install CUDA Toolkit 12.x**

Download from [NVIDIA's CUDA Toolkit archive](https://developer.nvidia.com/cuda-12-9-1-download-archive):
- Select **Windows**, **x86_64**, **12.x** (latest 12.x version), **exe (local)**
- Run the installer with default settings

**Step 2: Verify CUDA is installed**
```bash
nvcc --version
```
Should show version 12.x

**Step 3: Enable GPU in config.yaml**
```yaml
transcription:
  enabled: true
  model_size: medium
  device: auto    # auto (GPU if available), cuda (GPU only), or cpu
```

**Step 4: Verify GPU is working**
```bash
python -c "import ctranslate2; print(ctranslate2.get_supported_compute_types('cuda'))"
```
Should print: `['float32', 'float16', 'int8_float16', 'int8']`

**Step 5: Test transcription**
```bash
python transcribe.py recordings/meeting.wav
```
Should show: `✓ CUDA available` and `Using device: cuda (float16)` ✓

### Configuration Options

- **`device: auto`** (default) — Uses GPU if available, falls back to CPU automatically
- **`device: cuda`** — Forces GPU only (errors if CUDA unavailable)
- **`device: cpu`** — Forces CPU (ignores GPU)

### Performance

GPU transcription is typically **3–5x faster** than CPU on medium/large models:
- **CPU (int8)**: ~1x speed
- **GPU (float16)**: ~3–5x speed

**CPU is fine for:** Quick notes, small models (tiny/base)
**GPU is recommended for:** Large models (medium/large-v3), long recordings

## Limitations

- Speech-only: Music and non-speech audio are filtered out
- Accuracy depends on audio quality (noise, clarity, volume)

## Troubleshooting

### Recording Issues

**No audio captured**
- Open the GUI and check the **Select Microphone** and **Select Speaker** dropdowns
- Verify all available devices are showing in the dropdowns
- Verify mic and speaker are working in Windows Settings

**Audio is quiet or distorted**
- Check Windows volume levels for mic and system audio
- Try adjusting gain in device settings
- Try a different microphone from the dropdown

### Transcription Issues

**Transcription not running**
- Verify `transcription.enabled: true` in `config.yaml`
- Check `faster-whisper` is installed: `pip install faster-whisper`
- Verify you have enough disk space for model cache (~1-5GB)

**Transcription is very slow**
- If the model wasn't downloaded during installation, the first transcription downloads it — wait for completion
- Subsequent runs use the cached model from `%USERPROFILE%\.cache\ai_note_taker\models\`
- Try smaller model: change `model_size: tiny` or `base` in Settings
- **Enable GPU**: Install CUDA Toolkit 12.x for 3–5x speedup (see [GPU Acceleration](#gpu-acceleration) section)

**GPU errors (CUDA not found, cublas DLL missing, etc.)**
- Install **CUDA Toolkit 12.x** from [NVIDIA's archive](https://developer.nvidia.com/cuda-downloads-archive)
- Verify CUDA is available: 
  ```bash
  nvcc --version
  ```
  Should show version 12.x
- Test ctranslate2 support:
  ```bash
  python -c "import ctranslate2; print(ctranslate2.get_supported_compute_types('cuda'))"
  ```
  Should print: `['float32', 'float16', 'int8_float16', 'int8']`
- If still failing, fall back to CPU: change `device: cpu` in `config.yaml`

**Transcription fails on audio file**
- For non-WAV formats (MP3, M4A, FLAC): install ffmpeg from https://ffmpeg.org/download.html
- Add ffmpeg to Windows PATH or place in `utils/` folder

### Meeting Minutes Issues

**Meeting minutes not generating**
- Go to **Settings** and toggle **Enable Summarization** ON
- Enter your Claude API key when prompted (or paste it from your Anthropic console)
- Restart the app after entering the key

**"No API key provided" error**
- Click the API Key field in Settings and enter your key from https://console.anthropic.com
- Key should start with `sk-ant-`
- Click "Save API Key" to store it securely

**Invalid/expired API key**
- Get a new key from https://console.anthropic.com
- Clear the key field in Settings and enter the new one
- Click "Save API Key" and restart the app

**Word document formatting issues**
- Ensure `assets/meeting_minutes_TEMPLATE.docx` exists in the app directory
- Ensure `assets/note_taker_prompt.txt` exists (meeting minutes prompt template)
- Try re-transcribing the recording to regenerate minutes

### General

**"Module not found" errors**
- Activate venv: `venv\Scripts\activate`
- Install dependencies: `pip install -r requirements.txt`

**Permission denied on venv/Scripts/**
- Right-click Command Prompt → "Run as Administrator"
- Then activate venv and try again

## Project Structure

```
ai_note_taker/
├── gui.py               # Main GUI application (PySide6) — primary entry point
├── main.py              # Core recording functionality (used by GUI)
├── transcribe.py        # Transcription module (also runnable standalone)
├── summarize.py         # Meeting minutes generation (Claude API)
├── docx_generator.py    # Word document formatter
├── config_loader.py     # Centralized config & .env loader
├── auth_manager.py      # Authentication for API key management
├── config.yaml          # Configuration file
├── .env                 # API credentials (auto-created on first run, in .gitignore)
├── .env.example         # Template for .env file
├── requirements.txt     # Python dependencies
├── README.md            # This file
├── RELEASES.md          # Version history and release notes
├── assets/              # Templates and prompts
│   ├── note_taker_prompt.txt           # Meeting minutes prompt
│   ├── meeting_minutes_TEMPLATE.docx   # Word doc template
│   └── possible_names.csv              # Attendee names for AI name recognition
├── recordings/          # Audio output directory (auto-created)
├── transcripts/         # Transcript output directory (auto-created)
├── meeting_minutes/     # Meeting minutes output (auto-created)
├── utils/               # Build scripts and utilities
│   ├── build_exe.py               # Build script for creating .exe
│   ├── build_stub_installer.py    # Create Windows installer
│   └── ffmpeg/          # (optional) ffmpeg binary for media conversion
└── dist/                # Build output (created by utils/build_exe.py)
    └── AI Note Taker/   # Distribute this entire folder (zip it)
```

## License

MIT
