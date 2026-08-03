# Handoff: Garage Tracker — plan-first home screen

## Overview

A ground-up rework of the Garage Tracker single-page app (`daniellawrence38/garage-tracker`). The current build is four sibling tabs — Plan, Purchases, Decisions, Notes — where every record renders as the same card and nothing links to anything else.

**The change:** introduce one new object, a **Job** — a piece of work like *Line the walls in ply*. A Job owns a zone on the floor plan, its open decisions, its shopping list and its measurements. Purchases, decisions and notes stop being top-level tabs and become the anatomy of a Job.

The home screen becomes the floor plan. Clicking a shape on the plan opens a right-hand rail that answers "what does this thing need?" — its job, its unsettled decisions, its shopping list, its millimetres.

## About the design files

The files in this bundle are **design references created in HTML** — prototypes showing intended look and behaviour, not production code to copy. The existing app is a single vanilla-JS `index.html` with no build step; the task is to recreate this design *in that environment*, using its existing patterns (plain DOM, CSS custom properties, `dialog` elements, the GitHub-API persistence layer). Do not port the React/Design-Component runtime the prototype happens to be written in.

## Fidelity

**High-fidelity.** Final colours, typography, spacing and layout. Recreate pixel-for-pixel. Exact values are listed under Design tokens.

## Screens / views

### Home — plan-first workspace

Single full-viewport screen. `min-width: 1160px`, `height: 100vh`, `overflow-x: auto`, `overflow-y: hidden`. Desktop-first; phone is a later, separate layout.

Vertical stack of three bands, then a four-column body:

```
┌─ app bar ................................. 56px ─┐
├─ quick-add bar ........................... 52px ─┤
├─────┬──────────┬───────────────┬────────────────┤
│ nav │  plan    │  jobs         │  detail rail   │ flex: 1
│ 66  │  424     │  flex, min330 │  322           │
└─────┴──────────┴───────────────┴────────────────┘
```

Every vertical divider is `box-shadow: inset -1px 0 0 rgba(35,37,47,.13)`; the app bar's underline is `inset 0 -1px 0 rgba(35,37,47,.16)`.

#### 1. App bar — 56px, `#f6f7fd`
- Brand: 22×3px `#7a6cd0` bar, radius 2, then "Garage" at Inter 500 16px `#23252f`. Gap 10.
- Subtitle: "6.2 × 3.9 m · 2.4 m ceiling · rear gym + front store" — Inter 400 12.5px `#676b7c`.
- Spacer, then a search affordance: 210px min-width, padding 6/11, radius 8, `1px solid rgba(35,37,47,.18)`, magnifier icon 14px stroke `#676b7c` 1.5, label "Search jobs, items, sizes".
- Sync: 6px `#7a6cd0` dot + "Synced 4 min ago" at 12px `#5f6373`.
- Horizontal padding 18px, gap 16px.

#### 2. Quick-add bar — 52px, `#edeff8`
The single most important interaction change. One line replaces the FAB → modal → seven fields.

- Plus icon 15px stroke `#7a6cd0` 1.6.
- Typed text at Inter 400 14px `#23252f`, followed by a 1.5×17px `#7a6cd0` caret block.
- The **parse preview**: an arrow `→` at 11px `#5c6070`, then tag chips for each field the parser extracted — price, category, zone, job, status. Chips are the `.tag` component (below). Clicking a chip corrects that field.
- Right-aligned hint: "Return saves · Tab to correct a chip" at 11.5px `#5c6070`.
- Demo string: `Rubber matting 16.4 m² $420 ordered` → chips `$420` (accent), `Flooring`, `Gym` (neutral), `Job · Floor the gym` (outline), `Ordered` (neutral).

#### 3. Nav rail — 66px, `#f6f7fd`
Five items, each a 52px-wide column: 20px stroked icon over a 9.5px/500 label, gap 5, padding 9px 0, radius 8. Active item: background `#e7e5fe`, colour `#5d5294`. Inactive: colour `#676b7c`. Icons are Phosphor-style, `stroke: currentColor`, `stroke-width: 1.5`, `fill: none`.

Order: **Plan** (active) · **Jobs** · **Buy** · **Calls** · **Notes**.

"Calls" is the open-decisions inbox — decisions renamed to the thing you actually have to do about them.

#### 4. Plan column — 424px
- Header row, 14px/500 `#23252f` "Floor plan" · "to scale · 1 m grid" 11.5px `#5c6070` · right "Drag to move · R rotates" 11.5px `#676b7c`.
- **Legend** strip, `#edeff8`, radius 6, padding 7/10, gap 14. Four swatches 12×9px, radius 1, label 10.5px `#5f6373`:
  - Ordered — fill `#d8d3f8`, inset ring `#7a6cd0`
  - Owned — fill `#c3c7d8`, inset ring `#8388a0`
  - Decided, not bought — fill `#fbfbff`, inset ring `#8a8e9e`
  - Undecided — `1px dashed #9599aa`, no fill
