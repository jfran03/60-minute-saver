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

## 11. Rebalance spec (for matrix calibration)

Purpose: prevent the score model from always choosing transit, while keeping the story honest and defensible.

### 11.1 Outcome targets

- Weekday outcome split target (16 cells): **Transit 10-12 wins, Drive 4-6 wins**
- Weekend outcome split target (16 derived cells): **Transit 7-10 wins, Drive 6-9 wins**
- Keep these anchor cells fixed:
  - Tuscany + AM rush (weekday) -> **Transit wins**
  - Mahogany + Midday (weekday) -> **Drive wins**
  - Saddle Ridge + PM rush (weekday) -> **Transit wins**
  - Evergreen + Weekend (at least midday/off-peak) -> **Drive wins**

### 11.2 Margin targets (how decisive each verdict should feel)

- Clear wins (most cells): score gap **>= 4.0**
- Non-obvious flips (demo-worthy cells): score gap **1.0 to 3.5**
- Avoid coin-flips in default demo states: score gap **< 1.0** should be rare (max 1-2 cells)

### 11.3 Numeric guardrails for data entry

Use these when editing the 16 base cells:

- **Drive time (m):**
  - AM/PM rush: usually 34-55
  - Midday/off-peak: usually 26-42
- **Transit time (m):**
  - LRT-strong suburbs (Tuscany, Saddle Ridge): usually 45-58
  - Transfer-heavy suburbs (Evergreen, Mahogany): usually 52-80
- **Transit productive minutes (prod):**
  - Set using: `prod = round(inVehicleTransitMinutes * 0.80 to 0.88)`
  - Practical cap: `prod <= m - 8` (reserves at least 8 min for walk/wait/transfer friction)
  - For transfer-heavy routes, start closer to 0.75-0.82 productivity
- **Drive cost ($):**
  - Weekday downtown parking case: usually 24-44 total
  - Midday/off-peak with lower parking: usually 12-24 total
- **Transit cost ($):**
  - Keep flat fare in MVP (single ride), update only after fare verification

### 11.4 Weekend transformation rules (replace blunt global deltas)

Do not apply one universal weekend delta to every suburb. Use suburb profile deltas:

- **Mahogany weekend**
  - Drive: `m -2 to -4`, `$ -8 to -14`
  - Transit: `m +4 to +8`, `prod -3 to -6`
- **Evergreen weekend** (force at least one clear drive-favoring surprise)
  - Drive: `m -3 to -5`, `$ -12 to -18`
  - Transit: `m +8 to +14`, `prod -8 to -14`
- **Saddle Ridge weekend**
  - Drive: `m -2 to -4`, `$ -8 to -12`
  - Transit: `m +2 to +6`, `prod -2 to -5`
- **Tuscany weekend**
  - Drive: `m -2 to -4`, `$ -8 to -12`
  - Transit: `m +2 to +5`, `prod -2 to -4`

### 11.5 Validation checklist (must pass before demo)

- [ ] At least 4-5 cells produce verdicts that feel non-obvious or counter-intuitive
- [ ] No impossible values (`prod > m`, negative dollars, etc.)
- [ ] Outcome split stays within target ranges in 11.1
- [ ] Anchor cells in 11.1 still hold after all tuning
- [ ] Chosen demo pair flips verdict in under 10 seconds
- [ ] If score gaps are too large everywhere, reduce assumed transit productivity and/or reduce drive parking burden in select cells

### 11.6 Demo pair recommendation after rebalance

- **Cell A (transit win):** Tuscany + AM rush + Weekday
- **Cell B (drive win):** Evergreen + Midday (or Off-peak) + Weekend

This pair demonstrates one obvious case and one surprising case with a clear narrative for judges.

## 12. The cost-vs-time trade (model honesty)

### 12.1 The asymmetry

At the current Calgary fare ($3.70 single ride) vs typical downtown drive cost ($12-$38), **transit wins on raw cost in every cell**. This is mathematically obvious and not interesting on its own.

The interesting variable is **time**. Driving is faster door-to-door in nearly every scenario (in our matrix, drive saves 5-52 minutes vs transit). What we are really asking the user is:

> "How much would you pay to save N minutes?"

If the answer is "more than the drive's premium per hour", they should drive. If less, transit wins.

### 12.2 Time premium metric (add to UI)

Add an explicit metric to the Drive card and/or verdict banner:

- `time_saved = transit.m - drive.m`  (minutes drive saves)
- `dollar_premium = drive.$ - transit.$`  (extra dollars drive costs)
- `time_premium_per_hour = (dollar_premium / time_saved) * 60`

Display as: **"Driving costs $X to save Y min - that's $Z/hour for the time you reclaim."**

This reframes the decision. The user no longer has to trust our $25/hr assumption; they can compare $Z/hour to their own hourly rate.

Reference distribution from current weekday matrix:

- Cheap to save time (drive is rational for most): Mahogany midday ~$10/hr, Saddle Ridge midday ~$26/hr
- Mid premium (depends on personal rate): Evergreen AM ~$35/hr, Mahogany AM ~$42/hr
- Expensive (transit clearly wins): Tuscany AM ~$294/hr, Tuscany PM ~$364/hr, Saddle Ridge PM is infinite (transit is also faster)

### 12.3 Implication for scoring

The current single-score verdict is fine for a snap recommendation, but it hides the trade. Two improvements, in increasing scope:

- **Minimum (recommended for MVP):** keep the verdict, but show the time premium $/hr next to it. The user sees both the answer and the assumption.
- **Stretch (was deferred as Option C):** promote the **time-value slider** from v2 to a small inline control on the verdict ("My time is worth: $20 / $30 / $50 / $75 / $100 per hour"). The verdict text updates live. This makes the demo interactive in a memorable way.

