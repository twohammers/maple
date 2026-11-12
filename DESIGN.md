# DESIGN.md — Maple - Scale Finder

Header: "Maple · Scale Finder" wordmark left; theme switch right as two icon buttons (sun = light, moon = dark), the active one filled brass.

A tool for picking an instrument, a tuning, a root and a scale, then reading the scale off a fretboard.

**Direction: "Rosewood & Brass."** The page looks like the instrument it explains: a dark rosewood neck with brass-colored frets and inlays, plus a light "maple" mode. Calm, focused, a little warm. It should feel like a practice tool, not a marketing site. The fretboard is the hero; everything else is quiet.

Why: the only thing a learner stares at is the neck. Borrowing the neck's own palette makes the chart feel familiar at a glance, and a warm dark default is easier on the eyes during long practice sessions in low light. Brass gives one strong action color without reaching for the purple/blue gradient every generated site uses.

> Not based on a comparison of real reference sites (no reference library was connected). Built from the product's needs and standard design rules.

---

## Color

Dark is the default. Every text pair below is checked against WCAG (ratio in brackets).

| Token | Dark | Light | Role |
|---|---|---|---|
| `--bg` | `#16120F` | `#FAF7F2` | Page background |
| `--surface` | `#201A16` | `#F2ECE3` | Panels, selector cards |
| `--border` | `#3A322B` | `#DDD3C6` | Dividers, input borders |
| `--text` | `#F2EBE3` [15.8] | `#1F1A16` [16.1] | Body text, headings |
| `--text-muted` | `#B3A699` [7.8] | `#5E5349` [7.0] | Labels, fret numbers, helper text |
| `--primary` | `#E0A43A` [8.5] | `#8F5D0E` [5.3] | Buttons, links, selected chips, active controls |
| `--on-primary` | `#16120F` [8.5] | `#FFFFFF` [5.6] | Text on primary |
| `--board` | `#3B2A20` | `#EFE7DC` | Fretboard wood |
| `--on-board` | `#EFE7DC` [11.1] | `#16120F` [15.2] | Note labels on the board |
| `--fret` | `#C9A15A` | `#A88B5C` | Fret wires, inlay dots (decorative) |
| `--root` | `#E8705F` [6.1] | `#B5382B` [5.5] | Root note — the one accent |
| `--success` | `#4FB37A` [7.2] | `#2F7A4E` [4.9] | Practice mode "correct" |
| `--warning` | `#E58A3A` [7.1] | `#A3530F` [5.2] | Non-standard tuning notice |
| `--error` | = `--root` | = `--root` | Invalid input (rare; shares the accent) |

Rules
- Brass (`--primary`) means "you can click this" or "this is selected." It never marks a note.
- Coral (`--root`) is reserved for the root note on the fretboard. Nothing else in the UI uses it.
- Never encode meaning by color alone: the root note is also a different **shape** (rounded square vs. circle) and labeled.

## Note markers (the core visual)

| Marker | Shape | Fill | Label |
|---|---|---|---|
| Root | Rounded square, 28px | `--root` | Note name or `R` |
| Scale tone | Circle, 26px | `--text` (dark) / `--text` (light) on board | Note name or degree (2, ♭3, 4…) |
| Open-string tone | Ring (outline only) left of the nut | — | Note name |
| Out of scale | Not drawn | — | — |

Label toggle (one control, three states): **Note names · Degrees · Blank** (blank = quiz yourself). Optional "color by degree" view is a later feature; it would need its own validated palette.

## Typography

- **IBM Plex Sans** — all UI and body text. Weights 400, 500, 600.
- **IBM Plex Mono** — note names, tunings (`E A D G B E`), fret numbers, intervals. Monospace keeps tunings and degree labels aligned in columns.

| Step | Size / line height | Use |
|---|---|---|
| `xs` | 12 / 16 | Fret numbers |
| `sm` | 14 / 20 | Labels, helper text |
| `base` | 16 / 24 | Body, inputs |
| `lg` | 18 / 26 | Selector headings |
| `xl` | 22 / 30 | Scale name under the board |
| `2xl` | 28 / 34 | Page title |
| `3xl` | 36 / 42 | Current selection banner ("A Dorian") |

Body text never below 16px; prose capped at ~68ch.

## Spacing & layout