- **Plan canvas**: `#edeff8`, radius 8, ring `0 0 0 1px #dfe2ef`, padding 8, centred SVG.
- **Not-placed tray** below: label "Not placed" 11.5px `#5c6070` + neutral tags for unplaced shapes + an outline `+ shape` tag. This replaces the current build's "SHELF — DRAG ITEMS INTO THE ROOM" strip inside the SVG.

##### The SVG plan
`viewBox="0 0 5000 7250"`, all user units are **millimetres** offset by the interior origin at (700, 300). Reuse the existing `PLAN` geometry object; only the rendering changes.

| Element | Geometry | Paint |
|---|---|---|
| Gym floor | x700 y300 w3900 h4200 | `#eaecf7` |
| Front floor | x700 y4930 w3900 h1570 | `#e2e5f2` |
| 1 m grid | pattern origin 700,300 step 1000 | stroke `#d9dcec` w10 |
| Walls | rect 700,300 3900×6200 | stroke `#a9aec2` w150, no fill |
| Pillars | 700,4500 and 4170,4500, 430×430 | fill `#a9aec2` |
| Lintel | line y4715, x1130→4170 | stroke `#5c6070` w55, dash 130 90 |
| Steel beam | x2650, y300→4500 | stroke `#8b80cb` w45, dash 220 140 |
| Window | 640,2400 120×550 | fill `#eef0f8`, stroke `#7a6cd0` w40 |
| Door | y6500, x1356→3944 | stroke `#7a6cd0` w90 |
| Door swing | two r650 arcs, outward | stroke `#8b80cb` w35, dash 90 70 |
| Dimension lines | x430 (gym, front), y170 (width) | stroke `#676b7c` w22, 120-long ticks |

Dimension text 130px `#5f6373`; annotation text 105–125px `#676b7c` / `#565094`; letter-spacing 8–14 on the all-caps runs.

**Shapes** are `<g data-id="…">` with `cursor: pointer`, containing a `rect` (radius 20, stroke-width 45) and one or two `text` labels — name at 125–150px, dimensions at 110–120px. Paint by decision state:

| State | rect fill | rect stroke | name / dim text |
|---|---|---|---|
| Undecided | none | `#9599aa`, dash 130 90 | `#4a4e5e` / `#676b7c` |
| Decided, not bought | `#fbfbff` | `#8a8e9e` | `#4a4e5e` / `#676b7c` |
| Ordered | `#d8d3f8` | `#7a6cd0` | `#453a80` / `#544e85` |
| Owned | `#c3c7d8` | `#8388a0` | `#2b2e38` / `#4a4e5e` |
| Clearance zone | `#fbfbff` | `#7468b0` | `#4a3f86` / `#544e85` |

This replaces the current five decorative tones (`accent`/`info`/`good`/`warn`/`neutral`, labelled Amber/Blue/Green/Gold/Grey). **Colour is derived from status, never chosen by the user** — drop the "Colour" select from the shape dialog.

**Selection ring**: one `rect` rendered last, `fill:none`, `stroke:#7a6cd0`, `stroke-width:70`, `rx:24`, `pointer-events:none`, positioned at the selected shape's bounds inset by −70 on each side (i.e. `x-70, y-70, w+140, h+140`).

Shape positions in the reference (mm, interior origin 700,300):

| id | x | y | w | h | state |
|---|---|---|---|---|---|
| rack | 700 | 500 | 1200 | 1200 | undecided |
| treadmill | 3800 | 600 | 800 | 1800 | undecided |
| bench | 900 | 1950 | 1200 | 350 | undecided |
| rings | 2150 | 2700 | 1000 | 1000 | clearance |
| volt | 1100 | 3850 | 1400 | 600 | owned |
| bike1 | 800 | 5050 | 700 | 400 | decided |
| bike2 | 800 | 5750 | 700 | 400 | decided |
| cupboard | 2400 | 5030 | 900 | 500 | undecided |
| store1 | 3700 | 5030 | 900 | 450 | decided |
| store2 | 3700 | 5700 | 900 | 450 | decided |

