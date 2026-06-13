# AGENTS.md

## Project type

Static HTML/SVG visualization files for ESP32 breadboard circuits. **No build system, no dependencies, no package manager.**

## Entry point

- `index.html` is the navigation hub linking all HTML demos and SVG schematics
- Open it first: `open index.html`

## How to work with files

- Each `.html` file is **standalone** – open directly in a browser, no server needed
- `index.html` loads SVGs via `fetch()` and renders them inline as thumbnails + modals
- No compilation, bundling, or preprocessing required

## Language

Content and UI text are in **German** (e.g., "Schaltplan" = circuit diagram, "Steckbrett" = breadboard).

## What this is NOT

- Not an embedded firmware project (no ESP32 code to flash)
- Not a Node.js/npm project (no package.json needed)
- Not a web app requiring a dev server or build step

## Typical workflow

To preview changes:
```bash
open index.html  # macOS
# or open the file in any browser
```

No test suite, linter, or formatter configured. Verify changes by visual inspection in browser.
