<div align="center">
# Cluli

### The open-source AI copilot for technical interviews.

Cluli is a free, open-source **Cluely alternative** built for technical interviews, online assessments (OAs), and competitive programming.

It provides real-time AI assistance through a stealth desktop overlay that stays out of screen-sharing and recording captures. Ask a question through **voice or a screenshot**, and Cluli uses Gemini's multimodal capabilities to understand the context and stream an answer back to you.

**See it → Ask it → Get the answer.**

</div>

---

## Overview

During a technical interview, the information you need help with is often already right in front of you — the coding problem, an error message, documentation, or a diagram.

Cluli is designed to give you AI assistance without forcing you to switch applications or manually upload everything to a chatbot.

It runs as a transparent, frameless, always-on-top Electron application. You can capture your screen or ask through voice, send that context to the configured AI provider, and receive the response through a floating response interface.

The entire interaction is designed to happen alongside the interview rather than requiring a separate browser tab.

## Problem Statement

Technical interviews can require solving unfamiliar problems under time pressure.

When using a conventional AI assistant, the workflow usually looks like:

```text
Interview Question
       ↓
Take Screenshot / Copy Question
       ↓
Switch to Browser
       ↓
Open AI Assistant
       ↓
Upload / Paste Context
       ↓
Explain the Problem
       ↓
Wait for Response
       ↓
Switch Back
```

Every switch introduces friction and requires the user to manually move context between applications.

For an interview copilot, the interaction needs to be much faster.

## Solution

Cluli puts the AI assistant directly on the desktop.

```text
Interview Question
       ↓
Trigger Cluli
       ↓
Capture Screen / Ask by Voice
       ↓
Gemini Understands the Context
       ↓
AI Response Streams
       ↓
Read the Answer
```

The screenshot pipeline captures the desktop locally, identifies the relevant display, converts the captured image to PNG, and sends it to Gemini's multimodal model together with the selected prompt skill and recent session context.

Responses are streamed back through Electron IPC into Cluli's response interface.

This allows Cluli to understand what is currently on the screen without requiring the user to manually upload the screenshot or repeatedly explain the context.

---

## Features

### 🕵️ Stealth Overlay

Cluli runs as a transparent, frameless, always-on-top desktop overlay designed for use alongside screen sharing and recording applications.

The overlay can also be hidden automatically when screen sharing begins.

### 📸 Screenshot-Based AI

Capture the current screen and send it directly to Gemini for visual reasoning.

There is no separate OCR workflow between the screenshot and Gemini's multimodal processing.

### 🎙️ Voice Input

Ask questions using your microphone instead of typing.

Cluli supports:

- Local OpenAI Whisper
- Microsoft Azure Speech
- Manual recording
- Automatic voice activity detection

Local Whisper can run through a persistent Python worker and supports CPU inference and CUDA acceleration when available.

### ⚡ Real-Time Streaming

AI responses are streamed as they are generated.

```text
Gemini
   ↓
LLMService
   ↓
Electron IPC
   ↓
Response Renderer
   ↓
Answer appears progressively
```

### 🧠 Session Memory

Cluli keeps recent conversation context in memory.

This allows follow-up questions such as:

- "Can you optimize that?"
- "What is the time complexity?"
- "What if the input is empty?"
- "Give me the C++ version."

without requiring the entire problem to be explained again.

### 🎯 Interview-Focused AI Skills

Cluli separates AI behavior from the UI through prompt skill files.

Current skills include:

- DSA
- Programming

This makes it possible to configure how the AI responds without changing the application interface.

### 💻 Code-Friendly Responses

The response interface supports:

- Markdown
- Code blocks
- Syntax highlighting
- Algorithms
- Mathematical notation
- Tables
- Debugging explanations
- Visual reasoning
- Long-form answers

### 🖥️ Multi-Monitor Support

Cluli supports desktop capture across multiple displays and provides controls for managing the overlay and its position.

### 🖱️ Click-Through Mode

The overlay can be switched into click-through mode so it does not interfere with interaction with the application underneath it.

### ⌨️ Global Shortcuts

Core actions can be triggered without leaving the current application.

| Action | Shortcut |
|---|---|
| Screenshot Analysis | `Ctrl/Cmd + Shift + Alt + S` |
| Toggle Visibility | `Ctrl/Cmd + Shift + V` |
| Toggle Click-Through | `Ctrl/Cmd + Shift + I` |
| Open Chat | `Ctrl/Cmd + Shift + C` |
| Clear Session | `Ctrl/Cmd + Shift + \` |
| Speech | `Alt + R` |
| Move Window | `Ctrl/Cmd + Arrow Keys` |
| Toggle Always-on-Top | `Ctrl/Cmd + Shift + T` |

---

## How It Works

Cluli combines the desktop overlay, screenshot capture, session management, speech services, and Gemini into one pipeline.

```text
                         USER'S DESKTOP
                              │
                              ▼
                     ┌──────────────────┐
                     │   Cluli Overlay  │
                     └────────┬─────────┘
                              │
                        Preload Bridge
                              │
                              ▼
                     ┌──────────────────┐
                     │ Electron Main    │
                     │    Process       │
                     └────────┬─────────┘
                              │
             ┌────────────────┼────────────────┐
             │                │                │
             ▼                ▼                ▼
       CaptureService   SessionManager   SpeechService
             │                │                │
             └────────────────┼────────────────┘
                              ▼
                       ┌──────────────┐
                       │  LLMService  │
                       └──────┬───────┘
                              │
                              ▼
                     ┌──────────────────┐
                     │ Gemini Multimodal│
                     │      Model       │
                     └────────┬─────────┘
                              │
                          Streaming
                              │
                              ▼
                     ┌──────────────────┐
                     │ Response Window │
                     └──────────────────┘