#### 5. Jobs column — flex, min-width 330px
- Header: "Jobs" 14px/500 `#23252f`, "6 open · 2 waiting on you" 11.5px `#5c6070`, right-aligned segmented control (Open / Blocked / Done) scaled to 0.86.
- List: `display:flex; flex-direction:column; gap:9px; overflow-y:auto; min-height:0`.
- **Job card**: `#fbfbff`, radius 8, padding 13/15, ring `0 0 0 1px #d2d6e4`. A job with open decisions gets ring `#bdb5f0` instead.
  - Row 1 (`display:flex; align-items:baseline; gap:9px`): title Inter 500 14.5px `#23252f`; a `.tag` for status; spacer; a right-aligned meta value in `ui-monospace` 12px — the job's key measurement or its zone. **The meta and the tag must be `white-space: nowrap`; only the title may wrap.**
  - Optional body line: 12.5px/1.55 `#5f6373`.
  - Optional **decision chips**: `1px dashed #8b80cb`, radius 6, padding 5/9, 11.5px `#5d5294`. A solid `1px #d2d6e4` chip in `#5f6373` is used for a non-decision fact ("3 items · price unknown").
  - Optional **progress bar**: 5px tall, radius 3, track `#dcdfee`; ordered portion `#7a6cd0`, in-hand portion `#c8ccdc`.
  - Done job: background `#edeff8`, ring `#dfe2ef`, `opacity:.7`, title struck through in `#5f6373`.

Jobs in the reference: *Lifting corner* (blocked, 2 open decisions), *Line the walls in ply* (ordered, $659), *Floor the gym* (decided, 16.4 m²), *Replace the garage door* (decided, 2587 × 2152 mm), *Front-area storage* (1 open decision), *Paint the ceiling* (done).

#### 6. Detail rail — 322px, `#edeff8`
Contents, top to bottom, gap 14, padding 16/18:
1. Kicker "SELECTED ON PLAN" — 9.5px/600 `ui-monospace`, letter-spacing .12em, uppercase, `#5d5294`; right "Esc to clear" 11px `#5c6070`.
2. Name — Inter 500 19px `#23252f`. Dimensions — `ui-monospace` 12.5px `#544e85`, margin-top 7.
3. Tag row: zone (neutral) + state (outline).
4. Note — 12.5px/1.6 `#5f6373`.
5. Fading rule: `height:1px; background: linear-gradient(to right, transparent, rgba(35,37,47,.18) 20%, rgba(35,37,47,.18) 80%, transparent)`.
6. **Part of the job** — section kicker 9.5px `#676b7c`; then a row: 5×26px `#7a6cd0` bar, job name 13.5px/500, spacer, `›`. Background `#fbfbff`, ring `#d2d6e4`, radius 7, padding 9/11.
7. **To settle** — one dashed `#8b80cb` row per open decision: text 13px `#4a3f86`, right-aligned state 10.5px `#565094`. Empty state: a single row reading "Nothing open / settled".
8. **Shopping list** — `#fbfbff` rows, radius 7: item 13px `#4a4e5e`, spacer, status 11px `#676b7c`, price `ui-monospace` 12px `#5d5294`.
9. Footer buttons, pushed down by a flex spacer: `.btn.btn-primary` "Add to this" (flex:1) + `.btn.btn-secondary` "Measure".

## Interactions & behaviour

- **Select a shape** — click any `<g data-id>` on the plan. Sets `selected`, moves the selection ring, repopulates the whole rail. Escape clears. This is the only interaction implemented in the prototype.
- **Select a job** — clicking a job card should select its zone/shape on the plan (the inverse link). Not built in the prototype; build it.
- **Quick add** — parse the input on every keystroke; render extracted fields as chips. Return commits. Tab focuses the first chip; chips are editable in place. Parse rules to implement: `$n` → price; a known category word → category; a known zone word → zone; `for the <job>` / `→ <job>` → job; a status word (planned/researching/ordered/installed) → status; the remainder → item name. Unmatched fields fall back to defaults, never block the save.
- **Drag a shape** — keep the existing pointer-drag and 90° rotate (`R`); snap to the 1 m grid is desirable. Shapes may not leave the interior rect; a shape with no position lives in the "Not placed" tray rather than an in-SVG shelf.
- **Hover states** — job card ring lifts to `#b2b6ca`; plan shape stroke-width 45 → 60; nav item background `rgba(35,37,47,.05)`.
- **Focus** — `:focus-visible { outline: 2px solid #6a5db3; outline-offset: 2px }` on every interactive element. Never leave the browser default.
- **Transitions** — 120ms on `border-color`, `background`, `color`, gated behind `@media (prefers-reduced-motion: no-preference)`, as the current build already does.

## State management

Extend the existing `data.json` shape. New and changed fields:

```js
jobs: [{
  id, title,
  zone,                  // "Gym" | "Front area" | "General"
  status,                // "deciding" | "ready" | "ordered" | "done"
  shapeIds: [],          // links to shapes on the plan
  note,                  // one-line summary shown on the card
  metric,                // free-text key measurement, e.g. "2587 × 2152 mm"
  date
}]
```

