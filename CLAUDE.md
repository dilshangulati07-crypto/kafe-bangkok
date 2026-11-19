# Kafé Bangkok — project rules for Claude Code

Read this file and `PLAN.md` before every task.

## What this is
A static website that helps people find a Bangkok café that fits their mood. Data lives in `data/cafes.json` (160 cafés; the site counts them itself, so never hard-code the number). Plain HTML, CSS and JavaScript (ES modules). No frameworks, no build step, no npm packages, no API keys, no backend of our own. Hosted on Netlify; the "Suggest a café" form is collected by Netlify Forms.

## Who is building it
Dilshan, a final-year IT student, building this as a portfolio project. He must understand every line.
- After each task, explain what you built, file by file, in simple English.
- Prefer clear, readable code over clever code. Add short comments where the logic isn't obvious.
- Other code calls `scoreCafe(cafe, prefs)` from `js/score.js` and must still work when it returns `{score: 0, reasons: []}` (that's what it returns when no moods or features are picked).

## Design: a morning-sky hero, then a quiet editorial page
The hero should feel like a bright Bangkok morning: a soft sky, a cup of coffee in your hand. Everything below it should feel like a real, premium café brand: a high-end café's menu or a design magazine. Clean, editorial and restrained. Typography does the work, not colourful boxes. Readable first, always.

### Palette (CSS variables in `:root`)
| Token | Hex | Use |
|---|---|---|
| `--navy` | `#1F2A44` | text, headings, primary buttons, selected pills, the hot cup |
| `--coffee` | `#8A5A2B` | small accents only: the match %, active states (saved heart, ticked Compare), number labels, the hero's italic word |
| `--cream` | `#FBF7F0` | page background and the top bar (with a hairline under it) |
| `--white` | `#FFFFFF` | cards, panels, pills, inputs |
| `--hairline` | `#E5DED2` | 1px borders on cards and panels, and rules between sections |
| `--line-strong` | `#948B7E` | outlines on pills and form fields (3:1 contrast, so they're easy to see) |
| `--grey` | `#6B6660` | small secondary text below the hero (where, reasons, tags, hints) |
| `--mist` | `#F2EFEA` | the barista's message bubbles |
| `--sky-top` / `--sky-mid` | `#C9E2F5` / `#E3F0FA` | the hero sky only (and the 404 page's small piece of sky) |
| `--focus` | `#2F6FB3` | the keyboard focus ring |

Below the hero that's the whole palette: cream or white, navy, hairlines, grey text and a little coffee. No pastel panels, no per-mood colours, no gradients, no neon.

### Typography (Google Fonts)
- Headings: **Bricolage Grotesque**, bold, tight letter-spacing. Section headings are big (about 40–64px, letter-spacing about -0.045em).
- Body and UI: **Manrope** 400/500/600/700.
- Small labels: spaced uppercase (letter-spacing about 0.14em), e.g. "01 / FIND", "WHY IT FITS", field labels. Write them in normal case in the HTML and uppercase them with CSS, so screen readers don't spell them out.
- **Instrument Serif italic** appears only in the hero (the accent word "*today*") and the 404 headline. No handwritten fonts, and no italic serif notes anywhere else.
- Prices: Noto Sans Thai supplies the ฿ sign.
- **Thai** (when the TH / EN switch is on TH): Thai letters use Noto Sans Thai (loaded by `js/i18n.js` only then); English words keep the fonts above. Thai is never letter-spaced and gets more line height. The hero's accent word is upright in Thai (Instrument Serif has no Thai).

### Hero (keep as it is)
- Full-width soft sky gradient (pale blue to warm cream) with soft CSS clouds, and a huge, very faint marquee line ("Bangkok · 7:00 coffee · garden seats · late-night lattes ·") drifting sideways (`aria-hidden`).
- Two floating Kafé Bangkok cups in SVG (an iced coffee with a straw, a navy hot cup with a lid), each with our own "K" mark, never another café's logo.
- A live Bangkok clock (Asia/Bangkok) in a corner pill, and a greeting for the time of day. At night it offers "Open late right now".
- Headline "Find your Bangkok café, *today*." in Bricolage Grotesque with the italic serif word in coffee brown.
- Phones: the cups shrink and sit under the headline. The marquee stays big but faint.

### Below the hero
- **Layout:** lots of white space between sections, one consistent grid and left edge (`.wrap`), and a hairline between sections. A small "01 / FIND" style label sits above each section heading.
- **No illustrations:** no drawings, leaves, cartoon avatars or decorative icons. Use plain text and numbers. Thin line icons only where they do a job: heart, been there, close, arrow, search.
- **Filters:** moods are a row of outlined pills (filled navy when chosen); must-haves are smaller outlined pills; inputs are white with a thin outline.
- **Café cards:** white, 1px hairline border, 12px radius, no shadow. Café name large, "area · station" in small grey with the price, the description, then tags as plain small text separated by " · ". The match ("92% match") is a small coffee-brown number top-right. Reasons in one small grey line. Heart and Been there are small icon buttons; Compare is a small checkbox.
- **Café of the day:** a small band right under the hero, between hairlines: "Café of the day" as a small spaced label with the Bangkok date, then the café's name large, its area, description and a "See details" button.
- **Neighbourhoods:** a menu-like list of zone names with café counts and top moods as text. Hover underlines the name.
- **Plan a café crawl (03 / Crawl):** the form reuses the finder's pills, dropdown and segmented picker. The plan is a numbered list of stops (coffee "01" labels, hairlines between) beside a small map with navy numbered pins, a hollow ring for the starting station and a dotted navy line.
- **Café stats (05 / Stats):** charts in plain HTML and CSS (no library): thin navy bars, longest first, the number at the tip, each chart under a navy rule; "13 of 160" figures with a thin meter. Every number counted from the data. The first row counts every café on the site (hand-picked, local and chain from OpenStreetMap) and the top 10 chains.
- **More cafés nearby (from OpenStreetMap):** under the results, between hairlines: a heading "Also nearby: N more cafés", one small grey line saying moods can't apply, the "Include chains" switch with a brand dropdown, then quieter white cards that look like ordinary cafés (no match %, heart or Compare; a small grey "Chain" tag where the match would be on chain cafés, nothing on local ones). A card's foot is a row of plain text links: "Open in Google Maps" (bold), then "Website", "Facebook" and the phone number when the café has them. No "From OpenStreetMap" labels or OpenStreetMap links on cards, pop-ups, headings, the map key or pin labels.
- **Suggest a café (07 / Suggest):** white fields on the cream page like the finder; problems show in small coffee-brown text under the field; the thank-you is a white hairline panel.
- **How it works:** three short columns, each with a small number label and text. No pictures.
- **Saved and the passport:** the same cards; the passport is a white hairline panel with a thin progress line and small hairline boxes for visited neighbourhoods.
- **Drawer, chat, compare:** white panels with hairline borders and navy text. Chat bubbles are light grey for the barista and navy for you. The barista's avatar is a small navy circle with a white "K".
- **Footer:** navy, small text, no decoration.
- **Motion:** gentle and mostly in the hero (floating cups, drifting clouds, the marquee); cards fade in. Everything stops for `prefers-reduced-motion`.
- Mobile first. Must look great at 380px and on desktop. Nothing ever scrolls sideways.

### Things to avoid
- Pastel panels, coloured cards, rainbow tags, heavy shadows, big rounded "app" boxes below the hero
- Cartoon drawings, mascots, leaves and decorative icons below the hero
- Purple gradients, neon glows, dark "tech" themes, glassmorphism
- Photos, stock images, or any real café's logo or branding (our cups carry only our own "K")
- Low-contrast text: navy text, AA contrast everywhere
- Emoji as icons, fake testimonials, fake statistics, lorem ipsum
- Generic copy like "Unlock your perfect coffee experience". Write plainly and warmly, like a friend who knows Bangkok cafés.

## Code rules
- Files: `index.html`, `404.html`, `admin.html`, `css/style.css`, `css/admin.css`, `js/app.js`, `js/admin.js`, `js/score.js`, `js/chat.js`, `js/clock.js`, `js/crawl.js`, `js/i18n.js`, `js/map.js`, `js/osm.js`, `js/store.js`, `js/suggest.js`, `js/ui.js`, plus `data/cafes.json`, `data/osm-cafes.json`, `tools/fetch-osm.mjs`, `favicon.svg`, `images/`, `netlify.toml`, `README.md` and `docs/` (TECHNICAL.md and screenshots). `data-raw/` holds the script's saved OpenStreetMap reply: never deploy it or put it in the Netlify zip.
- **OpenStreetMap cafés** (`data/osm-cafes.json`, about 500): made only by `node tools/fetch-osm.mjs --save-raw data-raw/osm-raw.json` (or `--from data-raw/osm-raw.json` to clean the saved reply again), never edited by hand, and never merged into `cafes.json`. Only reliable cafés are kept: a real (not generic) name, not tagged as closed, edited on OpenStreetMap since 2022, and at least 2 signs that someone has checked them (opening hours, a website or social link, a phone, a street address, a check_date/survey:date from 2023 on, or a known chain's branch). The rules live at the top of the script; don't loosen them without asking. "Open in Google Maps" searches name + branch + street/station/area + Bangkok; a chain with no branch or street gets a brand search centred on the café (`/maps/search/<brand>/@lat,lng,18z`), never a bare-coordinate link. The 160 hand-picked cafés stay the main results with their match %; OpenStreetMap cafés show separately ("Also nearby" under the finder, grey and outlined map pins, the stats) and only take the area, line, near BTS/MRT, open now and search filters. Chains stay hidden until "Include chains" is on. The file is never loaded with the page: only when "Show more cafés" is pressed, the map opens, the stats come near, or the barista counts chains (`needOsm` in `app.js`). The data is ODbL: the credit "Café locations © OpenStreetMap contributors (ODbL)" is legally required and must stay, small, in the footer and in the map's corner, and only there (the stats may name OpenStreetMap once). See "OpenStreetMap cafés" in docs/TECHNICAL.md.
- The top bar has five links (Find a café, Neighbourhoods, Ask the barista, Saved, Suggest a café) and the TH / EN switch. The links sit in the bar from 64em; below that they're in the Menu drop-down, and under 30em the Menu button is icon-only (to fit TH / EN). Anything new in the bar must still fit in one row at 360px and at 1024px, in both languages.
- **Every word of the interface is in `js/i18n.js`, in English and Thai.** Never write interface text straight into HTML or JS: add a key to both lists and use `data-i18n` (HTML) or `t('key')` (JS). Café data (names, descriptions, areas, zones, stations) stays in English; café names and descriptions get `lang="en"`. Mood, feature and price labels in English come from `meta`; their Thai is in `i18n.js` under the same keys. `score.js`'s reasons are translated where they're shown.
- `admin.html` edits the café list in the browser only and downloads a new `cafes.json`. It's `noindex` and must never be linked from the site.
- "Suggest a café" is a Netlify form (`data-netlify="true"`, honeypot `bot-field`). Netlify reads it from the HTML at deploy time, so every field must be written in `index.html`, not only added by JavaScript.
- Station positions live in `meta.stations` (the crawl planner starts from them). Café of the day follows Bangkok's date (`bangkokDate` in `js/clock.js`).
- Use ES modules (`<script type="module">`). Load data with `fetch('data/cafes.json')`.
- The only outside libraries are Leaflet 1.9.4 and its add-on Leaflet.markercluster 1.5.3, for the Map view and the crawl map: from cdnjs, pinned, with integrity hashes, loaded only when a map is first needed (`js/map.js`). Nearby cafés show as numbered navy circles (white with a navy ring when a circle holds only OpenStreetMap cafés), styled in style.css (not the add-on's colourful default theme). Map tiles come from OpenStreetMap, and their credit must stay visible on the map.
- Opening hours live in `openingHours` (per day, `null` when not fully known), BTS/MRT lines in `line` (a list), and map positions in `lat`/`lng` (`null` when unknown). See "Café data" in docs/TECHNICAL.md.
- Accessibility: real `<button>`s, labels on every input, visible focus styles, `aria-live` for result counts and chat messages, good colour contrast.
- Save user stuff (favourites, visited, last filters) in `localStorage`, wrapped in try/catch.
- No console errors. Test at mobile and desktop widths.
- Keep the data file the single source of truth. Mood and feature labels come from `meta` in `cafes.json` (Thai versions in `js/i18n.js`).

## Git and GitHub
- Repo: https://github.com/dilshangulati07-crypto/kafe-bangkok (public, branch `main`).
- Always commit and push as **dilshangulati07-crypto**: git identity `Dilshan Gulati <278357136+dilshangulati07-crypto@users.noreply.github.com>` (set in this repo's local git config), and push with the `dilshangulati07-crypto` GitHub account (`gh auth switch -u dilshangulati07-crypto` if another account is active).
- After every task, commit the changes with a clear message and push to `origin main`. Never commit secrets, `.env` files or deploy zips (see `.gitignore`).
