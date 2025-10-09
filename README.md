# Oscars Glossary Training

Oscars Glossary Training is a kid-friendly, browser-based Swedish ↔ English vocabulary trainer. It runs entirely on the client, stores each player’s progress locally, and can work offline once it has been loaded over HTTPS.

## Features

- **Player accounts** stored locally with salted+hashed passwords (educational use only).
- **Glossary builder** – add, edit, duplicate, and delete vocabulary lists; a quiz can’t start until there are at least ten terms.
- **Two quiz modes** – free-text “Type Mode” and multiple-choice “Guess Mode”, with quick switching from the quiz or results screens.
- **Adaptive scoring** via Levenshtein similarity (correct / partial / incorrect) and per-item feedback.
- **Progress history** per player with “retry incorrect” and “review difficult words” shortcuts.
- **Offline capable PWA** – service worker caches the app (works best when hosted on HTTPS/GitHub Pages).
- **Accessibility touches** – keyboard shortcuts, ARIA live announcements, high-contrast UI.

## Quick Start

```bash
git clone https://github.com/OscarJerez/glossary-trainer.git
cd glossary-trainer
```

No build step is required. Open `Glossary trainer/index.html` in a modern browser (Chrome, Edge, Firefox, Safari). For the best PWA experience, serve it over HTTPS or `http://localhost` with a static server.

### Serving locally (optional)

```bash
python -m http.server 8000
# then open http://localhost:8000/Glossary%20trainer/index.html
```

## Usage Tips

- Player data (glossaries, settings, quiz history) lives in the browser’s `localStorage`. Clearing site data resets everything.
- On the quiz screen you can use **Enter** to submit and **N** to advance, and **1–4** for choices in Guess mode (unless you are typing in the answer field).
- The mode toggle button is available within both the Quiz and Results views for quick switching.

## Deployment (GitHub Pages)

1. Commit the repo to GitHub.
2. In the repository settings, enable **Pages** → Deploy from **main** (or your chosen branch) – root directory.
3. After GitHub builds, visit the published URL (e.g. `https://<username>.github.io/glossary-trainer/Glossary%20trainer/index.html`).
4. The PWA/service worker will activate on first load over HTTPS.

## Tech Stack

- HTML5 + CSS3 + vanilla JavaScript (no frameworks).
- LocalStorage for persistence, Web Crypto API for password hashing.
- Service worker (`sw.js`) for offline caching, `manifest.webmanifest` for PWA metadata.

## Contributing / Issues

Bug reports and pull requests are welcome! Please feel free to fork the repo, open issues, or submit improvements as you spot them.

---
_Educational project — not intended for storing sensitive user data._
