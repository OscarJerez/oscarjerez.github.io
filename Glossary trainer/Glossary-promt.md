# HTTPS Glossary Trainer (Swedish → English) with Login, Saved JSON, and Pre-Quiz Builder — Single-Page PWA (CodexGPT-5 Prompt)

**Goal**  
Build a kid-friendly, accessible vocabulary trainer where a player signs in (username/password), creates or loads their own glossary (Swedish → English), and must **enter the words to train before starting the quiz**. The app supports Type mode and Guess mode, partial-credit scoring, results with per-word similarity %, and review of difficult words. All data is saved per player and exportable/importable as JSON. **No timers.** HTTPS-ready and offline-capable (PWA), fully client-side.

---

## Output (print full code for each file)
1. `index.html` — single page; embedded CSS+JS; responsive; **no external libs**; HTTPS/PWA-ready  
2. `manifest.webmanifest`  
3. `sw.js` — service worker; offline cache  
- Include simple icons via data URLs or tiny PNGs/SVGs (safe for HTTPS).
- Add a short README section as an HTML comment at the top of `index.html` with deploy steps (GitHub Pages).

---

## High-Level Flow
1. **Auth Screen** → Register / Login (username/password; case-insensitive username uniqueness)  
2. **Glossary Builder** (required before first quiz):  
   - Player must add vocabulary pairs (Swedish + one or more English synonyms) **or** import JSON.  
   - **At least 10 entries required** to start.  
   - Manage glossaries: create, rename, duplicate, delete; select active glossary.  
3. **Quiz** (Type or Guess):  
   - Shuffle; progress bar; keyboard controls; ARIA feedback.  
4. **Results & Review**:  
   - Overall %; counts (correct/partial/incorrect); table with per-word similarity; “Retry incorrect only”; export results CSV.  
   - “Review session” for items with low similarity (<70%) as a separate short quiz.

---

## Functional Requirements

### A) Authentication (client-side, educational only)
- Register: username (unique), password (≥8 chars). Trim spaces; normalize to NFC.  
- Hash passwords **locally** using Web Crypto (PBKDF2 or SHA-256 + random salt); store only `{ username, salt, passwordHash }` in `localStorage`.  
- Rate limiting: after 5 failed logins, exponential backoff (e.g., 30s, then 60s).  
- Logout clears in-memory session. Persist session in `localStorage` so refresh keeps the user signed in until logout.  
- “Export My Data (JSON)” and “Import My Data (JSON)” per player (includes glossaries + history but **never** plain passwords).

### B) Data Model (stored in localStorage per user; downloadable as JSON)
```json
{
  "users": [
    {
      "id": "uuid",
      "username": "oscar",
      "salt": "base64...",
      "passwordHash": "base64...",
      "createdAt": "2025-10-09T10:00:00Z",
      "profile": {
        "activeGlossaryId": "uuid-or-null",
        "settings": {
          "mode": "type|guess",
          "showHintFirstLetter": false,
          "ignorePunctuation": true,
          "strictThreshold": 95,
          "diacriticInsensitive": true
        }
      },
      "glossaries": [
        {
          "id": "uuid",
          "name": "Default A",
          "createdAt": "ISO",
          "updatedAt": "ISO",
          "terms": [
            { "id":"uuid", "sv":"cykel", "en":["bicycle","bike"], "tags":[] }
          ]
        }
      ],
      "history": [
        {
          "id":"uuid",
          "glossaryId":"uuid",
          "date":"ISO",
          "mode":"type|guess",
          "overallPct": 87.5,
          "counts": { "correct": 18, "partial": 7, "incorrect": 5 },
          "perItem":[
            { "termId":"uuid","sv":"cykel","yourAnswer":"bicycle","expected":"bicycle","similarity":100 }
          ],
          "incorrectTermIds":["uuid","uuid2"]
        }
      ]
    }
  ],
  "currentUserId": "uuid-or-null"
}
```

### C) Glossary Builder (required pre-quiz step)
- UI to add a term: **Swedish** (`sv`) + **English** (`en`) where `en` accepts comma-separated synonyms and converts to an array.  
- Validate: both fields required; no duplicates (case/diacritic-insensitive).  
- List view with inline edit/delete; counters for total terms.  
- Import JSON (textarea/file) → preview → validate → merge or replace.  
- Export current glossary to JSON; export **all** player data to JSON.  
- Require **≥10 terms** to unlock “Start Quiz”.

