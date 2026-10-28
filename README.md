<div align="center">

# 🕯️ Ofrenda

**A memory altar that never leaves your machine.**

Add the stories, foods and songs of someone you miss. A local open-weight model helps you remember them, and only from what *you* shared.

![Python](https://img.shields.io/badge/python-3.10%2B-blue)
![Flask](https://img.shields.io/badge/flask-3.x-lightgrey)
![Ollama](https://img.shields.io/badge/runs%20on-Ollama-black)
![Model](https://img.shields.io/badge/model-Gemma%20(open--weight)-orange)
![License](https://img.shields.io/badge/license-MIT-green)
![Hacktoberfest](https://img.shields.io/badge/Hacktoberfest-2026-purple)

*Built for the Hacktoberfest Open-Source AI Challenge, Week 4: **Ghost in the Machine***

<!-- Add a screenshot or GIF here once you have one: -->
<!-- ![Ofrenda screenshot](docs/screenshot.png) -->

</div>

---

## The idea

On Día de los Muertos, families build an *ofrenda* for the people they miss: a photo, a candle, their favorite food, their song. Someone is only truly gone when nobody says their name anymore.

A family's grief archive is some of the most private data they own, so it should not live on a server they don't control. Ofrenda keeps everything on your computer and uses an **open-weight model running locally** to help you remember.

## Features

- 🕯️ **Light a candle**: create an altar with a name, years, relationship and photo.
- 📖 **Add memories** in any language: stories, favorite foods, songs, sayings.
- ✍️ **Write a remembrance**: the local model drafts a short tribute using *only* your memories. It is told never to invent names, places or events. You can edit it before saving.
- 🗣️ **Ask the Memory Keeper**: ask "What did she cook on Sundays?" and get an answer with citations like `[2]`.
  - It speaks *about* the person, never in their voice. No fake séance.
  - If nobody shared the answer, it says so instead of guessing.
- 📦 **Export as one HTML file**: no server, no JavaScript, no external fonts. Opens offline in any browser, in any year.
- 🛟 **Graceful fallback**: if Ollama isn't running, a labelled rule-based mode keeps the app usable.

## Quick start

### 1. Install a local model

Install [Ollama](https://ollama.com), then pull a Gemma model:

```bash
ollama pull gemma3:4b
ollama list        # confirm the exact model name you have
```

### 2. Run Ofrenda

**Windows (PowerShell / VS Code terminal)**

```powershell
git clone https://github.com/noorulhuda07/ofrenda.git
cd ofrenda
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
python seed_demo.py     # optional: creates a fictional demo altar
python app.py
```

**macOS / Linux**

```bash
git clone https://github.com/noorulhuda07/ofrenda.git
cd ofrenda
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
python seed_demo.py
python app.py
```

Open **http://127.0.0.1:5000**.

> The first model request loads the model into memory and can take a minute on a CPU-only laptop. Try a smaller model such as `gemma3:1b` if it's too slow.

## Configuration

| Variable | Default | Purpose |
|---|---|---|
| `OFRENDA_MODEL` | `gemma3:4b` | Preferred Ollama model. If it isn't installed, the app uses any installed Gemma, then any other chat model. |
| `OLLAMA_HOST` | `http://localhost:11434` | Where Ollama is listening. |
| `OFRENDA_DATA` | `./data` | Where altars and photos are stored. |
| `PORT` | `5000` | Web server port (always bound to `127.0.0.1`). |

PowerShell example: `$env:OFRENDA_MODEL="gemma3:1b"; python app.py`

## How it works

```
Browser (vanilla JS)  ->  Flask API (127.0.0.1)  ->  Ollama (local Gemma)
                               |
                          ./data/*.json  (plain files you own)
```

1. **Memories are numbered** and passed to the model: `[1] (food) tamales de rajas ...`
2. **Strict system prompts** tell the model to use only those memories, cite them, never impersonate the person, and treat memory text as data rather than instructions.
3. **A small keyword retriever** selects the most relevant memories per question. It splits on whitespace, so it works for scripts like Telugu and Devanagari as well as English.
4. **Citations are parsed** from the answer and shown next to it so you can see exactly where each claim came from.
5. **Export** renders the altar into a single escaped HTML file with the photo embedded.

## Project layout

```
app.py            Flask server + JSON API
llm.py            Ollama client, prompts, retriever, offline fallback
store.py          JSON-file storage (atomic writes, validated ids)
export.py         One-file offline HTML export
templates/        index.html
static/           app.js, style.css (no build step, no external requests)
seed_demo.py      Fictional demo data for screenshots and demos
tests/            Unit tests (standard library only)
```

## Privacy and safety

- Server binds to `127.0.0.1` only. Nothing is sent to any cloud service.
- Data is plain JSON in `./data`: readable, copyable, deletable. `data/ofrendas/` and `data/photos/` are git-ignored, so real memories are never committed by accident.
- The UI renders user text with `textContent`, and the export HTML-escapes everything.
- IDs are validated against path traversal; photo uploads are extension-checked and capped at 8 MB.
- Open-weight models can still make mistakes. Treat drafts as a starting point and edit them.

## Tests

```bash
python -m unittest discover -s tests -v
```

Covers storage validation, retrieval (including Telugu tokens), offline fallback, and HTML-escaping in the export.

## Why open-source AI?

- **Private**: the stories never leave your machine or train someone else's model.
- **Offline**: the model, the app and the exported altar all work without internet.
- **Durable**: not tied to a memorial service that can change terms or shut down.
- **Controllable**: owning the prompts means I could enforce the rule that matters most, *never speak in the voice of the dead*.
- **Free to run**: no per-question cost.

## Roadmap

- [ ] Voice-note recording with local Whisper transcription (oral history)
- [ ] Printable ofrenda poster export
- [ ] Multiple photos per altar
- [ ] Local embeddings for better retrieval on large archives

## Contributing

Issues and PRs are welcome. Please never include real people's private memories in issues, screenshots or tests; use fictional data like `seed_demo.py`.

## License

[MIT](LICENSE) © 2026 Noor Ul Huda

## Acknowledgements

[Ollama](https://ollama.com), [Gemma](https://ai.google.dev/gemma), [Flask](https://flask.palletsprojects.com), and the DEV + MLH Hacktoberfest community.