### 12.4 Updated framing for the 30-second pitch

Replace "X productive min reclaimed" with the trade made explicit:

> "Driving saves you 38 minutes - and costs $26 more. That is $42 per hour just for the time. If your hour is worth more than $42 to you, drive. Otherwise the train is the honest answer. Most calculators do not even ask."

### 12.5 Anti-pattern to avoid

Do not present the verdict as moralizing ("you should take transit"). The product's credibility depends on it sometimes saying drive. Section 11 outcome targets exist precisely so the model is not a transit advocacy tool.

## 13. Design direction

**Visual reference:** [calgary.ca](https://www.calgary.ca/home.html). The product should feel like it could plausibly belong to or be linked from the City of Calgary's own site. This buys credibility for free and sells the "honest civic tool" framing better than a generic dark-mode hackathon look.

### 13.1 What to borrow from calgary.ca

- **Light, almost-white background.** Off-white / very light grey (~`#F4F4F4` or `#FFFFFF`).
- **High-contrast black text.** Near-black body copy, no medium grey for primary content.
- **Sans-serif system stack.** Calgary uses a clean humanist sans; default to system sans (`-apple-system, Segoe UI, Helvetica, Arial`) for parity without webfonts.
- **Red as the single accent color.** Calgary brand red (close to `#C8102E`, which we already use for `ctrain`). Reuse it for the verdict banner, primary buttons, and links. Avoid additional accent colors.
- **Card layout with thin grey borders.** Subtle `1px` borders, generous padding, rounded but not chunky corners (`rounded-md` to `rounded-lg`, not `rounded-2xl`).
- **Plain, factual labels.** Calgary's tone is functional, not marketing-y. Mirror it: "Door-to-door", "Total cost", "Time premium" - no exclamations, no emoji.
- **Accessible defaults.** WCAG-style contrast, real `<label>` elements for inputs, focus rings visible.

### 13.2 What to drop from the current MVP

- Dark slate-950 background and white-on-dark text.
- Red `ctrain/15` translucent fills for the verdict block.
- Decorative uppercase "Calgary" eyebrow.
- Amber accent on the time premium row (replace with red or near-black bold).

### 13.3 Out of scope for the redesign

- Replicating calgary.ca's full nav/header/footer chrome.
- Pulling official logos or wordmarks (we are not the City; do not imply endorsement).
- Webfonts, image assets, or any external CSS beyond Tailwind CDN.

### 13.4 Acceptance check

A new viewer who has used calgary.ca should be able to say "this looks like it could live on the city's site" without being told. If it still reads as a generic SaaS dashboard, the redesign has not landed.

## 14. User habit profiles

The verdict changes meaningfully depending on **how often** and **why** someone makes the trip. A 5-day in-office worker compounding savings is a different decision than a once-a-month Flames-game attendee. The MVP picks one habit profile up front and uses it to (a) frame the savings story, and (b) lightly adjust assumptions where it actually matters.

### 14.1 The 5 habit types

| # | Profile | Trips / week | What changes in the model | Story angle |
|---|---------|---------------|----------------------------|-------------|
| 1 | **Daily 5-day commuter** | 10 (5 round trips) | Use monthly transit pass cost (~$115 / 22 work days) instead of single fare; multiply savings by 10 trips/wk | "Switching saves you $X and Y hours every month - this is the 60-min hackathon claim made real" |
| 2 | **Hybrid 3-day commuter** | 6 (Tue/Wed/Thu) | Single-ride fare still cheaper than monthly pass; multiply savings by 6 trips/wk | "Hybrid math: monthly pass is no longer the default - single fares win at 3 days/wk" |
| 3 | **Flex / off-peak shifter** | Variable, 4-8 | Default time-of-day to off-peak; drive time better, parking still pricey, transit headways longer | "You already optimize traffic - here is the cost of that choice you have not seen" |
| 4 | **Car-dependent errand chainer** | 6-10 | Add fixed $0/min "transit infeasible" penalty if user marks daycare/gym/groceries; verdict caveat: "drive (no transit option for your stops)" | "Honest answer: transit is not viable for your day. Here is what driving really costs you so you can plan accordingly." |
| 5 | **Occasional downtown visitor** | 0.5-2 / month | Use event parking rate (~$25-40 flat), Stampede/Flames surge optional; do not amortize savings across a year | "For one trip the math is small, but event parking and one-beer rules tilt this more than people expect" |

### 14.2 MVP implementation

Add one input above the existing four: **"I am a..."** dropdown with the 5 profiles above. Profile selection should:

- Set sensible defaults for time-of-day and weekday/weekend (e.g. Profile 1 -> AM rush + weekday; Profile 5 -> PM rush + weekend).
- Switch the verdict's amortization line: per-trip vs per-week vs per-month.
- For Profile 4, surface a small "transit not viable" badge on the transit card and force the verdict to **DRIVE** with an honest caveat instead of pretending transit is competitive.

### 14.3 Numeric assumptions to source

- [ ] Calgary Transit monthly adult pass price (was ~$115; verify 2026)
- [ ] Average downtown event parking rate (Stampede / Flames nights vs ordinary evening)
- [ ] Off-peak transit headway penalty (extra wait minutes vs AM rush)

### 14.4 Out of scope for v1

- Free-form habit input ("I work 4 days, 2 in office")
- Saving habit profile across sessions
- Multi-stop trip modeling beyond the binary "errands required: yes/no" for Profile 4
- Loyalty / employer transit subsidy modeling

### 14.5 Acceptance check

A judge selecting **Profile 1 (daily 5-day)** should see the verdict expressed in **monthly dollars and hours**. Switching to **Profile 5 (occasional visitor)** should re-express the same trip as a **single-trip** comparison with event parking. If switching profiles does not visibly change the framing of the savings, the feature has not landed.
