# Video walkthrough: recording guide and script

**Target length:** 1:45–2:00 (the handbook asks for 1–2 minutes; stay under 2:00).
**Goal:** show the value in the first 20 seconds, prove it on real data, then explain the build and what's next.

---

## Part 1. Before you record (10 minutes)

### Setup
1. **Run the production build**, not dev mode. This hides the Next.js "N" badge and is faster:
   ```bash
   cd ~/WebstormProjects/caprae-leadradar
   npm run build && npm start        # opens on http://localhost:3000
   ```
   If port 3000 is busy, stop the other server first or use `npm start -- -p 3001`.
2. **Warm the cache** so the demo is quick. In a normal browser window, click **Try it: HVAC companies in Austin, TX** once and wait until the progress line disappears (about 15 s).
3. **Open a private / incognito window** at http://localhost:3000. It starts empty, but the server cache is warm, so results load in seconds.
4. In that window, add your name after the first search: **Buy box → About you → Your name**, so outreach drafts are signed.

### Screen and audio
- Browser window about **1440 × 900**, zoom **100%**, bookmarks bar hidden, other tabs closed.
- macOS notifications off (Focus → Do Not Disturb).
- Recorder: **Loom** (easiest, gives a share link) or **QuickTime** (`Cmd + Shift + 5` → Record Selected Portion, microphone on).
- Quiet room, headphones or AirPods mic, speak a little slower than normal.

### Also open, in separate tabs for the last section
- The GitHub README on the architecture diagram: https://github.com/mawaisismail/caprae-leadradar#3-architecture
- Optional: a SaaSquatch demo screenshot showing the table full of N/A (`docs/screenshots` or the YouTube demo paused at ~0:40).

---

## Part 2. The script (read this while recording)

About 290 words, roughly 2:00 at a calm pace. **Bold** = what to click or show.

### 0:00–0:15 · The problem
**Show:** the SaaSquatch demo, paused on the results table with lots of N/A (or just start on the LeadRadar empty screen).

> "SaaSquatch is great at producing lead lists. But I went through its demo frame by frame, and the user is still left with three questions: who do I call first, why now, and what do I say? So I built LeadRadar, a quality-first layer on top of SaaSquatch."

### 0:15–0:35 · Find real businesses
**Show:** the LeadRadar empty screen. **Click** *Try it: HVAC companies in Austin, TX*. Let the rows stream in.

> "You pick an industry and a city. LeadRadar pulls real, currently listed businesses from OpenStreetMap in a couple of seconds. You can also drop in a SaaSquatch CSV export, and the columns are mapped automatically. Every lead is then enriched on its own: we read the company website politely, respecting robots.txt, verify the email domain has a real mail server, and merge duplicates."

### 0:35–1:05 · Prioritise with evidence
**Show:** the ranked table and the stats strip. **Click** the top lead (Efficient AC, Electric & Plumbing). Scroll the *Why it ranks* tab slowly.

> "Now it's ranked. Because SaaSquatch serves acquisition entrepreneurs, there's a 'Businesses to buy' lens: succession pressure, AI value-creation upside, owner reachability and buy-box fit. And every point is backed by evidence. This company has been in business since 1976, calls itself family-owned, and names its CEO, Molly. So the next step isn't 'contact the company', it's 'call and ask for Molly'. Franchises and chains get pushed down, because there's no independent owner to buy from."

**Optional (2 seconds):** click **Buy box**, move one weight slider, close it.
> "Tune the buy box and rankings update instantly."

### 1:05–1:25 · Reach out
**Click** the *Outreach* tab → **Generate**. Scroll through the email, call opener and follow-ups.

> "SaaSquatch's email generator asks you to type three paragraphs of context. Here there's nothing to type: the email, the cold-call opener, a LinkedIn note and two follow-ups are written from the verified signals. One click opens your mail app and logs the touch in the pipeline."

**Click** close (X) → **Pipeline** tab for 2 seconds → **Export** menu (hover HubSpot / Pipeline brief).

> "Leads move through a pipeline, and export straight to HubSpot, Salesforce, a call sheet or a one-page brief for the deal team."

### 1:25–1:45 · How it's built
**Show:** the README architecture diagram on GitHub.

> "It's Next.js 16 and TypeScript end to end. Serverless API routes stream results as they arrive. Data comes from OpenStreetMap, company websites and DNS checks. Results are cached in libSQL, SQLite locally and Turso in production, and it's built to deploy on Vercel. Outreach plugs into Claude, with a template fallback. There are 40 unit tests plus browser tests."

