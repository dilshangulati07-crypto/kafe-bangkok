# Kafé Bangkok — prompts for Claude Code

**Setup:** put `CLAUDE.md`, `PLAN.md`, `PROMPTS.md` and `data/cafes.json` in a folder called `kafe-bangkok`. Open that folder in VS Code, open the terminal, and start Claude Code there.

**How to use:** paste **one phase at a time**. After each phase:
1. Run the site: `python -m http.server 8000`, then open `http://localhost:8000`.
2. Click around on desktop and phone width (browser dev tools → device toolbar).
3. Ask Claude Code to explain anything you don't understand before moving on.
4. Commit to Git: `git add . && git commit -m "Phase X done"`.

Small phases give much better results than one giant prompt.

---

## Phase 1 — Foundation and hero

```
Read CLAUDE.md and PLAN.md first and follow them exactly.

Phase 1: set up the project and build the page shell.
- Create index.html, css/style.css, js/app.js, js/ui.js, js/store.js, and js/score.js.
- js/score.js must contain ONLY this stub. Do not write any scoring logic:
    // TODO (Dilshan): implement scoring. Return {score: 0-100, reasons: string[]}
    export function scoreCafe(cafe, prefs) {
      return { score: 0, reasons: [] };
    }
- Set up the design system from CLAUDE.md as CSS variables, fonts and base styles.
- Build a sticky top bar: "Kafé Bangkok" wordmark with a small line-drawn cup icon, and links: Find a café, Neighbourhoods, Ask the barista, Saved.
- Build the hero: a short warm headline (write it yourself, plain and specific to Bangkok cafés), one line of supporting text, and a large hand-drawn SVG illustration of a coffee cup with gently rising steam, framed by monstera and pothos leaves. Two buttons: "Find my café" (scrolls to the finder) and "Ask the barista" (opens chat, which comes later; leave a placeholder).
- Load data/cafes.json in app.js and log the count to check it works.
- Mobile first. Respect prefers-reduced-motion.

When done, explain each file you created in simple English.
```

---

## Phase 2 — The finder (filters + results)

```
Phase 2: build the café finder. Follow CLAUDE.md.

Filters (build the state in a single `prefs` object: { moods: [], features: [], zone: "Anywhere", maxPrice: 3, nearStation: false, query: "" }):
- Mood picker: 5 large selectable cards from meta.moods (Date spot, Green escape, Work / study, Aesthetic, Budget). Each has its own small line-drawn SVG icon (not emoji). Multi-select.
- Must-have chips from meta.features. Multi-select.
- Area dropdown: "Anywhere" + every unique `zone`.
- Price: three toggle buttons ฿ / ฿฿ / ฿฿฿ meaning "up to this price".
- "Near BTS/MRT" toggle (only cafés with a station).
- Search box that matches café name or area.
- "Clear all" link.

Results:
- Filter by zone, maxPrice, nearStation and query in app.js. For every remaining café call scoreCafe(cafe, prefs) from js/score.js and sort by score (highest first), then by name.
- Show "Showing X cafés" in an aria-live region.
- Sort dropdown: Best match, Price low→high, Name A→Z.
- Result card: name (Young Serif), area + station (or "Check map" if no station), price (฿ symbols), mood and feature tags as small pills, the description, a "% match" badge (hide it when score is 0), and up to 3 reasons in a short handwritten-style line (Caveat) like a barista's note.
- A nice empty state with a small illustration and a "Clear filters" button.
- Save the last used filters to localStorage and restore them on load.

Do NOT write scoring logic. score.js stays as the stub.
Explain what you built when done.
```

---

## Phase 2½ — YOU write the score

Don't use Claude Code to write this. Read section 3 of PLAN.md, design your weights on paper, write `scoreCafe` in `js/score.js`, and test with your 3 test cases. You can ask Claude Code *questions* ("how do I loop over an array in JS?") but write the function yourself.

---

## Phase 3 — Café detail drawer, favourites, visited

```
Phase 3: details and saving. Follow CLAUDE.md. Don't touch js/score.js.

- Clicking a result card opens a side drawer (bottom sheet on mobile) with: name, area, zone, station, price, hours (or "Hours not listed — check Google Maps"), all tags with full labels, the description, the reasons from scoreCafe, an "Open in Google Maps" button (mapsUrl, new tab), and small "Sources" links.
- Drawer closes with an X button, the Escape key, and clicking outside. Trap focus while open and return focus to the card after closing.
- Heart button on cards and in the drawer to save favourites (localStorage via js/store.js).
- "Been there" check button to mark cafés as visited (localStorage).
- A "Saved" section/view listing favourites, with a count in the top bar.
- Show a small "✓ checked" badge on cafés where verified is true.

Explain what you built when done.
```

---

## Phase 4 — Ask the barista (chat assistant, no API)

