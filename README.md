# Glossary Trainer

Glossary Trainer är en barnvänlig, helt statisk ordtränare (svenska ↔ engelska). All arkitektur och utveckling är skapad av Oscar Jerez. All logik körs i webbläsaren, data sparas lokalt och sidan kan fungera offline efter första laddningen.

## Funktioner

- **Ordlistor och quiz**: Bygg, döp om, duplicera och ta bort ordlistor. Två lägen: skrivläge och gissningsläge (flervalsfrågor).
- **Snäll rättning**: Levenshtein-baserad likhetsbedömning med feedback (rätt/delvis/fel) och historik för svåra ord.
- **Import/export (JSON)**: Dela ordlistor mellan lärare/elever via JSON-filer (helt lokalt).
- **PWA/offline**: Service workern cachar sidan för offline-användning över HTTPS.
- **Tillgänglighet**: Tangentbordsstöd, ARIA live och fokusstilar.

## Användningstips

- Data (ordlistor, inställningar, quizhistorik) sparas i `localStorage`. Rensa inte site-data om du vill behålla framsteg.
- Tangentbordsgenvägar i quiz: **Enter** för att skicka, **N** för nästa, **1–4** för svar i gissningsläge.
- Minimikravet för att starta ett quiz är 1 ord.

## Publicering (GitHub Pages)

1. Lägg koden i ett GitHub-repo.
2. Aktivera **Pages**: deploy från `main` (root-katalogen).
3. Besök den publicerade URL:en (t.ex. `https://<användare>.github.io/glossary-trainer/index.html`).
4. Service workern aktiveras vid första laddning över HTTPS.

## Teknik

- HTML, CSS, vanilla JavaScript (ingen ramverksberoende build).
- `localStorage` för all ihållande data (inga backend-anrop).
- `sw.js` + `manifest.webmanifest` för PWA/offline.

## Feedback

Felrapporter och förbättringsförslag är välkomna. Öppna ett issue eller skicka en PR.

---
_Utbildningsprojekt – inga känsliga uppgifter bör lagras._

## License / användning

Copyright (c) 2025 Oscar Jerez. All rights reserved.

Koden i detta repo får inte kopieras, säljas eller distribueras vidare
som egen produkt utan skriftligt tillstånd från upphovsmannen.
Du får gärna läsa, lära och inspireras, men inte bygga en kommersiell
kopia av Glossary Trainer.
