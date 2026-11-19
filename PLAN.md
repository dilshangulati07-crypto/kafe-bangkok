# Kafé Bangkok — Project Plan

Find the right Bangkok café for your mood. Pick a vibe (date, green escape, work, aesthetic, budget), add must-haves and an area, and the site ranks cafés with a match score and a short reason why each one fits.

- **Type:** static website (HTML, CSS, JavaScript). No backend, no API keys.
- **Hosting:** Netlify (free).
- **Data:** `cafes.json`, 105 cafés, included with this plan.
- **Time:** about 1 week.

---

## 1. Features

### Must have (version 1)
1. **Mood picker:** 5 large buttons: Date spot, Green escape, Work / study, Aesthetic, Budget. User can pick more than one.
2. **Must-haves:** toggle chips for Garden, Outdoor, Riverside, Rooftop, Quiet, Good Wi-Fi, Specialty coffee, Desserts, Brunch, Open late / 24h, Pets.
3. **Area filter:** dropdown of zones (Old Town & Chinatown, Ari & Phaya Thai, Thonglor…), plus "Anywhere".
4. **Price filter:** ฿ / ฿฿ / ฿฿฿.
5. **Ranked results:** cards sorted by match score, each showing name, area, nearest station, price, tags, description, a **"92% match"** badge and a one-line **"why it fits"** (e.g. "Quiet · garden seating · near BTS Ari").
6. **Café detail:** clicking a card opens a panel with hours (if known), all tags, an **Open in Google Maps** link, and source links.
7. **Mobile-friendly** layout.

### Nice to have (version 2)
- **Surprise me** button: random café from the top 5 matches.
- **Favourites** saved in the browser (`localStorage`).
- **Share link:** encode the chosen filters in the URL so friends see the same results.
- **"Near BTS/MRT only"** toggle.

---

## 2. Data format (`cafes.json`)

```json
{
  "meta": { "moods": {...}, "features": {...}, "price": {...} },
  "cafes": [
    {
      "id": "daydream-believer",
      "name": "Daydream Believer",
      "area": "Ari",
      "zone": "Ari & Phaya Thai",
      "station": "BTS Ari",
      "price": 2,
      "moods": ["green", "aesthetic", "date"],
      "features": [],
      "description": "Glass garden house with hanging ferns and lots of natural light.",
      "hours": "Tue–Sun 10:00–21:30",
      "mapsUrl": "https://www.google.com/maps/search/?api=1&query=Daydream%20Believer%20Bangkok",
      "sources": ["https://..."],
      "verified": false
    }
  ]
}
```

- `moods` and `features` use the short keys listed in `meta`, so the UI labels come from one place.
- `station` is empty when the source didn't name one. Show "Check map" instead.
- `verified: false` means you haven't checked it yourself yet. **Before launch, check at least your top 20** on Google Maps (still open? hours?) and set `verified: true`. You can show a small "✓ checked" badge on those.

---

## 3. The match score — **you build this part**

This is the brain of the site and your interview story. Don't let AI write it. Plan it on paper first.

**Inputs:** the user's selected moods, must-haves, area, price. **Output:** a score from 0–100 and a list of reasons.

**Suggested approach** (you decide the exact numbers):

| Rule | Idea |
|---|---|
| Area | If the user picked an area and the café isn't in it → leave it out entirely. |
| Price | If the café is above the chosen price → leave it out, or subtract points (your choice, explain why). |
| Moods | Each selected mood the café has = points. Most of the score comes from here. |
| Must-haves | Each must-have the café has = points. Missing one = lose points (or exclude: decide). |
| Bonus | Small bonus for having a station, or for `verified: true`. |
| Normalise | Divide by the maximum possible points so the result is 0–100%. |

**Reasons:** while scoring, push a short phrase into a `reasons` array each time something matches (e.g. "garden seating", "near BTS Ari"). Show the top 3 on the card.

**Test it:** write 3 test cases by hand before coding. Example: "Mood = Work, Must-have = Open late → La Bonne Cafe and Le Café Phénix should be near the top." Check the site gives that result.

**Be ready to explain:** why you chose those weights, what happens with no filters selected, and how you'd change it if users complained results felt wrong.

---

## 4. Folder structure

