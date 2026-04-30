# PRD: Calgary Commute Honest Calculator

**Hackathon prompt:** Build a tool that saves someone 60 minutes.
**Time budget:** ~2 hours, solo.
**Stage:** PRD + MVP (single static page).

---

## 1. Problem

Calgary suburb-to-downtown commuters default to driving out of habit. Google Maps tells them "30 min driving vs 45 min transit" and they pick driving — but that comparison hides parking cost, parking-search time, the walk from the parking lot, gas, vehicle wear, and the fact that 30 minutes of driving is 0 minutes of usable time while 45 minutes of CTrain is 45 minutes you can read, work, or rest.

Existing tools (Google Maps, Calgary Transit app, Waze) optimize for door-to-door minutes. None of them tell you the **honest** trade — total cost in dollars *and* the productive time you get back.

## 2. Target user

**Suburb-to-downtown commuter who currently drives by default.**

Specifically the routine commuter, not the one-off visitor — because the win compounds. One smart switch saves a few minutes; switching three days a week for a month is where the "60 minutes" claim becomes real.

Not in scope:
- Suburb-to-suburb commuters (transit usually loses, calculator is uninteresting)
- Downtown residents (already on transit or walking)
- Out-of-town visitors

## 3. Key insight

> **We don't make your trip shorter. We make your time count.**

The product surfaces two things Google Maps doesn't:
1. **Total cost of the drive** — fare/parking/gas/vehicle wear in one number
2. **Productive minutes** — minutes where hands AND eyes are free. Driving = 0 productive minutes (eyes on road, hands on wheel). Transit = ~80–90% of in-vehicle time is productive (excludes walk to station, wait time, transfers).

Podcasts and audiobooks are not productive minutes by this definition. This is a deliberate, defensible line: a productive minute is one where you could be answering email, reading, or sleeping. A judge asking "but I listen to podcasts while driving" gets the answer: "Then driving is fine for you. Our tool is for the minutes where you want your hands and eyes back."

## 4. MVP scope (what gets built in 2 hours)

**One screen. One decision. Side-by-side comparison.**

### Inputs
- Origin: dropdown of 4 hardcoded suburbs
  - **Mahogany (SE)** — no LRT, BRT Route 302 to downtown
  - **Evergreen (SW)** — no LRT, bus connection to Red Line at Fish Creek-Lacombe
  - **Saddle Ridge (NE)** — Saddletowne Blue Line terminus in-community
  - **Tuscany (NW)** — Tuscany Red Line terminus in-community
- Destination: hardcoded as "Downtown Calgary" (7th Ave transit corridor)
- Time of day: dropdown (AM rush / midday / PM rush)
- Day of week: weekday / weekend toggle

### Outputs (side-by-side cards)
For each option (DRIVE / TRANSIT):
- Door-to-door minutes
- Productive minutes (hands + eyes free)
- Total cost ($)
  - Drive: gas + parking + vehicle wear (CRA per-km rate)
  - Transit: fare ($3.70 single ride, 2026 rate — verify before demo)
- One-line summary: "X min trip, Y productive min, $Z"

### The verdict
A clear recommendation banner: **TAKE THE CTRAIN** or **JUST DRIVE** based on a single weighted score:
- Score = total cost + (non-productive minutes × time-value)
- Time-value is hardcoded for MVP at $25/hr (median Calgary wage proxy)
- Lower score wins

### Out of scope for v1 (write down so you don't get tempted)
- Live traffic / live transit data — use realistic hardcoded estimates
- Maps / route visualization
- User accounts, history, notifications
- Slider for user-adjusted time-value (this was Option C; defer to v2)
- Weather penalty
- Park-and-ride hybrid options
- Mobile-specific UI polish

## 5. Riskiest assumption

**That the verdict will surprise the user often enough to matter.**

If the calculator says "drive" for all 4 suburbs in all 4 time slots, the demo is dead. Quick gut check on the 16 cells (4 suburbs × 4 time-of-day combos):
- Tuscany + AM rush → transit should win (LRT terminus, downtown parking $25+)
- Mahogany + midday → drive should win (no LRT, bus is slow off-peak)
- Saddle Ridge + PM rush → transit should win (Deerfoot is brutal, Blue Line terminus right there)
- Evergreen + weekend → drive should win (transit requires transfer, parking is cheaper/free on weekends downtown)

If the matrix has at least 4–5 cells where the verdict is non-obvious or flips against intuition, the demo lands. **Build the data table for these 16 cells before writing any UI code.**

## 6. Success metric (for the demo, not for users)

A judge sees the verdict flip between two cells in under 10 seconds and says "huh, I didn't expect that." That's the moment.

Secondary: judge can articulate the productive-minutes definition back to you without prompting. If they can, the framing landed.

## 7. Stack

- Single-file `index.html`
- Tailwind via CDN
- Vanilla JS, no framework
- Hardcoded data object for the 16-cell matrix
- Deploys to any static host (or runs from `file://`)

No build step, no dependencies to install, no API keys. Solo dev in 2 hours cannot afford to debug tooling.

### 7.1 Easiest path (locked)

We ship **`index.html` only** in-repo—markup, Tailwind CDN, and vanilla JS in one file. No split bundles, no npm, no framework. The 16-cell commute matrix lives in a `MATRIX` object in the same file; **weekend vs weekday** is handled with small runtime adjustments (cheaper weekend parking, slightly shorter drives, worse Sunday-style transit for Evergreen) so we do not duplicate 32 full cells.

**Deploy:** open locally (`file://`) or drag to any static host (Netlify Drop, GitHub Pages, S3, etc.).

## 8. Build order (suggested, ~2 hours)

1. **0:00–0:20** — Fill the 16-cell data matrix with realistic numbers. This is the actual product. Source: Google Maps for drive times, Calgary Transit trip planner for transit times, calgaryparking.com for downtown rates. Hardcode everything.
2. **0:20–0:40** — Sketch the single-screen layout (two cards + verdict banner + 4 inputs).
3. **0:40–1:30** — Build the HTML/JS. Inputs update → lookup in matrix → render cards → compute verdict.
4. **1:30–1:45** — Pick the 2 cells you'll demo (one obvious, one surprising). Pre-test that the verdict flip is dramatic.
5. **1:45–2:00** — Write the 30-second pitch. Practice it twice.

## 9. The 30-second pitch (draft)

> Calgary commuters spend 71 hours a year stuck in traffic. The tools we use — Google Maps, Waze — optimize for door-to-door time. But they hide the real cost: parking, gas, and the fact that driving steals every minute of your trip. Our calculator shows the honest comparison: total dollars *and* productive minutes — minutes where your hands and eyes are free. For a Tuscany commuter at 8 AM, the CTrain costs $21 less and gives you back 35 productive minutes. Three days a week, that's over an hour a week reclaimed. We don't make your trip shorter. We make your time count.

## 10. Open questions to resolve before building

- [ ] Verify current Calgary Transit single-ride fare (was $3.70; check 2026 rate)
- [ ] Verify current downtown parking daily max (was ~$25; check ParkPlus rates)
- [ ] Decide: include vehicle wear in cost? (CRA rate is ~$0.70/km — adds up fast and strengthens the case for transit, but some judges will call it abstract)
- [ ] Decide: show the math, or just the verdict? (Showing math = more credibility; just verdict = cleaner demo)