- `purchases[].jobId` — new, nullable.
- `decisions[].jobId` — new, nullable.
- `notes[].jobId` — new, nullable.
- `shapes[].tone` — **remove**. Derive paint from the linked purchases' statuses: any `ordered` → ordered; all `installed` → owned; any linked decision with status `deciding` → undecided; otherwise decided.
- `shapes[].links` — keep; it already links shapes to purchases/decisions and is what makes the rail work.

Runtime state (not persisted): `selectedShapeId`, `jobFilter` (`open` | `blocked` | `done`), `quickAddDraft`.

Migration: on load, if `jobs` is absent, synthesise one job per existing decision title and attach purchases by zone + category. Keep `version` bumping and the existing normalise-on-load pattern.

## Design tokens

Derived from the Nocturne design system, light ground.

**Colour**

| Role | Value |
|---|---|
| Page ground | `#eef0f8` |
| App ground / bar | `#f6f7fd` |
| Surface (cards, rows) | `#fbfbff` |
| Sunken panel (rail, legend, plan canvas) | `#edeff8` |
| Accent (fills, rules, selection ring) | `#7a6cd0` |
| Accent (text, borders, buttons) | `#6a5db3` |
| Accent (small text) | `#5d5294` |
| Accent (strong text) | `#4a3f86` |
| Accent tint (panel, nav active) | `#e7e5fe` |
| Accent tint (ordered fill) | `#d8d3f8` |
| Text primary | `#23252f` |
| Text secondary | `#4a4e5e` |
| Text muted | `#5f6373` |
| Text faint | `#5c6070` / `#676b7c` |
| Hairline / border | `#d2d6e4` |
| Hairline (sunken) | `#dfe2ef` |
| Track / fill grey | `#dcdfee` / `#c8ccdc` / `#c3c7d8` |
| Wall grey | `#a9aec2` |
| Grid | `#d9dcec` |
| Divider (alpha) | `rgba(35,37,47,.13)` — .16 on the app bar, .18 on rules |

Every text colour above clears WCAG AA (4.5:1) on its own backdrop. Accent text below 18px must use `#5d5294` or darker — never `#7a6cd0`.

**Type** — Inter throughout (400 / 500 / 600). Sizes in use: 19, 16, 14.5, 14, 13.5, 13, 12.5, 12, 11.5, 11, 10.5, 9.5. Numeric values and measurements use `ui-monospace, Menlo, monospace`. Section kickers: 9.5px/600 monospace, `letter-spacing: .12em`, uppercase.

**Radius** — 4 (chips) · 6 (legend, decision chips) · 7 (rail rows) · 8 (cards, panels, buttons) · 999 (dots).

**Spacing** — 4 / 5 / 6 / 7 / 8 / 9 / 10 / 11 / 12 / 13 / 14 / 16 / 18 px. Gaps do the work; avoid per-element margins.

**Elevation** — no drop shadows in the app chrome. Elevation is a hairline: `0 0 0 1px #d2d6e4`. The only real shadow is on modals: `0 0 0 1px #b2b6ca, 0 16px 40px rgba(35,37,47,.16)`.

**Components reused from Nocturne** — `.btn` (`.btn-primary` outlined accent, `.btn-secondary`, `.btn-ghost`), `.tag` (`.tag-accent` `#ded9fb`/`#453a80`, `.tag-neutral` `#dfe2ef`/`#3b3e4a`, `.tag-outline` `#6a5db3` border/`#5d5294` text; all `white-space: nowrap`), `.seg` + `.seg-opt`, `.input`, `.field`, `.table`.

## Assets

None. All iconography is inline SVG drawn on `currentColor` (Phosphor-style, 1.5 stroke, 20px in the nav, 14–15px inline). No images, no icon font, no external requests other than the Inter webfont.

## Files

| File | What it is |
|---|---|
| `garage-home-standalone.html` | **The design.** Self-contained, opens offline in a browser. This is the reference to match. |
| `Garage Home.dc.html` | Source of the above, inside the design project. |
| `Garage Tracker Redesign.dc.html` | The full options canvas: the written brief, plus three alternative homes (1a plan-first — chosen; 1b job board; 1c annotated working drawing) and three add/edit treatments (one-line parse, inline row, detail sheet). Useful for the reasoning and for the rejected directions. |

## What was deliberately dropped from the current build

- The four-tab bar. Plan/Jobs/Buy/Calls/Notes is a nav rail, and Purchases/Decisions/Notes are no longer peers of the plan.
- The three-up stat row. It decorated; the jobs list carries the same information in context.
- The five decorative shape tones and the "Colour" field in the shape dialog.
- The FAB → modal add flow as the *primary* path. The modal stays, as the detail sheet, for items you're about to spend real money on.
- "SHELF — DRAG ITEMS INTO THE ROOM" as SVG text; replaced by the HTML "Not placed" tray.
