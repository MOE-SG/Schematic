# Moe's Schematic

**Schematic & Panel Documentation Workspace**

Moe's Schematic is a self-contained, browser-based schematic capture tool, installable
as a PWA (Progressive Web App). At its core it's a single `index.html` file — no
account, build step, or server required to just open and use it — with an optional
`manifest.json`, `sw.js` (service worker), and a few icon files alongside it that
enable installing it as a standalone app with offline support. Every drawing lives on
an A3 landscape sheet with a red ASME-style border, inch-accurate zone references, and
a fully editable engineering title block.

---

## Contents

- [Getting Started](#getting-started)
- [Installing as an App (PWA)](#installing-as-an-app-pwa)
- [Sheet & Title Block](#sheet--title-block)
- [Toolbar Reference](#toolbar-reference)
- [Component Library](#component-library)
- [Element Inspector](#element-inspector)
- [Bill of Materials (BOM)](#bill-of-materials-bom)
- [File Format](#file-format)
- [Controls & Shortcuts](#controls--shortcuts)
- [Browser Support](#browser-support)
- [Known Limitations](#known-limitations)
- [License](#license)

---

## Getting Started

1. Open `index.html` in any modern desktop browser (double-click it, or serve it from
   any static host — no backend needed).
2. Pick a symbol from the **Engineering Structural Library** on the left, then click on
   the sheet to place it.
3. Wire components together, annotate with text, and fill in the title block on the right.
4. Use the **Output** group to export DXF/JPG or print, and the **Parts** group to
   generate a Bill of Materials.
5. Use **File → Save** to download your work as a `.json` project file, and **File → Open**
   to resume it later.

All project data stays in your browser session and any files you explicitly save —
nothing is uploaded to a server.

---

## Installing as an App (PWA)

Moe's Schematic can be installed like a native app (its own window, an icon on your
home screen/dock, and offline support after the first visit), when served over
**HTTPS or `localhost`** — browsers block service workers and install prompts on a
plain `file://` page, so this only applies when you host the folder (e.g. GitHub
Pages, or any static web host).

1. Serve the folder containing `index.html`, `manifest.json`, `sw.js`, and the
   `icon-*.png` files together (they must stay alongside each other).
2. Open it in Chrome, Edge, or another PWA-capable browser.
3. Click the **📲 Install** button that appears in the header once the browser
   decides the app is installable (or use your browser's own install / "Add to
   Home Screen" option — the button is a shortcut to the same prompt, not the
   only way to install).
4. The installed app opens in its own window and keeps working offline after
   that first successful load, since the service worker caches the app shell.

If you just double-click `index.html` locally instead, everything still works
exactly as before — you simply won't get the install prompt or offline caching.

---

## Sheet & Title Block

- The drawing sheet is rendered at the true **A3 landscape** aspect ratio (420 mm × 297 mm,
  ≈16.5″ × 11.7″), with a red outer border and inch-accurate zone markers (numbers across
  the top/bottom, letters down the left/right) for callouts like `4-B`.
- The title block (bottom-right) mirrors a standard ASME drawing block:
  - Unspecified tolerances (three fully editable lines)
  - Sheet size designation (e.g. `A3`)
  - Document/drawing number with revision
  - Company name
  - Date, drawn-by, and sheet number (e.g. `1/1`)
- All title block fields are editable live from the **Title Block Fields** panel on the
  right sidebar and update the sheet instantly.

## Toolbar Reference

| Group | Controls | Description |
|---|---|---|
| **File** | New, Open, Save | Start a blank sheet, load a saved `.json` project, or download the current one. |
| **Output** | DXF, JPG, Print | Export a vector DXF, export a raster JPG snapshot, or print an exact, borderless A3 sheet. |
| **Parts** | BOM | Open the auto-generated Bill of Materials. |
| **Insert** | Text, Symbol | Place free-form text annotations or import a raster image as a custom symbol/footprint. |
| **Draw** | Line, Poly, Rect, Circle, Pin | Freehand drawing primitives and terminal pins, all snapped to the active grid. |
| **View** | Grid size, Fit | Change the snap grid (5/10/20 px) or reset zoom to fit the whole sheet. |

The status bar on the right of the header shows the current zoom level, active tool mode,
sheet size, and an **About** (`?`) button with a quick feature/controls summary.

## Component Library

Symbols are organized into four collapsible categories in the left sidebar. Click a
category header (or its chevron ▼/▶) to expand or collapse it — handy for keeping
the list short once you know which set you need. Each header also shows a live count
of the parts inside it.

**Electrical Components** (28 parts) — Standard Resistor, Variable Resistor,
Potentiometer, Non-Polar Capacitor, Polarized Capacitor, Ground (GND), Chassis
Ground, Power Source (VCC), Battery, Diode, Zener Diode, Schottky Diode, LED, SCR
(Thyristor), Fuse, Inductor, Crystal Oscillator, Transformer, Relay (SPST),
Transistor (NPN), Transistor (PNP), N-Channel MOSFET, Switch (SPST), Operational
Amplifier, Speaker/Buzzer, Antenna, Motor, Connector (2-Pin).

**Logic Gates** (6 parts) — AND, OR, NOT (Inverter), NAND, NOR, XOR. Standard
MIL/ANSI-style gate outlines with input/output pins ready to wire.

**Flow Chart Nodes** (3 parts) — Terminal Block, Process Engine, Decision Branch.
Useful for process/logic diagrams alongside or instead of electrical schematics.

**Rack Systems Layout** (3 parts) — 19″ Full Frame Unit, 19″ Half Frame Module,
Rackmount Computer. For enclosure/rack elevation-style layouts.

Each placed part is automatically assigned the next reference designator for its
prefix (e.g. `R1`, `R2`, `C1`, `C2` — numbering is per-prefix, so parts that share a
prefix, like the two capacitor types, never collide). Related parts intentionally
share a prefix only when that's the real-world convention (e.g. both grounds use
`GND`); otherwise each new part type gets its own prefix (`LED`, `DS`, `SCR`, `POT`,
etc.) so its numbering stays independent.

## Element Inspector

Selecting any placed element opens the **Element Inspector** in the right sidebar:

- **Designator Name** — the reference designator (or annotation text, for text objects).
- **Value / Rating** — free-text field for a component's value (e.g. `10k`, `100nF`,
  `1N4148`). This feeds directly into the Bill of Materials.
- **Show Label** — toggles visibility of the designator/value label on the sheet.
- **Pin Terminal Configuration** — for components with pins, lets you fine-tune each
  pin's position.
- **Element Stroke Color** — applies to the currently selected element, or to the next
  element you draw/place if nothing is selected.

## Bill of Materials (BOM)

Click **📋 BOM** in the Parts group to open a live-generated BOM:

- Components are grouped by **type + value**, so identical parts (same type and same
  Value/Rating) collapse into a single row with a combined quantity and a sorted list
  of reference designators (e.g. `R1, R2, R5`).
- Columns: Item #, Qty, Reference Designator(s), Description, Value/Rating, Category.
- **Export CSV** downloads the table as `<DocumentID>_BOM.csv` for use in Excel or any
  parts/ERP system.
- **Print BOM** opens a clean, separate A4 print layout with its own header (company,
  document ID, revision, date, sheet).

## File Format

**Save/Open** uses a plain JSON structure (`system_schema.json` on save) containing:

```jsonc
{
  "titleBlock": { "company": "...", "docId": "...", "rev": "...", "date": "...", "drawnBy": "...", "sheet": "...", "size": "...", "tol1": "...", "tol2": "...", "tol3": "..." },
  "components": [ /* placed parts: type, x, y, rotation, refDes, value, color, pins, ... */ ],
  "wires": [ /* net connections */ ],
  "customImages": [ /* imported raster symbols */ ],
  "annotations": [ /* free-form text */ ],
  "shapes": [ /* lines, polylines, rectangles, circles */ ]
}
```

This makes projects easy to version-control, diff, or script against outside the app.

## Controls & Shortcuts

- **Wires/Nets** — select a wire directly, or drag a marquee box around it and press
  <kbd>Backspace</kbd> to delete.
- **Move Pins** — click a component, then drag any red terminal pin dot to reposition it.
- **Marquee Box** — drag across empty canvas space to sweep-select multiple parts or
  custom-drawn lines at once.
- **Spacebar** — rotate the current selection 90°.
- **Delete Selected** — press <kbd>Delete</kbd>/<kbd>Backspace</kbd> on desktop, or tap the
  red **🗑 Delete Selected** button in the Element Inspector (this is the way to delete on
  phone/touch, where there's no keyboard). Selecting any element opens the inspector
  automatically on phone-sized screens.

## Browser Support

Any current desktop or mobile browser with Canvas2D support (Chrome, Edge, Firefox,
Safari). Printing uses `@page` sizing for exact, borderless A3 output — tested in
Chromium-based browsers; other browsers should honor the same CSS but may vary slightly
in print preview.

### Phone / touch support

- Below ~900px wide, the header toolbar collapses into a **☰** menu button; tap it to
  open a full-width dropdown with all the same File/Output/Parts/Insert/Draw/View groups.
- The component library and inspector sidebars become slide-over drawers (tap the
  **◂▸** tab on either edge) instead of squeezing the canvas, and start collapsed on
  phone-sized screens so the sheet is visible immediately.
- Single-finger touch drags draw, select, and move elements exactly like a mouse.
- **Pinch with two fingers** to zoom, and drag with two fingers to pan — anchored under
  your fingers the same way scroll-wheel zoom is anchored under the cursor on desktop.

## Known Limitations

- No electrical rule checking (ERC) or netlist export — this is a documentation/drafting
  tool, not a full EDA suite with simulation.
- Imported symbols/footprints are raster images (PNG/JPG), not editable vector symbols.
- Multi-sheet projects are not yet supported; each project file represents one A3 sheet.

## License

Licensed under the GNU General Public License v3.0 (GPL-3.0).

---

*Built for MOE's ENTERPRISE.*