### 1:45–2:00 · What's next and close
**Show:** back on the ranked list.

> "Next, I'd add state business-registry lookups to find more owner names, licensed sources like Google Places for wider coverage, and learn the scoring weights from which leads actually convert. Thanks for watching."

**Stop recording.**

---

## Part 3. Cheat sheet: the stack in one breath

Use this if they ask follow-up questions, or if you want a slightly longer stack section.

| Layer | What we used | One-line reason |
|---|---|---|
| App | **Next.js 16** (App Router), **React 19**, **TypeScript** (strict) | One codebase for UI and APIs |
| Styling | **Tailwind CSS v4**, custom white/slate/blue tokens, Geist font, lucide icons | Clean, consistent, no heavy UI library |
| APIs | Next.js route handlers (Node serverless), **NDJSON streaming** | Results appear as each site finishes |
| Discovery | **OpenStreetMap**: Nominatim (geocoding) + Overpass (businesses), 3 mirrors raced | Real, free, open data with no credits |
| Enrichment | Native `fetch` + **Cheerio**, custom **robots.txt** parser (RFC 9309) | Fast, polite, serverless-friendly |
| Verification | Node **DNS MX** lookups, email syntax/role/disposable checks | Catches dead emails without spam-like probing |
| Data quality | Union-find **dedupe** on domain, phone and fuzzy name | Merges the same business across sources |
| Scoring | Pure TypeScript, runs in the browser | Instant re-ranking, fully explainable |
| AI | **Claude** via `@anthropic-ai/sdk` + **Zod** structured output, template fallback | Drafts grounded only in verified facts |
| Storage | **libSQL**: SQLite locally, **Turso** in production; two-tier cache | Same database everywhere, works on serverless |
| CSV | **PapaParse** | SaaSquatch import; HubSpot/Salesforce export |
| Testing | **Vitest** (40 tests), **Playwright** browser checks, live smoke script | Confidence the real flows work |
| Hosting (planned) | **Vercel** serverless + **Turso** | Pay-per-use, bursty workload, shared cache |

---

## Part 4. How to make it better (roadmap talking points)

Pick 2–3 for the video; keep the rest for the interview.

**Data coverage and accuracy**
1. **Secretary of State registry lookups:** real owner/officer names and formation dates for owners not named on their websites.
2. **Licensed sources** (Google Places, Yelp Fusion) behind the same lead interface, for cities where OpenStreetMap is thin. Dedupe already handles multiple sources.
3. **Headless-browser worker** (Playwright) for JavaScript-only websites, run only for top-tier leads to control cost.

**Smarter prioritisation**
4. **Learn scoring weights** from pipeline outcomes (which leads reached "In conversation" or "NDA/LOI").
5. **Change alerts:** re-crawl saved leads weekly and flag new signals, like a new "we're hiring" page or an owner stepping back.
6. **Lookalike search:** "find more businesses like this A-tier lead" in nearby cities.

**Workflow and integrations**
7. **Direct CRM sync** (HubSpot and Salesforce OAuth) instead of CSV files.
8. **Sequencing:** schedule the day-3 and day-8 follow-ups and track replies.
9. **Team features:** accounts, shared buy boxes, lead assignment and activity history.

**Product and scale**
10. **Deploy** to Vercel + Turso, add authentication and per-user rate limits.
11. **Background job queue** for large lists (thousands of leads) with progress notifications.
12. **Compliance:** CAN-SPAM/GDPR-friendly unsubscribe handling and data-retention controls.

---

## Part 5. After recording

- [ ] Watch it once end to end: audio clear, nothing private on screen, under 2:00.
- [ ] Upload to **Loom** or **YouTube (Unlisted)**, and check the link works in a private window.
- [ ] Put the video link and https://github.com/mawaisismail/caprae-leadradar in the submission email (see `docs/SUBMISSION.md`).

### If something goes wrong mid-recording
- **Search is slow or fails:** cut and use **Load a sample SaaSquatch export** instead; the script still works ("or import your SaaSquatch CSV…").
- **A website shows "unreachable" or "captcha":** point at it and say *"sites with bot protection are flagged for manual review, never bypassed"*. That's a strength, not a bug.
- **You stumble:** pause 2 seconds and repeat the sentence, then trim it in Loom or iMovie.
