# Quiz-HTML Agent

[![OpenCode](https://img.shields.io/badge/OpenCode-skill-blue)](https://opencode.ai)
[![Antigravity](https://img.shields.io/badge/Antigravity-skill-purple)](https://antigravity.ai)
[![Claude](https://img.shields.io/badge/Claude-skill-orange)](https://claude.ai)

Two independent AI skills for **generating educational quizzes from documents** and **turning quiz data into standalone HTML files**. Each skill is a self-contained git submodule with its own `SKILL.md`, scripts, and assets.

## Live Demo

Try the quiz HTML template live: **[quiz-html-generator.vercel.app](https://quiz-html-generator.vercel.app/)** — a fully functional dark-theme quiz with 8 question types, lives, XP, streaks, and confetti.


### quiz-html-generator

**No dependencies.** The skill reads your data source (JSON, Markdown, CSV/XLSX, DOCX, PDF) and produces a self-contained `.html` file — open it in any browser.

## Repository Structure

```
quiz-html-agent/
├── quiz-from-docs/         # Skill: extract quiz content from PDFs/text/images/videos
│   ├── SKILL.md            # Skill instructions & Duolingo-inspired methodology
│   ├── scripts/            # generate_audio.py, generate_card_image.py, extract_text.py
│   ├── evals/              # Evaluation benchmarks
│   └── references/         # Reference materials
├── quiz-html-generator/    # Skill: build standalone HTML quizzes from structured data
│   ├── SKILL.md            # Skill instructions & template reference tables
│   ├── assets/             # template.html (~2570 lines, dark MAZA theme)
    │   ├── template.html       # The full interactive quiz template
    │   └── gen_tts.py          # Optional audio generation helper
│   └── evals/              # Evaluation benchmarks
├── .gitignore
├── .gitmodules             # Submodule definitions
└── README.md
```

## Skills Overview

### quiz-from-docs

Creates `quiz_[lesson].json` from source documents with **8 question types**, **audio** for every text field (via `edge-tts`), and **AI-generated images** (via Gemini 3.1 Flash).

**Inputs:** PDF, TXT, HTML, PNG/JPG/GIF/BMP/TIFF (OCR), MP4/AVI/MOV/MKV (transcripts)  
**Output:** `quiz_[lesson].json` — never overwrites, appends `_1`, `_2` suffix  
**Audio:** 8 voice options across PT, PT-BR, EN, ES  
**Images:** Only for MCQ, Multi-Select, Matching, Sequencing, Flash Card

### quiz-html-generator

Takes structured data and produces a **standalone, dark-theme HTML file** with splash screen, progress bar, 3 lives (hearts), XP/streak system, audio replay, and confetti on completion.

**Inputs:** JSON, Markdown, CSV/XLSX, DOCX, PDF  
**Output:** `<input-filename>.html` — self-contained, zero external deps  
**Template:** MAZA dark theme, Inter font, Tabler Icons, `--clr-*` CSS variables  
**Live preview:** [quiz-html-generator.vercel.app](https://quiz-html-generator.vercel.app/)

## 8 Question Types

| Type | `type` value | Description |
|------|-------------|-------------|
| MCQ | `mcq` | Single-choice, tap-to-select cards |
| Multi-Select | `multi-select` | Pick multiple correct answers (partial credit) |
| True/False | `true-false` | Two large buttons |
| Fill-in-the-Blank | `fill-blank` | Type or drag tiles; Levenshtein near-miss detection |
| Matching | `matching` | Pair left/right items by clicking |
| Sequencing | `sequencing` | Drag-and-drop reorder items |
| Numeric | `numeric` | Enter number with tolerance & unit; near-miss feedback |
| Flash Card | `flashcard` | Flip card to reveal answer, then select |

## Setup

```powershell
# Clone with submodules
git clone https://github.com/getur/quiz-html-agent.git
cd quiz-html-agent
git submodule update --init --recursive
```

## Tool Compatibility

Each skill is a `SKILL.md` file that any **skill-compatible AI tool** can load. Supported platforms:

| Tool | Type | How It Works |
|------|------|-------------|
| **OpenCode** | Terminal CLI | Loads skills automatically from your local filesystem. Skills are triggered by describing your task in natural language — it detects the matching skill and follows its instructions. |
| **Antigravity / Google CLI** | IDE + Terminal | Google's AI-powered CLI and IDE extension. Installs skills from the local filesystem; the agent reads the `SKILL.md` and follows its workflow. Supports the same skill format. |
| **Claude AI / Claude Code** | Web + Terminal | Anthropic's platform. Upload `SKILL.md` files via the web interface (Settings → Skills) or register via the Claude Code plugin marketplace with `/plugin marketplace add getur/quiz-html-agent`. |
| **CODEX** | Terminal CLI | Agentic coding tool that can load skill instructions from local files. Point it at a skill directory and it will follow the `SKILL.md` workflow. |

## Requirements

### quiz-from-docs

| Dependency | Purpose | Install |
|-----------|---------|---------|
| Python 3.10+ | Runtime | [python.org](https://python.org) |
| PyMuPDF (fitz) | PDF text extraction | `pip install PyMuPDF` |
| edge-tts | MP3 audio generation (Microsoft Edge TTS) | `pip install edge-tts` |
| Pillow (PIL) | Image processing / fallback image gen | `pip install Pillow` |
| google-genai | Gemini AI image generation | `pip install google-genai` |
| pytesseract | OCR for image text extraction | `pip install pytesseract` + [Tesseract OCR](https://github.com/tesseract-ocr/tesseract) |
| ffmpeg + openai-whisper | Video transcript extraction (optional) | `pip install openai-whisper` |
| Google API Key | Gemini image generation | Set `GOOGLE_API_KEY` env var |

Do not worry with the requirements, most of them will be automaticaty installed by your agent 

## Installation & Use

### Terminal (CLI Tools)

#### OpenCode

OpenCode automatically detects skills from your local filesystem. Skills trigger when your prompt matches their description.

```powershell
# Install Python deps for quiz-from-docs
pip install PyMuPDF edge-tts Pillow pytesseract google-genai
$env:GOOGLE_API_KEY = "your-key-here"

# Then simply describe your task:
#   "Create a quiz from these PDF documents"
#   "Build an HTML quiz from this JSON file"
```

OpenCode reads the `SKILL.md`, follows its workflow, and produces the output.

#### Claude Code

```powershell
# Register as plugin marketplace
/plugin marketplace add getur/quiz-html-agent

# Or install individual plugin packs
/plugin install quiz-from-docs@quiz-html-agent
```

#### CODEX

```powershell
# Point CODEX at the skill directory
codex --skill ./quiz-html-generator
```

### IDE (Graphical Tools)

#### Antigravity (example)

1. Open Antigravity editor
2. Click the skill icon in the sidebar (or open Command Palette → "Load Skill")
3. Browse to `quiz-html-agent/quiz-from-docs/` or `quiz-html-agent/quiz-html-generator/`
4. Select the `SKILL.md` file
5. The agent loads the skill — now describe your task in the chat panel

Other IDEs with skill support (Google CLI extension, Claude desktop app) follow a similar flow: point the tool at the skill folder and start prompting.

## Design Philosophy

Both skills follow **Duolingo-inspired methodology**:

- **Warm, encouraging tone** — explanations teach rather than label
- **At least 3 question types per quiz**, alternated, starting with MCQ or True/False
- **Every question maps to a specific learning competency**
- **Explanations never start with "Correct" / "Incorrect"** — teach, don't label

## Submodule Workflow

Each skill is its own Git repo. Changes must be committed and pushed from within each subdirectory:

```powershell
cd quiz-from-docs
git add .
git commit -m "your message"
git push
cd ..
git add quiz-from-docs
git commit -m "Update quiz-from-docs submodule pointer"
```

## License

[MIT](./quiz-from-docs/SKILL.md) for both skills unless otherwise noted.

---

*Built for educators, content creators, and anyone turning learning materials into interactive quizzes.*