```
Phase 4: build a chat assistant called "Ask the barista" in js/chat.js. It must work with NO API and no AI service. It is rule-based and uses scoreCafe from js/score.js to rank cafés. Follow CLAUDE.md.

UI:
- A floating round button (bottom right) with a line-drawn cup icon opens a chat panel. On mobile the panel is full screen. The hero "Ask the barista" button opens it too.
- Messages styled like notes on a café counter: the barista's bubbles on --paper, the user's bubbles in --leaf with white text. A small barista avatar drawn in SVG.
- aria-live on the message list. Close with X or Escape.

Conversation:
1. The barista greets the user warmly in one or two short lines and asks what kind of café they want today.
2. Offer quick-reply chips for each mood. The user can also type freely.
3. Then ask follow-ups with chips: area ("Anywhere" + zones), must-haves (a few common ones + "No, that's all"), and budget.
4. Build a prefs object from the answers, rank cafés with scoreCafe (apply the same zone/price filters as the finder), and reply with the top 3 as mini cards (name, area, % match, one reason). Each mini card opens the detail drawer. Offer chips: "Show me more", "Surprise me", "Start over", "Use these in the finder" (applies the prefs to the main finder and scrolls there).

Free-text understanding (keyword parser in chat.js):
- Map words to prefs. Examples: "date", "romantic", "girlfriend", "boyfriend" → date; "plants", "garden", "green", "nature", "trees" → green; "work", "study", "laptop", "wifi" → work (and feature wifi for "wifi"); "pretty", "instagram", "aesthetic", "photos" → aesthetic; "cheap", "budget", "student" → budget and maxPrice 1; "quiet", "calm" → feature quiet; "late", "night", "24" → lateNight; "dog", "cat", "pet" → pets; "river", "rooftop", "view", "brunch", "dessert", "cake" → the matching features.
- Detect zones by keywords: "ari", "thonglor", "ekkamai", "siam", "silom", "sathorn", "chinatown", "old town", "yaowarat", "lat phrao", "bang na", "thonburi", "phrom phong", "asok".
- If the message mentions enough, skip the questions and answer straight away. If nothing is understood, reply kindly and show the mood chips again.
- Keep a short memory in the conversation: "something cheaper" lowers maxPrice, "somewhere else" excludes the last results, "closer to BTS" sets nearStation.

Write the barista's lines yourself: short, friendly, a bit playful, plain English. No emoji. Vary the wording with a few alternatives per message so it doesn't feel robotic.

Explain the parser and conversation flow to me step by step when done.
```

---

## Phase 5 — Neighbourhoods, surprise, compare, share

```
Phase 5: extra features. Follow CLAUDE.md. Don't touch js/score.js.

- "Neighbourhoods" section: a grid of zone cards. Each shows the zone name, number of cafés, the most common moods there, and a small leaf or building line illustration. Clicking sets the area filter and scrolls to results.
- "Surprise me" button in the finder: picks a random café from the current top 10 and opens its drawer with a little steam animation on the button.
- Compare: a "Compare" checkbox on cards (max 3). A bar appears at the bottom showing the chosen cafés; clicking "Compare" opens a side-by-side table of area, station, price, moods, features, hours.
- Shareable links: keep the finder state in the URL hash using simple keys (for example #m=date,green&f=quiet&z=ari&p=2) and restore it on load and on hashchange. Add a "Copy link" button with a "Copied" message.
- "Café passport" in the Saved view: a progress line like "You've visited 7 of 105 cafés" and a stamp-style badge for each zone where the user visited at least one café.

Explain what you built when done.
```

---

## Phase 6 — Polish, about, footer, performance

```
Phase 6: polish. Follow CLAUDE.md. Don't touch js/score.js.

- "How it works" section: 3 short steps in plain English with small illustrations (pick your mood → add must-haves → get ranked cafés with reasons). Mention the score is a custom algorithm.
- Footer: short line about the project ("A student project by Dilshan Gulati"), links to GitHub and portfolio as placeholders, and a "Café data compiled from public guides" note with the source sites listed from cafes.json.
- Add a favicon (cup SVG), page title and meta description, and Open Graph tags.
- Check accessibility: keyboard-only navigation through filters, cards, drawer and chat; focus outlines; contrast.
- Check mobile layouts at 380px, 768px and 1280px. Fix anything that overflows or looks cramped.
- Lazy-render results if needed so the page stays fast.
- Remove console.logs and unused CSS.
- Write a README.md using the template in PLAN.md section 8, leaving the "How the match score works" part for me to write.

Then give me a checklist of anything I should test by hand before deploying to Netlify.
```

---

## Phase 7 — You

- Check your top 20 cafés on Google Maps and set `verified: true`.
- Add 5–10 cafés you know yourself to `cafes.json`.
- Write the score explanation in the README.
- Push to GitHub, deploy on Netlify, add it to your CV and portfolio.

## Handy follow-up prompts
- "Explain js/chat.js like I'm a beginner, function by function."
- "Why did you structure it this way? What's a simpler alternative?"
- "The site looks too generic here: [describe]. Make it feel more like a bright café full of plants, following CLAUDE.md."
- "Review my scoreCafe function. Don't rewrite it; point out bugs and edge cases."