### D) Modes
- **Type Mode**: free-text input; ENTER submits; optional hint (first letter); option to ignore punctuation.  
- **Guess Mode**: 4 choices (1 correct + 3 distractors) with keys 1–4; ensure distractors are plausible (similar length or same part of speech where possible).  
- Toggle mode at any time (setting persists). Shuffle order each run. Provide “Retry incorrect only” on results.

### E) Scoring (no timer)
- Normalize: trim; case-insensitive; **diacritic-insensitive** (å/ä/ö ≈ a/o equivalents for matching).  
- Typed answers use **normalized Levenshtein similarity**:  
  `similarity = 100 * (1 - distance / max(len(expected), len(answer)))`, clamped 0–100.  
  If multiple accepted answers exist, take the **max** similarity across them.  
- Thresholds: **≥90% → Correct**, **60–89% → Partially correct**, **<60% → Incorrect**.  
- Multiple-choice: 100% for correct, 0% otherwise.  
- Overall score = **average of item similarity** across all attempted items.

### F) Results View
- Show overall % (1 decimal), counts, and a responsive table:  
  **Swedish | Your answer | Expected (best match) | Similarity %**  
- Buttons: **Retry incorrect only**, **Play again (shuffle)**, **Export results (CSV)**.  
- “Review difficult words” lists terms with average similarity <70% over last 3 attempts.

### G) UI/Accessibility
- Clean, high-contrast, kid-friendly; big tap targets; readable fonts.  
- Keyboard controls:  
  - ENTER to submit in Type Mode  
  - Keys 1–4 in Guess Mode  
  - **N** for next  
- Announce correctness and progress via ARIA live region; avoid motion/flash.  
- Progress footer: “Question X of N”.

### H) Settings Panel
- Toggle Mode (Type/Guess), Hint (first letter), Ignore punctuation, **Strict mode** (≥95% = correct).  
- Import/Export JSON for glossary and full player data.  
- Reset to defaults; **delete account** (requires typing the username to confirm).  
- Persist all settings in `localStorage`.

### I) Security & HTTPS
- PWA with service worker; only register SW on **HTTPS** or `localhost`.  
- Add CSP meta: `default-src 'self'`.  
- Use only relative or `https:` URLs for icons/manifest.  
- Warn in UI: “Educational login stored locally; not for sensitive data.”

### J) Code Quality
- Plain **HTML/CSS/JS** (no frameworks, no CDNs).  
- Clear, descriptive names; small functions with JSDoc-style comments:  
  `registerUser`, `loginUser`, `hashPassword`, `loadCurrentUser`, `saveUserData`,  
  `createGlossary`, `addTermToGlossary`, `startQuiz`, `renderQuestion`,  
  `levenshteinDistance`, `similarityPercent`, `scoreTypedAnswer`, `scoreChoiceAnswer`,  
  `renderResults`, `retryIncorrectOnly`, `exportCSV`, `importGlossaryJSON`,  
  `exportPlayerDataJSON`, `diacriticNormalize`, etc.  
- Keep logic modular and easy for students to read.

### K) Acceptance Criteria
- Player cannot start a quiz until a glossary with **≥10 terms** exists (prompt to add terms).  
- Login/Logout works; passwords are never stored in plain text; **salted hash** is used.  
- Per-player glossaries and history persist across reloads, and can be exported/imported as JSON.  
- Partial-credit scoring visible per item and in totals; results table shows per-word similarity.  
- “Retry incorrect only” and “Review difficult words” function correctly.  
- Works offline after first HTTPS load (GitHub Pages OK).

---

## Sample default terms (optional starter list; still require player to add or confirm before first quiz)
```json
[
  { "sv": "cykel", "en": ["bicycle","bike"] },
  { "sv": "fönster", "en": ["window"] },
  { "sv": "lärare", "en": ["teacher"] },
  { "sv": "häst", "en": ["horse"] },
  { "sv": "bollen", "en": ["the ball","ball"] }
]
```

---

## Deliver exactly
- **`index.html`** with inline CSS/JS, manifest link, and conditional SW registration.  
- **`manifest.webmanifest`** with name, short_name, start_url, display, and icons.  
- **`sw.js`** caching `index.html`, manifest, and icons with a cache-first strategy.  
- Minimal, polished UI; **no timers**; **no external libraries**.