```
kafe-bangkok/
├── index.html
├── css/
│   └── style.css
├── js/
│   ├── app.js        ← loads data, reads filters, renders cards (Claude Code can build)
│   └── score.js      ← scoreCafe(cafe, prefs) → {score, reasons}  (YOU build)
├── data/
│   └── cafes.json
├── README.md
└── netlify.toml      (optional)
```

`app.js` loads data with `fetch('data/cafes.json')`. Note: `fetch` doesn't work when you open `index.html` by double-clicking. Run a local server instead: `python -m http.server 8000`, then open `http://localhost:8000`.

---

## 5. Design direction

- Warm, coffee-shop feel, but not the usual brown: try **cream background, deep green text, terracotta or matcha accent**, or a dark espresso theme. Pick one and stay consistent.
- One display font for headings (Google Fonts) + one clean body font.
- Mood buttons big and friendly, like cards, with an icon each.
- Result cards: name + match badge on top, tags as small pills, "why it fits" in italic.
- Must work at phone width (~400px).

---

## 6. Prompt for Claude Code

Copy this into Claude Code from inside your `kafe-bangkok` folder:

> Build a static website called **Kafé Bangkok** using plain HTML, CSS and JavaScript (no frameworks, no build step). Read `PLAN.md` for the full spec and use `data/cafes.json` as the data source.
>
> Build: `index.html`, `css/style.css`, and `js/app.js`. The page has a hero, a mood picker (multi-select, 5 moods from `meta.moods`), must-have chips (from `meta.features`), an area dropdown (unique `zone` values plus "Anywhere"), a price filter, and a results list of cards sorted by score. Clicking a card opens a detail panel with hours, tags, the Google Maps link and source links. Make it responsive and accessible (keyboard focus, labels).
>
> **Do NOT implement the scoring logic.** Create `js/score.js` with only this stub, and call it from `app.js`:
> ```js
> // TODO (Dilshan): implement scoring. Return {score: 0-100, reasons: string[]}
> export function scoreCafe(cafe, prefs) {
>   return { score: 0, reasons: [] };
> }
> ```
> `prefs` should look like `{ moods: [], features: [], zone: "Anywhere", maxPrice: 3 }`. Results with score 0 should still show when no filters are selected.
>
> After building, explain each file to me section by section, as if teaching a beginner.

Then ask Claude Code follow-ups like "why did you use event delegation here?" until you understand every file.

---

## 7. Your week

| Day | Do |
|---|---|
| 1 | Create the folder, put `cafes.json` in `data/`, run the Claude Code prompt, read and understand every file. |
| 2 | Design the score on paper. Write your 3 test cases. |
| 3 | Write `score.js` yourself. Test against your cases. Fix. |
| 4 | Check your top 20 cafés on Google Maps; fix any closed ones, set `verified: true`. Add 5+ cafés you know yourself. |
| 5 | Polish design and mobile layout. Add one "nice to have" (Surprise me is easiest). |
| 6 | Push to GitHub. Deploy on Netlify (connect the GitHub repo, no build command, publish directory = root). |
| 7 | Write the README, take screenshots, add it to your portfolio and CV. |

---

## 8. README for GitHub (fill in)

```markdown
# Kafé Bangkok ☕
Find a Bangkok café that fits your mood: date spot, green escape, work, aesthetic or budget.

**Live:** https://<your-site>.netlify.app

## Features
- Mood + must-have + area + price filters
- Custom match score with "why it fits" reasons
- 100+ hand-tagged cafés across 9 Bangkok zones

## How the match score works
<2–4 sentences in your own words>

## Built with
HTML, CSS, JavaScript · hosted on Netlify

## What I'd add next
<your ideas>
```

---

## 9. CV line (after it's live)

**Kafé Bangkok** · Solo project · *HTML · CSS · JavaScript · Netlify*
- Built a café finder for Bangkok that ranks 100+ cafés by mood, must-haves, area and price.
- Designed a weighted match-scoring algorithm that explains each recommendation.
- Researched, structured and verified the café dataset in JSON.

---

## 10. Data notes

- Café list compiled 6 Nov 2026 from public guides: That Travel Blog, TripCanvas, Drip Roast, Visit Thailand Today and The Thaiger (each café's `sources` field links the page).
- Descriptions are written fresh, not copied. Keep it that way, and don't copy Google reviews or photos. Link to Google Maps instead.
- Cafés open and close often in Bangkok. Re-check your list every few months.
