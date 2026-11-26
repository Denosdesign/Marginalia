# Marginalia · A reading journal

A local-first, single-user British English current affairs library. The interface is English-first, with Traditional Chinese vocabulary support. Two lessons from the conversation are preloaded as learning adaptations, not newly fact-checked news articles. Their exact original URLs were not available when building the app, so source labels are retained and links are left blank. The Tai Po lesson includes ten extra vocabulary items selected from the article.

## Getting started

- Quick trial: open `index.html` in a regular browser. File-preview viewers may disable JavaScript or IndexedDB. Local-file storage varies by browser; do not depend on it for a permanent library.
- Recommended: serve the whole folder from one stable HTTPS origin (a static host, e.g. GitHub Pages or Cloudflare Pages). No server-side runtime or API key is required. On iOS, visit that address in Safari and use Share > Add to Home Screen. The manifest and service worker support installation and offline shell caching on supported browsers. Speech can require network access depending on the selected voice.
- For local development, run `python -m http.server 8000` from the folder, then open `http://localhost:8000/`.
- Keep the same origin across updates. A different domain, port, browser profile or installed web app may have a separate database. Export a backup before moving.

## Learning loop

1. Read an article without explanations first. Use the read-aloud function if helpful; it requests en-GB and prefers an available UK English voice. Speed and font size are adjustable. The system voice controls the actual accent.
2. Highlight selected passages within a single paragraph; add a note or turn them into a card. Highlights use paragraph identifiers and exact offsets. Preserve the original article text; import a new ID for substantial revisions.
3. Save vocabulary and language patterns selectively. A term and its contextual meaning form the vocabulary key, rather than just the word. Related articles remain linked; different senses can have separate cards. English examples and Chinese meanings are included.
4. Submit a reading reflection: main idea, optional opinion, difficulty, comments. Export it to your AI teacher. Import returned feedback JSON. The interface does not send your data to an AI automatically. Export the learning profile periodically so the teacher can adapt future lessons and recycle weak vocabulary.
5. Review due cards using active recall, then choose Again / Hard / Good / Easy. A simple adjustable SM-2-inspired scheduling rule assigns the next date; Again returns in 10 minutes, Good generally starts at one day, Easy at four days. This is a heuristic, not FSRS or a scientifically calibrated memory prediction. Scheduled reviews create an append-only event log. Practice modes do not alter the schedule. Default 15 cards per session; mixed direction alternates English recognition and recall from meaning. Read the linked article again when a term is repeatedly missed. Prefer a handful of meaningful cards per lesson over saving everything indiscriminately.

## Storage design

IndexedDB database `marginalia-library`, version 1. Each store is keyed by a stable ID.

| Store | Purpose |
| --- | --- |
| lessons | Structured article sections, provenance, date, topic, region, level, vocabulary, patterns and social analysis |
| highlights | Lesson ID, section ID, paragraph index, character range, exact selected text and personal note |
| cards | Sense-specific term key or custom card, examples, source IDs, scheduling state |
| reviews | Individual scheduled review events, rating and next due date |
| progress | Reading status, saved articles, scroll position and last activity |
| feedback | Personal summary, opinion, difficulty, comments and teacher feedback |
| settings | Reading speed, font size, review direction, session limit and backup metadata |

The original content is separate from personal learning records; updates must not overwrite learning history. The language view derives a cross-article index from the stored lessons. Search is currently in-memory and sufficient for a personal text collection; for thousands of long articles consider indexed search and pagination. Schema version is explicit so later migrations can be implemented. Storage is local only; no cloud sync, accounts, or backend integration. Browser deletion, eviction and private browsing remain risks. Request persistent storage where supported and export regular JSON backups. Import merges without overwriting existing records by default; full replacement requires an explicit confirmation. No hidden external analytics or dependencies.

## AI lesson contract

Use `Settings > Copy AI instructions` or the example file. AI should generate data only, not modify the application source. Import checks the format version and required fields, skips duplicate lesson IDs and previews before committing. Use unique stable IDs, e.g. `housing-hk-2026-11-27`. Store original source URLs and dates when verified; mark unverified material accordingly. To update a factual article substantially, use a new ID rather than changing the paragraph text beneath existing highlights. Each vocabulary entry is `[term, Traditional Chinese meaning, English definition, example, CEFR]`. Each language pattern has `title`, `explanation`, `quote` and `examples`. For feedback return `{"format":"marginalia-feedback","schemaVersion":1,"lessonId":"existing-id","aiFeedback":"..."}`. Backups are also versioned JSON.

## Roadmap (not implemented)

For a cross-device library, migrate to a database-backed authenticated service with per-record conflict resolution, immutable lesson versions, full-text indexes and sync/export endpoints. To automate AI imports, add a server-side validation endpoint with authentication, idempotency and import review; never embed private API keys in the browser. More advanced learning tools could include cloze generation, semantic sense grouping, independent production tests, adaptive FSRS scheduling, review analytics and periodic rediscovery of older articles. Do not make these prerequisites for the current local-first workflow.

## Validation

JavaScript syntax checked with Node. Pure lesson validation and scheduling tested separately. Browser end-to-end testing was attempted but the managed Chromium environment blocks both local file and loopback URLs. Therefore UI, browser-specific persistence, selection behaviour on iOS, offline caching and actual speech playback still require testing on the target browser.