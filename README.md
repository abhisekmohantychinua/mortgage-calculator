# Mortgage Calculator (HTML/CSS/JS)

A single‑file, responsive mortgage calculator that mirrors the UX of mortgagefinder.ae while matching the calculation model used on hateemmortgage.com. It features two‑way synced sliders and inputs, a filled slider track, and an international phone input (UAE by default).

## Features

- Clean, responsive UI with namespaced styles and IDs:
  - Classes prefixed with `.mgc-` and IDs with `#mgc-` to avoid collisions
  - Two‑column layout on desktop; stacks to one column on tablets/mobiles
  - Sticky results panel on desktop; disabled on small screens to prevent overlap
- Calculator logic
  - Centralized LIMITS config for min/max/steps
  - Inputs: Finance Amount, Profit Rate, Finance Period
  - Two‑way sync between number inputs and range sliders
  - Range sliders render a filled track based on current value
  - Outputs: Monthly Payment and Total Profit Paid
- International phone input
  - Uses intl-tel-input v25.x, default country UAE, separate dial code
  - Preferred countries tuned for GCC and common markets
  - Graceful init with `loadUtils` and retry on load
- Submission behavior
  - On submit, shows an alert and logs labeled values to the console (no network requests)

## Quick start

This project is static—just open `index.html` in a modern browser. No build tools required.

Optional: serve locally to avoid any browser cross‑origin restrictions with file URLs.

```powershell
# Example: serve current folder on http://localhost:8000
python -m http.server 8000
```

Then visit <http://localhost:8000> and open `index.html`.

## Project structure

```text
.
├─ index.html   # All HTML, CSS, and JS (no build step needed)
└─ README.md    # This documentation
```

## How it works

- Centralized configuration lives in `index.html` inside the `LIMITS` object:
  - finance: { min: 100000, max: 5000000, step: 1000 }
  - period: { min: 5, max: 40, step: 1 }
  - rate: { min: 1, max: 15, step: 0.01 }
- Inputs and sliders are tied together with clamping and step rounding:
  - Editing the text field updates the slider and vice versa
  - Sliders repaint a filled gradient track to visualize the value
- Monthly payment formula:
  - Amortized payment with principal P, monthly rate r, and term n months
  - Total profit paid = (monthly payment × number of months) − principal

## UI/UX and responsiveness

- Desktop: two columns, results panel is sticky with `top: 32px`
- ≤ 1024px: layout stacks to a single column and sticky is disabled
- Global CSS guards to prevent horizontal scrolling on mobile:
  - `box-sizing: border-box` globally
  - `html, body { max-width: 100%; overflow-x: hidden; }`
  - Inputs/labels are allowed to wrap; long labels break lines

## What changed vs the previous Dubai-specific version

- Removed residency tiers, down payment, and purchase price inputs
- Removed the upfront costs modal and fee calculations (DLD, trustee, etc.)
- Simplified to match hateemmortgage.com: Finance Amount (100k–5M), Profit Rate (1–15%), Finance Period (5–40y)

## International phone input

- CDN: `intl-tel-input@25.12.3`
- Config highlights: `initialCountry: "ae"`, `separateDialCode: true`, `preferredCountries: ["ae","sa","om","qa","kw","bh","eg","gb","us","in"]`
- `loadUtils` dynamically imports the utils script from the same CDN

If the CDN is blocked, the input still works as a regular `<input type="tel">`, just without formatting/flags.

## Customization

- Change limits, steps, and residency tiers by editing the `LIMITS` object
- Adjust fees inside `calcFees()` to match policy updates
- Tweak breakpoints or spacing inside the CSS at the top of `index.html`
- All selectors are namespaced with `.mgc-` and `#mgc-` for safe embedding

## Troubleshooting

- Horizontal scroll on small screens: fixed via global border‑box and overflow guards. If you embed this into another page, ensure no parent container imposes widths > 100vw or negative margins.
- Phone field not showing flags: check CDN connectivity. The control initializes on `DOMContentLoaded` and `load` events.
- Sliders not showing filled track: ensure JS is executing (no console errors). The filled gradient is painted in `setRangeFill()`.

## Privacy

The form does not send data to any server. It only logs values to the console and shows an alert for demo purposes. Integrate your own submit handler/XHR/fetch as needed.

## Browser support

Tested on recent Chrome, Edge, and Firefox. Safari iOS works with the responsive layout and phone input; older browsers may need polyfills.

## Roadmap / Next steps

- Optional: persist state in query params or localStorage
- Add validation and inline error states for contact fields
- Provide dark mode and improved contrast settings
- Export/share calculation summary (PDF/print)