```

### Screenshot Pipeline

```text
Current Desktop
      ↓
Desktop Capture
      ↓
Display Identification
      ↓
Native Image Capture
      ↓
PNG Conversion
      ↓
Selected AI Skill
      ↓
Recent Session Context
      ↓
Gemini Multimodal AI
      ↓
Streaming Response
      ↓
Electron IPC
      ↓
Response Window
```

A Gemini request can contain:

- Text prompt
- Captured image
- Selected prompt skill
- Programming-language context
- Recent session history

---

## Tech Stack

- **Desktop Runtime:** Electron
- **Frontend:** HTML / CSS / JavaScript
- **AI:** Google Gemini
- **Gemini SDK:** `@google/genai`
- **Desktop Capture:** Electron `desktopCapturer` / `screen`
- **IPC:** Electron IPC
- **Secure Bridge:** Electron `contextBridge`
- **Cloud Speech:** Microsoft Azure Speech
- **Local Speech:** OpenAI Whisper
- **ML Runtime:** Python / PyTorch
- **Configuration:** dotenv
- **Logging:** Winston
- **Packaging:** electron-builder
- **CI/CD:** GitHub Actions
- **Development:** OpenAI Codex
- **Repository:** GitHub

---

## Codex / OpenAI Usage

OpenAI Codex was used throughout the development of Cluli as a software-engineering accelerator.

It was used to assist with implementation and iteration across areas including:

- Electron architecture
- IPC and preload boundaries
- Desktop capture
- Gemini integration
- Streaming
- Multi-window communication
- Speech infrastructure
- Whisper worker management
- Testing
- Packaging
- CI/CD

Codex helped accelerate development across multiple interconnected systems while keeping the project focused on becoming a working desktop application rather than just an AI prototype.

---

## Privacy & Security

Cluli handles potentially sensitive desktop context, so privacy is an important part of its architecture.

### Local Processing

- Desktop screenshots are captured locally.
- Local Whisper transcription can run on-device.
- API credentials are stored locally.
- `.env` is excluded from Git.

### Renderer Isolation

The renderer does not receive unrestricted Node.js or Electron access.

Privileged functionality is exposed through a restricted preload API using Electron's `contextBridge`.

### Network Boundaries

When screenshot analysis is requested, the captured image is sent to Gemini for multimodal processing.

When Azure Speech is selected, speech data is sent to Azure.

When local Whisper is selected, transcription can remain local.

> Screenshots may contain sensitive information. Users should only use screenshot analysis when they are comfortable sending the captured image to the configured AI provider.

---

## Project Structure

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

---

## How to Run Locally

### Requirements

You'll need:

- Node.js
- npm
- Gemini API key

For optional local Whisper support:

- Python
- PyTorch
- Whisper
- CUDA-compatible hardware for GPU acceleration

### Clone

```bash
git clone https://github.com/slothrulez/cluli.git
cd cluli
```

### Install

```bash
npm install
```

### Configure

```bash
cp env.example .env
```

Windows CMD:

```cmd
copy env.example .env
```

Add your Gemini API key:

```env
GEMINI_API_KEY=your_real_key_here
```

### Run

```bash
npm start
```

On Windows CMD:

```cmd
set "ELECTRON_RUN_AS_NODE=" && node_modules\.bin\electron.cmd .
```

On PowerShell:

```powershell
Remove-Item Env:ELECTRON_RUN_AS_NODE -ErrorAction SilentlyContinue
& .\node_modules\.bin\electron.cmd .
```

---

## Demo

### Live Demo

Cluli is a desktop application and does not currently have a browser-based live demo.

**GitHub:**  
https://github.com/slothrulez/cluli

### Demo / Pitch Video

Add your demo or pitch video here.

The demo should show:

1. Cluli running alongside an interview/coding environment
2. The overlay remaining available during the session
3. A screenshot being captured
4. Gemini analyzing the problem
5. The response streaming into Cluli
6. Voice input, if demonstrated

---

## Screenshots

Add screenshots here showing:

- Cluli overlay
- Interview question capture
- Gemini response
- Streaming response
- Chat window
- Settings
- Voice input
- Cluli alongside an IDE

---

## Current Limitations

Cluli currently depends on:

- Internet connectivity for Gemini requests
- A valid Gemini API key
- Provider quotas and model availability
- Platform-specific desktop capture/content-protection behavior
- Additional system resources for local Whisper
- CUDA availability for GPU acceleration
- An in-memory session rather than persistent cross-device history

---

## Roadmap

Planned improvements include:

- Selectable screenshot regions
- Per-window capture
- Clipboard context
- OCR fallback
- Image annotation
- More AI skills
- Per-skill configuration
- Custom prompts
- Response pinning
- Session history
- Export/import sessions
- Configurable shortcuts
- Model selection
- Local LLM support
- GPU status
- Improved speech diagnostics
- Accessibility improvements
- Additional privacy controls
- Release signing and automatic updates

---

## Why Cluli?

AI assistants are already good at solving problems.

The frustrating part is getting the problem to them at the right moment.

During an interview, that friction matters even more.

Cluli is built around the idea that the AI should be able to understand the context that's already in front of you — without forcing you to leave the workflow.

**Your question is on the screen.  
Cluli sees the context.  
Gemini reasons about it.  
The answer comes back to you.**

---

## Built With

**Electron · Gemini · Whisper · Codex**

Made for technical interviews, OAs, DSA, and competitive programming.

**Cluli — AI assistance without leaving the interview.**