- 4px base scale: `4, 8, 12, 16, 24, 32, 48, 64`.
- Content max width 1200px; the fretboard may go full-bleed and scroll horizontally inside its own container.
- Desktop: controls in one row above the board (Instrument → Tuning → Root → Scale). Board fills the width.
- Mobile (<720px): **the fretboard turns vertical** (nut at top, strings left to right). Controls stack above as compact selects. This is the one non-negotiable layout decision — an 8-string, 24-fret board does not fit horizontally on a phone.

## Shape & depth

- Radius: `4` inputs/chips, `8` panels/cards, `full` note circles.
- One shadow only, for the open dropdown/menu: `0 8px 24px rgb(0 0 0 / .28)` (dark), `/ .12` (light). The board and panels are flat.

## Components

**Fretboard**
- 4–8 strings; board height grows with string count; string thickness steps up toward the low strings (1px → 3px).
- Equal fret spacing (not true-to-scale) so later frets stay readable. 0–24 frets; default view 0–12 with a "show full neck" toggle.
- Inlays at 3, 5, 7, 9, 12 (double), 15, 17, 19, 21, 24. Ukulele: 5, 7, 10, 12.
- Fret numbers below the board in `--text-muted`, mono.

**Chart view toggle** (large segmented control, first item in the toolbar)
- **Whole neck**: every scale note, frets 0–12 or 0–24.
- **Box positions**: one playable shape at a time, with finger numbers. Position chips 1…n (← → keys step through them), a brass band shading the box's frets, and the rest of the scale shown as faint dots for context.
- Box rules: 7-note scales use three-notes-per-string; pentatonics use two-notes-per-string; blues adds the ♭5 inside the pentatonic box; ukulele (and any shape spanning more than 6 frets) falls back to one-finger-per-fret windows.
- Labels switch to **Fingers · Notes · Degrees · Blank** in box view (Fingers is the default there).

**Selectors**
- Instrument: segmented control — Bass · 6 · 7 · 8 · Uke.
- Tuning: select with presets per instrument (Standard, Drop D, D Standard, Drop A for 7, F♯ standard for 8, etc.) plus **Custom** (one note picker per string). Non-standard tuning shows a `--warning` note only if the board can't be computed.
- Root: 12 chips in a single row (C C♯/D♭ D … B), with a ♯/♭ display toggle.
- Scale: grouped select — *Modes* (Ionian, Dorian, Phrygian, Lydian, Mixolydian, Aeolian, Locrian), *Pentatonic & Blues*, *Harmonic & Melodic minor*.

**Buttons**: primary (brass fill), secondary (border only), quiet (text only). Hover darkens fill 8%; focus is a 2px `--primary` outline offset 2px; disabled is 40% opacity with no hover.
**Inputs**: `--surface` fill, `--border` border, focus ring as buttons.
**Nav**: logo/name left, light/dark toggle right. No marketing nav.
**Footer**: one line — about, keyboard shortcuts.

## Icons & imagery

- **Lucide** icons, 1.5px stroke, 20px. Only where they replace words (theme toggle, settings, play).
- No photos or stock illustrations. The fretboard *is* the imagery.

## Avoid

Gradients, glassmorphism, gradient text, emoji as icons, centered hero sections, three identical feature cards. This is a tool, not a landing page.

## CSS tokens

```css
:root {
  --bg:#FAF7F2; --surface:#F2ECE3; --border:#DDD3C6;
  --text:#1F1A16; --text-muted:#5E5349;
  --primary:#8F5D0E; --on-primary:#FFFFFF;
  --board:#EFE7DC; --on-board:#16120F; --fret:#A88B5C;
  --root:#B5382B; --success:#2F7A4E; --warning:#A3530F;
  --font-ui:"IBM Plex Sans",system-ui,sans-serif;
  --font-mono:"IBM Plex Mono",ui-monospace,monospace;
  --r-sm:4px; --r-md:8px;
}
@media (prefers-color-scheme: dark) {
  :root:not([data-theme="light"]) {
    --bg:#16120F; --surface:#201A16; --border:#3A322B;
    --text:#F2EBE3; --text-muted:#B3A699;
    --primary:#E0A43A; --on-primary:#16120F;
    --board:#3B2A20; --on-board:#EFE7DC; --fret:#C9A15A;
    --root:#E8705F; --success:#4FB37A; --warning:#E58A3A;
  }
}
:root[data-theme="dark"] { /* same values as the dark block above */ }
```
