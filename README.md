# Cursor Calgary April Meetup

Prompt: "Build a tool that saves someone 60 minutes a week"

# Drivesplainer

Drivesplainer is a Calgary commute calculator that explains whether a suburb-to-downtown commuter should drive or take transit. It does not only compare trip duration; it also shows parking/gas/wear cost, productive minutes, and the implicit price of buying back time by driving.

## MVP

- Single-file `index.html`
- Tailwind via CDN
- Vanilla JavaScript
- Hardcoded commute matrix for four Calgary suburbs
- Live commuter habit profiles
- Live time-value tolerance presets and custom input
- City of Calgary-inspired visual direction, without using official branding

## Run

Open `index.html` in a browser. No install, build step, API key, or server is required.

## Demo Flow

1. Pick a commuter habit type.
2. Choose suburb, time of day, and weekday/weekend.
3. Adjust "My time is worth" to show how the verdict changes.
4. Use the verdict explanation to show the trade: driving may save time, but the app explains what that time costs per hour.

## Notes

All commute data is hardcoded for demo purposes. Verify Calgary Transit fares, monthly pass cost, downtown parking rates, and travel-time estimates before presenting as factual.
