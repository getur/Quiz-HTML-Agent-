# Quiz-HTML Agent

[![OpenCode](https://img.shields.io/badge/OpenCode-skill-blue)](https://opencode.ai)

A repository of two independent [OpenCode](https://opencode.ai) skills (and Claude-compatible) for **generating educational quizzes from documents** and **turning quiz data into standalone HTML files**. Each skill is a self-contained submodule with its own `SKILL.md`, scripts, and assets.

## Repository Structure

```
quiz-html-agent/
├── quiz-from-docs/         # Skill: extract quiz content from PDFs/text/images/videos
│   ├── SKILL.md            # Skill instructions & methodology (Duolingo-inspired)
│   ├── scripts/            # Audio gen, image gen, text extraction
│   ├── evals/              # Evaluation benchmarks
│   └── references/         # Reference materials
└── quiz-html-generator/    # Skill: build standalone HTML quizzes from structured data
    ├── SKILL.md            # Skill instructions & template reference tables
    ├── assets/             # HTML template (~2570 lines, dark MAZA theme)
    └── evals/              # Evaluation benchmarks
```

## Skills Overview

### [quiz-from-docs](./quiz-from-docs)
Creates a `quiz_[lesson].json` file from source documents. Supports **8 Duolingo-inspired question types** with **audio** (via `edge-tts`) and **AI-generated images** (via Gemini).

**Inputs:** PDFs, TXT, HTML, images (OCR), video (transcripts)  
**Output:** `quiz_[lesson].json` (never overwrites — appends `_1`, `_2` suffix)  
**Dependencies:** `PyMuPDF`, `edge-tts`, `Pillow`, `pytesseract`, `google-genai`  
**Audio:** 8 voice options across PT, PT-BR, EN, ES  
**Images:** Gemini 3.1 Flash (requires `GOOGLE_API_KEY`)  
**Scripts:** `scripts/generate_audio.py`, `scripts/generate_card_image.py`, `scripts/extract_text.py`

### [quiz-html-generator](./quiz-html-generator)
Takes structured quiz data (JSON, Markdown, Excel/CSV, DOCX, PDF) and produces a **standalone, dark-theme HTML file** featuring a full interactive quiz experience: splash screen, progress bar, 3 lives (hearts), XP/streak tracking, 8 question types, audio replay, and confetti on completion.

**Inputs:** JSON, Markdown, CSV/XLSX, DOCX, PDF  
**Output:** `<input-filename>.html` (self-contained, no external dependencies)  
**Template:** Dark MAZA theme, Inter font, Tabler Icons, custom `--clr-*` CSS variables

#### 8 Question Types

| Type | `type` value | Description |
|------|-------------|-------------|
| MCQ | `mcq` | Single-choice, tap-to-select cards |
| Multi-Select | `multi-select` | Pick multiple correct answers |
| True/False | `true-false` | Two large buttons |
| Fill-in-the-Blank | `fill-blank` | Type or drag tiles; near-miss detection |
| Matching | `matching` | Pair left/right items |
| Sequencing | `sequencing` | Drag-and-drop reorder |
| Numeric | `numeric` | Enter number with tolerance & unit |
| Flash Card | `flashcard` | Flip card, then select response |

## Getting Started

### Prerequisites

- **OpenCode** (recommended) or **Claude Code** / **Claude.ai** with skills support
- Python 3.10+ (for `quiz-from-docs` audio/image generation)
- Tesseract OCR (for image text extraction)

### Setup

```powershell
# Clone with submodules
git clone https://github.com/getur/quiz-html-agent.git
cd quiz-html-agent
git submodule update --init --recursive
```

### Using with OpenCode

Each subdirectory is a skill that OpenCode loads automatically. Trigger them by describing your task:

- *"Create a quiz from these PDF documents"* → loads **quiz-from-docs**
- *"Build an interactive HTML quiz from this JSON"* → loads **quiz-html-generator**

### Using quiz-from-docs (Python)

```powershell
pip install PyMuPDF edge-tts Pillow pytesseract google-genai
$env:GOOGLE_API_KEY = "your-key-here"
# Then ask OpenCode/Claude to create a quiz from your documents
```

### Using quiz-html-generator

Provide structured data (JSON/MD/CSV/DOCX/PDF) and the skill produces a standalone `.html` file. No additional dependencies needed.

### Using with Claude Code

Register this repository as a plugin marketplace via:

```
/plugin marketplace add getur/quiz-html-agent
```

## Design Philosophy

Both skills follow **Duolingo-inspired methodology**:

- **Warm, encouraging tone** — explanations teach rather than label ("Correct" / "Incorrect" are never the first words)
- **At least 3 question types per quiz**, alternated, starting with MCQ or True/False
- **Progressive difficulty** and clear competency targeting
- **Every question maps to a specific learning competency**

## Submodule Workflow

Each skill lives in its own Git repository. Changes within a submodule must be committed and pushed from inside that subdirectory independently:

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

[MIT](./quiz-from-docs/SKILL.md) for both skills unless otherwise noted in their respective repositories.

---

*Built for educators, content creators, and anyone who wants to turn learning materials into interactive quizzes.*
