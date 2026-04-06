# Nemawashi

**Multi-axis group consensus.** Single-HTML-file decision tool for the messy real-world case where "where should we go for the retreat?" is actually four questions tangled together.

> *Named after 根回し — the Japanese practice of building informal consensus before a formal decision. Literally "going around the roots."*

## The problem

Doodle and When2Meet solve one axis: *"when are you free?"*

Real group decisions are multi-axis. Planning a retreat means aligning on dates AND location AND hotel AND meal plan AND budget tier. Each person has hard constraints ("I can't do March") and soft preferences ("I'd prefer Goa over Rishikesh"). No tool handles this.

The current workflow is a WhatsApp thread with 47 messages, three spreadsheets nobody reads, and one person rage-quitting.

## What it is

- One self-contained `index.html` — open the file, it works
- Define as many decision axes as you need (dates, location, hotel, meals…), each with 2–12 options
- Participants score every option on every axis: **🚫 Block** / **😐 Okay** / **👍 Like** / **❤️ Love**
- The tool finds the combination that maximises group satisfaction while respecting any vetoes
- Tie-break is egalitarian: when totals match, the combo that's least bad for the least-happy person wins
- **No server. No accounts. No data leaving the device.**

## How it works

The trick is that **the group chat is the transport layer**. Nemawashi never sees any data in transit — it's just a calculator that runs in the browser.

1. **Setup** — organiser defines axes and options, clicks *Generate share link*. The whole config gets URL-encoded into the hash fragment (which browsers never send to servers). Send the link into WhatsApp / Slack / email.
2. **Respond** — each participant clicks the link. The page auto-switches to scoring mode. They tap to score each option, click *Generate share-back link*, get a URL bundling both the event and their scores. They send *that link* back into the chat.
3. **Results** — organiser clicks each share-back link as it arrives in chat. Each click auto-merges that response into the results. No copy-paste-debug. The winning combination, top-5 ranked alternatives, and per-axis heatmaps appear immediately.

(There's also a fallback "plain code" path for the encrypted-event variant or for power users who'd rather paste `NW:Ravi:3,1,0|2,1,1|3,1,1` directly into the textarea. The textarea on the Results tab still works exactly as before.)

For sensitive events, *Encrypted link…* gives you an AES-256-GCM (PBKDF2 200K) variant. Share the passphrase out-of-band.

## Features

**Setup**
- Add / remove / drag-reorder axes (up to 8)
- Add / remove options per axis (up to 12 each)
- Plain share link or encrypted share link with passphrase
- Auto-saves your draft to localStorage so you can come back to it

**Respond**
- Tap each option to cycle Okay → Like → Love → Block
- Mobile: swipe right/left to upgrade/downgrade
- Default = Okay, so participants only need to mark what they care about
- One-click *Generate share-back link* — produces a URL bundling event + response (~250 chars total). Send back into chat. Plain `NW:Name:scores` code available as a fallback.

**Results**
- Click share-back links from chat → auto-merge into results (one click per participant, no copy-paste). Bulk-paste codes also supported as a fallback
- Per-axis heatmaps: rows = participants, columns = options, colour-coded by score
- Top-5 combinations table with total score + fairness floor
- **Winning combination card** — the answer, large and obvious, with a 決 hanko stamp
- **What-if mode**: toggle participants in / out, ranking updates instantly
- **Deadlock view**: when no combination is valid, surfaces the closest-to-consensus options *and* "if X could flex on Y" suggestions
- **Export**: copy as text (paste-back into chat), download as PNG (lazy-loads `html2canvas` from CDN)
- Validation banner if a pasted code was generated for a different event structure

**Privacy**
- Event configs travel as URL hash fragments — never sent to any server
- Response codes travel through whatever channel the group already uses
- All computation is local; no analytics, no cookies, no tracking
- Encrypted-link variant uses AES-256-GCM with PBKDF2 200K iterations

## Run

Open `index.html` directly (works from `file://`) or serve the folder:

```
python3 -m http.server 8100
```

Then visit `http://localhost:8100/`.

## Tech

- Zero dependencies, zero build step, zero workers
- Pure JavaScript, ~1500 lines including CSS and the help / intro modals
- Combinatorial scoring is main-thread with `setTimeout(0)` yields above 5 K combos — sub-100ms for any realistic event (3–5 axes × 3–5 options)
- `crypto.subtle` for AES-256-GCM, `URLSearchParams` and `encodeURIComponent` for the plain hash format

## Tests

Open `tests.html` in the same folder. 51 unit tests cover combinations, validity, scoring, ranking, encode/decode round-trips, response-code parsing, name sanitisation, eventId stability, and the encrypt/decrypt round-trip.

## Series

Part of the [NakliTechie browser-native tools series](https://naklitechie.github.io/) — single-file web apps that run entirely in the browser. No server. No API keys. No data leaving the device.

## License

MIT — Chirag Patnaik, 2025
