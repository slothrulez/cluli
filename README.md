<div align="center">

# Cluli

**The invisible AI interview copilot.**

Real-time AI help on a stealth overlay that screen sharing cannot see. Ask by voice or screenshot, and get clear answers that stream in as you need them.


<a href="#download">Download</a> &nbsp;|&nbsp;
<a href="#quick-start">Quick start</a> &nbsp;|&nbsp;
<a href="#how-it-works">How it works</a>

**See something → Capture it → Ask → Get a streamed answer.**

[![Electron](https://img.shields.io/badge/Electron-Desktop%20App-47848F?logo=electron\&logoColor=white)](https://www.electronjs.org/)
[![Gemini](https://img.shields.io/badge/AI-Google%20Gemini-8E75B2)](https://ai.google.dev/)
[![Whisper](https://img.shields.io/badge/Speech-Whisper-412991)](https://github.com/openai/whisper)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](#license)

---
When you're coding, reading documentation, debugging an application, studying an algorithm, or looking at something you don't understand, the relevant information is often already visible on your screen.

Cluli can capture that screen, send the visual context to Gemini's multimodal capabilities, combine it with your selected AI skill and recent session context, and stream the response back to you.

No browser tab switching.
No manual screenshot uploading.
No repeatedly explaining what you're looking at.

---

## 🚀 Why Cluli?

### The traditional workflow

```text
Working on something
       ↓
Stop working
       ↓
Take screenshot / copy context
       ↓
Open browser
       ↓
Open AI assistant
       ↓
Upload / paste
       ↓
Explain context
       ↓
Wait for response
       ↓
Return to original application
```

### The Cluli workflow

```text
Working on something
       ↓
Trigger Cluli
       ↓
Capture current desktop
       ↓
Gemini understands the context
       ↓
Response streams back
       ↓
Continue working
```

The goal is to make AI assistance feel like a **native layer of the desktop**, rather than another application you have to switch into.

---

# 🧠 Core Architecture

```text
                         USER'S DESKTOP
                               │
                               ▼
                    ┌─────────────────────┐
                    │    Cluli Overlay    │
                    │ Transparent / Always │
                    │       on Top        │
                    └──────────┬──────────┘
                               │
                         Preload Bridge
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Electron Main     │
                    │      Process        │
                    └──────────┬──────────┘
                               │
              ┌────────────────┼────────────────┐
              │                │                │
              ▼                ▼                ▼
       CaptureService    SessionManager   SpeechService
              │                │                │
              └────────────────┼────────────────┘
                               ▼
                       ┌──────────────┐
                       │  LLMService  │
                       └──────┬───────┘
                              │
                              ▼
                    ┌────────────────────┐
                    │ Gemini Multimodal  │
                    │       Model        │
                    └─────────┬──────────┘
                              │
                         Streaming
                              │
                              ▼
                    ┌────────────────────┐
                    │  Response Window  │
                    └────────────────────┘
```

## The renderer is intentionally separated from privileged desktop functionality through Electron's preload bridge and `contextBridge`. Renderer processes do not receive unrestricted Node.js or Electron access.

# 🔥 Features

## 🖥️ Desktop-Native AI

Cluli runs as a transparent, frameless, always-on-top desktop overlay rather than a conventional full-size application window.

It can remain available while you're working in another application and supports visibility and click-through controls.

---

## 📸 Multimodal Screenshot Analysis

Cluli integrates directly with Electron's desktop capture APIs.

The screenshot pipeline is:

```text
Desktop
   ↓
desktopCapturer / screen
   ↓
Display identification
   ↓
Native image capture
   ↓
PNG conversion
   ↓
Gemini multimodal request
```

The screenshot is not merely attached manually by the user — desktop capture is part of Cluli's application architecture.

---

## ⚡ Real-Time Streaming

Cluli streams Gemini responses as they are generated.

```text
Gemini
  ↓
LLMService
  ↓
Electron IPC
  ↓
Response Renderer
  ↓
User sees tokens progressively
```

This avoids the feeling of waiting for a complete generation before the interface updates.

---

## 🧠 Contextual Sessions

Cluli maintains recent interaction context in memory.

This allows follow-up questions to build on previous interactions without forcing the user to repeatedly restate the entire problem.

The session manager intentionally focuses on recent relevant context rather than maintaining an unbounded transcript.

---

## 🎯 Prompt Skills

AI behavior is separated from the application's UI through prompt skill files.

Current skills include:

* **DSA**
* **Programming**

Skills are stored under:

```text
prompts/
├── dsa.md
└── programming.md
```

This makes AI behavior independently configurable without coupling prompt logic directly to the UI implementation.

---

## 💬 Dedicated Response Window

The compact overlay isn't forced to display everything.

Long responses are handled by a dedicated response window capable of presenting:

* Markdown
* Code
* Syntax highlighting
* Mathematical notation
* Tables
* Algorithms
* Debugging explanations
* Visual reasoning
* Long-form answers

---

## 🎙️ Speech Input

Cluli supports two speech architectures:

### Microsoft Azure Speech

Cloud-based speech recognition through Azure.

### Local Whisper

Speech can also be processed locally using Whisper through a persistent Python worker.

The local architecture supports:

* CPU inference
* CUDA when available
* Persistent worker processes
* Model warmup
* Model unloading
* Voice activity detection
* Local model storage
* Manual recording
* Automatic speech detection

---

# ⌨️ Global Shortcuts

Cluli is designed so interaction does not require leaving the current application.

| Shortcut                     | Action               |
| ---------------------------- | -------------------- |
| `Ctrl/Cmd + Shift + S`       | Open Settings        |
| `Ctrl/Cmd + Shift + Alt + S` | Screenshot Analysis  |
| `Ctrl/Cmd + Shift + V`       | Toggle Visibility    |
| `Ctrl/Cmd + Shift + I`       | Toggle Click-Through |
| `Ctrl/Cmd + Shift + C`       | Open Chat            |
| `Ctrl/Cmd + Shift + \`       | Clear Session Memory |
| `Alt + R`                    | Speech               |
| `Ctrl/Cmd + Arrow Keys`      | Move Window          |
| `Ctrl/Cmd + Shift + T`       | Toggle Always-on-Top |

---

# 🧩 AI Pipeline

Every screenshot interaction follows a structured pipeline:

```text
┌──────────────────┐
│ Current Desktop  │
└────────┬─────────┘
         ↓
┌──────────────────┐
│ Screenshot       │
│ Capture          │
└────────┬─────────┘
         ↓
┌──────────────────┐
│ PNG Conversion   │
└────────┬─────────┘
         ↓
┌──────────────────┐
│ Selected Skill   │
└────────┬─────────┘
         ↓
┌──────────────────┐
│ Recent Session   │
│ Context          │
└────────┬─────────┘
         ↓
┌──────────────────┐
│ Gemini           │
│ Multimodal AI    │
└────────┬─────────┘
         ↓
┌──────────────────┐
│ Streaming        │
│ Response         │
└────────┬─────────┘
         ↓
┌──────────────────┐
│ Electron IPC     │
└────────┬─────────┘
         ↓
┌──────────────────┐
│ Response Window  │
└──────────────────┘
```

Gemini requests can contain:

* Text prompts
* Captured images
* Selected prompt skill
* Programming-language context
* Recent session history

---

# 🛠️ Tech Stack

| Component       | Technology                            |
| --------------- | ------------------------------------- |
| Desktop Runtime | Electron                              |
| AI              | Google Gemini                         |
| Gemini SDK      | `@google/genai`                       |
| Frontend        | HTML / CSS / JavaScript               |
| Desktop Capture | Electron `desktopCapturer` / `screen` |
| IPC             | Electron IPC                          |
| Secure Bridge   | Electron `contextBridge`              |
| Cloud Speech    | Microsoft Azure Speech                |
| Local Speech    | OpenAI Whisper                        |
| ML Runtime      | Python / PyTorch                      |
| Configuration   | dotenv                                |
| Logging         | Winston                               |
| Packaging       | electron-builder                      |
| CI/CD           | GitHub Actions                        |

Gemini's default model is configured through the application with fallback model candidates, while the LLM service handles timeouts, retries, fallback behavior, and streaming/non-streaming recovery.

---

# 🔐 Security & Privacy

Cluli handles potentially sensitive desktop context, so privacy is an explicit architectural concern.

### Local processing

* Desktop screenshots are captured locally.
* Local Whisper transcription can be performed on-device.
* API credentials are stored locally.
* `.env` is excluded from Git.

### Controlled renderer access

The renderer communicates with privileged Electron functionality through a restricted preload API rather than unrestricted Node.js access.

### Network boundaries

When screenshot analysis is requested, the captured image is sent to Gemini for multimodal processing.

When Azure Speech is selected, speech data is sent to Azure.

When local Whisper is selected, transcription can remain local.

> **Important:** Screenshots may contain sensitive information. Users should only use screenshot analysis when they are comfortable sending the captured image to the configured AI provider.

---

# 🧪 Testing

Cluli includes an end-to-end screenshot smoke-test workflow that validates the most important path through the application:

```text
Overlay
   ↓
Main Process
   ↓
Desktop Capture
   ↓
PNG Conversion
   ↓
Prompt Selection
   ↓
Gemini Authentication
   ↓
Multimodal Request
   ↓
Streaming
   ↓
IPC
   ↓
Response Rendering
```

Speech testing is also available:

```bash
npm run test-speech
```

The documented manual smoke test covers:

* Application startup
* Overlay interaction
* Window dragging
* Settings
* Gemini connectivity
* Screenshot analysis
* Streaming
* Chat
* Click-through mode
* Speech input
* Microphone permissions
* Transcription

---

# 📦 Installation

## Requirements

You'll need:

* Node.js
* npm
* A Gemini API key

Optional speech functionality may additionally require:

* Python
* PyTorch
* Whisper
* CUDA-compatible hardware for GPU acceleration

---

## Clone

```bash
git clone https://github.com/slothrulez/cluli.git
cd cluli
```

---

## Install Dependencies

```bash
npm install
```

---

## Configure Environment

Copy the example environment file:

### Unix

```bash
cp env.example .env
```

### Windows CMD

```cmd
copy env.example .env
```

Then add your Gemini API key:

```env
GEMINI_API_KEY=your_real_key_here
```

Additional environment variables configure speech providers, Azure credentials, Whisper behavior, and other runtime settings.

---

# ▶️ Running Cluli

### Unix

```bash
npm start
```

### Windows CMD

The package's Unix-oriented start script uses `env -u`, which is not supported by Windows CMD.

Run Electron directly:

```cmd
set "ELECTRON_RUN_AS_NODE=" && node_modules\.bin\electron.cmd .
```

### Windows PowerShell

```powershell
Remove-Item Env:ELECTRON_RUN_AS_NODE -ErrorAction SilentlyContinue
& .\node_modules\.bin\electron.cmd .
```

---

# 🎤 Optional Whisper Setup

Cluli can use a local Whisper worker for speech recognition.

The worker is located at:

```text
scripts/whisper_worker.py
```

The JavaScript service communicates with it through JSON-lines IPC.

Supported worker actions include:

```text
warmup
transcribe
unload
shutdown
```

Whisper can automatically select CUDA when available and fall back to CPU otherwise.

---

# 🏗️ Project Structure

```text
cluli/
│
├── src/
│   ├── main/
│   │   ├── WindowManager
│   │   ├── SessionManager
│   │   ├── CaptureService
│   │   ├── LLMService
│   │   ├── SpeechService
│   │   ├── WhisperWorkerService
│   │   ├── FirstRunManager
│   │   └── WhisperInstaller
│   │
│   ├── renderer/
│   │   ├── index.html
│   │   ├── chat.html
│   │   ├── llm-response.html
│   │   ├── settings.html
│   │   └── onboarding.html
│   │
│   └── preload/
│
├── prompts/
│   ├── dsa.md
│   └── programming.md
│
├── scripts/
│   └── whisper_worker.py
│
├── assests/
│   └── icons/
│
├── env.example
├── setup.sh
├── package.json
└── README.md
```

The `assests/` directory name is intentionally retained because it is referenced by the application.

---

# 🧑‍💻 Development

The repository includes setup options for different development workflows:

```bash
./setup.sh
```

Available options include:

```text
--skip-whisper
--run
--no-run
--build
--ci
```

On Windows, Git Bash or WSL can be used for the setup script.

---

# 📋 Manual Smoke Test

After starting Cluli:

1. Confirm the overlay appears.
2. Verify the overlay can be interacted with.
3. Drag the overlay.
4. Confirm normal clicks still work.
5. Open Settings.
6. Test Gemini connectivity.
7. Trigger screenshot analysis.
8. Confirm the response window opens.
9. Confirm the response streams progressively.
10. Open Chat.
11. Send a text prompt.
12. Toggle click-through mode.
13. Toggle click-through back off.
14. Optionally test speech.
15. Verify microphone permissions.
16. Verify transcription.
17. Run:

```bash
npm run test-speech
```

---

# 🧠 Engineering Highlights

Cluli brings several systems together into one desktop application:

* Electron multi-window orchestration
* Transparent always-on-top windows
* Click-through interaction
* Global keyboard shortcuts
* Multi-monitor desktop capture
* Gemini multimodal inference
* Streaming AI responses
* Context-aware sessions
* Skill-based prompting
* Secure preload architecture
* Azure speech integration
* Local Whisper inference
* Persistent Python ML worker
* Voice activity detection
* CPU/CUDA model selection
* First-run onboarding
* Local configuration
* Rotating application logs
* Cross-platform packaging
* Automated release builds

The engineering challenge is not any single component — it is coordinating all of them into an interaction that feels like a native part of the desktop.

---

# 🤖 AI Development

OpenAI/Codex was used during development as a software-engineering accelerator across the project.

It was used to assist with implementation and iteration across areas including:

* Electron architecture
* IPC and preload boundaries
* Desktop capture
* Gemini integration
* Streaming
* Multi-window communication
* Speech infrastructure
* Whisper worker management
* Testing
* Packaging
* CI/CD

The resulting project integrates these components into a working desktop application rather than being a standalone AI prototype.

---

# 🗺️ Roadmap

The architecture is designed to support future capabilities such as:

* [ ] Selectable screenshot regions
* [ ] Per-window capture
* [ ] Multi-monitor capture UI
* [ ] Clipboard context
* [ ] OCR fallback
* [ ] Image annotation
* [ ] More AI skills
* [ ] Per-skill configuration
* [ ] Custom prompts
* [ ] Response pinning
* [ ] Session history
* [ ] Export/import sessions
* [ ] Configurable shortcuts
* [ ] Model selection
* [ ] Local LLM support
* [ ] GPU status
* [ ] Improved speech diagnostics
* [ ] Accessibility improvements
* [ ] Additional privacy controls
* [ ] Release signing and automatic updates

---

# ⚠️ Current Limitations

Cluli currently depends on:

* Internet connectivity for Gemini requests.
* A valid Gemini API key.
* Provider quotas and model availability.
* Platform-specific desktop capture/content-protection behavior.
* Additional system resources for local Whisper.
* CUDA availability for GPU acceleration.
* An in-memory session rather than persistent cross-device history.

Screenshot analysis also requires users to consider the privacy implications of sending their captured desktop context to the configured AI provider.

---

# 🌐 Repository

**GitHub:**
https://github.com/slothrulez/cluli

---

# 💡 The Idea

The fundamental idea behind Cluli is simple:

> **The AI shouldn't require you to leave your workflow to give it context.**

Your code is on the screen.
Your error is on the screen.
Your documentation is on the screen.
Your diagram is on the screen.
Your problem is on the screen.

**So why not let the screen become the context?**

Cluli is an attempt to make that interaction feel native.

---

## Built with Electron + Codex

**Cluli — contextual AI for your desktop.**
