# heatmap-github.css


A CSS snippet that makes the [Heatmap Calendar](https://github.com/Richardsl/heatmap-calendar-obsidian) plugin for Obsidian look like GitHub's contribution graph: square cells, a dark rounded card, month labels above the week each month actually starts, and a little breathing room between months. Pure CSS — your DataviewJS block does not change.

It also fixes the common breakage where a theme's `li` rules stretch the plugin's cells into tall bars and wrap the month names letter by letter. The snippet owns every geometry property, so the grid renders the same under any theme.

> Add your screenshots here: `![Before](docs/before.png)` `![After](docs/after.png)`

## Features

- Square cells with a fixed gap and rounded corners, inside a dark rounded card that looks the same in light and dark Obsidian themes
- Month labels placed above the real first week of each month, for any year and either week-start setting — no drift, no collisions
- Optional gap at every month boundary (`--heatmap-month-gap`); set it to `0px` for GitHub's continuous grid
- All seven weekday labels and a four-digit year
- Fluid sizing: cells scale with the pane between 9 px and 15 px, then the card scrolls sideways instead of squashing
- Theme-proof: every geometry rule outranks theme `ul`/`li` rules by specificity and `!important`
- The plugin's default greens are remapped to dark-mode greens; custom palettes pass through untouched
- 18 `--heatmap-*` knobs, overridable for the whole vault or per note
- Zero JavaScript, zero changes to your DataviewJS block

## Requirements

| Component | Version | Why |
| --- | --- | --- |
| [Heatmap Calendar](https://github.com/Richardsl/heatmap-calendar-obsidian) | 0.7.0 or newer | 0.7.0 added the per-month `month-jan` … `month-dec` cell classes the labels and gaps depend on |
| [Dataview](https://github.com/blacksmithgu/obsidian-dataview) | any current release | DataviewJS blocks are how the plugin gets its data |
| Obsidian | desktop or mobile, 2022 or later builds | Uses `aspect-ratio`, `inset` and `:not()` selector lists |

## Installation

1. Download `heatmap-github.css` from this repository (open the file, **Raw**, save).
2. Copy it into your vault at `.obsidian/snippets/heatmap-github.css`. If the folder does not exist, Obsidian creates it when you open **Settings → Appearance → CSS snippets** and click the folder icon.
3. In **Settings → Appearance → CSS snippets**, click the refresh icon, then switch **heatmap-github** on.
4. Optional, for GitHub's layout: **Settings → Heatmap Calendar → First day of the week → Sunday**.

The snippet applies immediately to every heatmap in the vault, in Reading view and Live Preview. Nothing to reload.

## Usage

Any existing Heatmap Calendar block works unchanged. For a fresh one, put this in a `dataviewjs` code block in your note:

```js
const calendarData = {
  year: 2026,               // optional, defaults to the current year
  entries: [],
  // colors: leave unset to use the snippet's dark-mode greens (see Colours)
}

for (const page of dv.pages('"daily notes"').where(p => p.meditation)) {
  calendarData.entries.push({
    date: page.file.name,       // YYYY-MM-DD
    intensity: page.meditation, // the number you track
  })
}

renderHeatmapCalendar(this.container, calendarData)
```

See the plugin's README for `content`, `color`, `intensityScaleStart` and the other `calendarData` options; all of them are compatible with the snippet.

## Configuration

Every knob is a CSS custom property read with a fallback, so you override it without editing the snippet.

| Variable | Default | What it controls |
| --- | --- | --- |
| `--heatmap-cell-min` | `9px` | Smallest cell size; below this the card scrolls sideways instead of squashing |
| `--heatmap-cell-max` | `15px` | Largest cell size on wide panes |
| `--heatmap-gap` | `3px` | Gap between cells |
| `--heatmap-month-gap` | `6px` | Extra space at each month boundary; `0px` gives a continuous grid |
| `--heatmap-radius` | `2px` | Cell corner radius |
| `--heatmap-label-col` | `30px` | Width of the year / weekday column |
| `--heatmap-col-gap` | `8px` | Space between that column and the grid |
| `--heatmap-label-h` | `14px` | Height of the header row (year and month labels) |
| `--heatmap-header-gap` | `6px` | Space between the header row and the grid |
| `--heatmap-font-size` | `10px` | Label font size |
| `--heatmap-content-size` | `8px` | Size of text or emoji passed as `entry.content` |
| `--heatmap-font` | Obsidian's monospace font | Label font family |
| `--heatmap-bg` | `#1b1b1b` | Card background |
| `--heatmap-border` | `#3d3d3d` | Card border |
| `--heatmap-fg` | `#d4d4d4` | Label colour |
| `--heatmap-empty` | `#3a3a3a` | Empty-day cell colour |
| `--heatmap-today` | `#f0f0f0` | Ring drawn around today |
| `--heatmap-card-radius` | `12px` | Card corner radius |

### Vault-wide overrides

Put them in any other enabled snippet, on `body`:

```css
body {
  --heatmap-month-gap: 0px;     /* continuous grid, like github.com */
  --heatmap-cell-max: 18px;     /* bigger cells on a wide pane */
}
```

Fixed-size cells (no fluid scaling): set `--heatmap-cell-min` and `--heatmap-cell-max` to the same value.

### Per-note overrides

Set the property on the block's container before rendering; the card inherits it:

```js
this.container.style.setProperty('--heatmap-month-gap', '0px')
this.container.style.setProperty('--heatmap-empty', '#2a2a2a')
renderHeatmapCalendar(this.container, calendarData)
```

### Light card

The card is dark by design, even in a light theme. For a GitHub-light look, override the colours and swap the palette (see Colours):

```css
body.theme-light {
  --heatmap-bg: #ffffff;
  --heatmap-border: #d0d7de;
  --heatmap-fg: #1f2328;
  --heatmap-empty: #ebedf0;
  --heatmap-today: #1f2328;
}
```

## Colours

Data cells get their colour from the plugin as an inline `background-color`, chosen from the `colors` array in your DataviewJS block. CSS cannot compute intensity colours, so the snippet does the next best thing: section 8 remaps the plugin's five default greens to dark-mode greens with attribute selectors.

| Plugin default (intensity 1 → 5) | Rendered as |
| --- | --- |
| `#c6e48b` | `#0e4429` |
| `#7bc96f` | `#006d32` |
| `#49af5d` | `#26a641` |
| `#2e8840` | `#39d353` |
| `#196127` | `#7ee787` |

The mapping inverts lightness on purpose: on a dark card, higher intensity should be brighter.

**Using your own palette.** Pass `colors` in the block as usual; the remap only matches the five default hex values, so anything else passes through. Delete section 8 of the snippet if you want to be tidy. Two palettes that fit the card:

```js
// GitHub dark (4 levels)
colors: { green: ['#0e4429', '#006d32', '#26a641', '#39d353'] }

// GitHub light (4 levels) — pair with the light-card override
colors: { green: ['#9be9a8', '#40c463', '#30a14e', '#216e39'] }
```

Multi-colour heatmaps (`color: 'blue'` on individual entries) work as before.

## How it works

The plugin renders this DOM; the snippet restyles it without touching the markup:

```text
div.heatmap-calendar-graph
├── div.heatmap-calendar-year      "26"  → shown as "2026" via ::before
├── ul.heatmap-calendar-months     12 equal columns → hidden, labels regenerated
├── ul.heatmap-calendar-days       7 weekday labels
└── ul.heatmap-calendar-boxes      53 × 7 grid of li.month-xxx.isEmpty | .hasData[style] | .today
```

- **Month labels at true positions.** `li.month-jan + li.month-feb::before` matches exactly one cell per year: the first day of February. Cells are `position: static` and the grid is `position: relative`, so the label's static position is the column's own left edge; a negative `top` lifts it into the header row.
- **Month gaps.** Each month's cells get `margin-inline-start: N × gap`, pushing them right out of their fixed tracks. A week that straddles two months splits into two staggered partial columns, the same way GitHub-style month-separated graphs look. The grid is `calc(100% - 11 × gap)` wide so nothing overflows.
- **Row alignment.** The weekday list is absolutely positioned inside its own grid area, so it contributes nothing to track sizing and is forced to exactly the grid's height; its seven `1fr` rows equal the cell rows by construction.
- **Square, fluid cells.** Columns are `minmax(0, 1fr)`, cells are `width: 100%` with `aspect-ratio: 1 / 1`; the grid's `min-width` / `max-width` clamp the result between the two cell-size knobs.
- **Theme-proofing.** Every selector starts with `div.heatmap-calendar-graph`, which outranks the plugin's own rules, and geometry is `!important` so theme `li` rules (margins, line-height, min-height, bullets, word-break) cannot reach it.
- **Palette remap.** `li.hasData[style*="#c6e48b"]` matches the inline style attribute, and `!important` wins over inline declarations.

## Troubleshooting

| Symptom | Cause | Fix |
| --- | --- | --- |
| Nothing changed; cells still tall and narrow | Snippet not enabled, or the file is not in `.obsidian/snippets/` | Settings → Appearance → CSS snippets → refresh → toggle on |
| Grid looks right but month labels and month gaps are missing | Heatmap Calendar older than 0.7.0 (no `month-*` classes) | Update the plugin |
| Horizontal scrollbar on the card | Pane narrower than 53 cells × 9 px plus gaps | Lower `--heatmap-cell-min` or `--heatmap-month-gap`, widen the pane, or turn off *Readable line length* for that note |
| Data cells are still light green | You pass your own `colors`; the remap only matches the plugin defaults | Use a dark palette in the block (see Colours) |
| Card is dark in my light vault | Intended | Apply the light-card override in Configuration |
| Today ring appears in a past year | The plugin marks today by day-of-year without checking the year | Set `showCurrentDayBorder: false` in blocks that show other years |
| Weeks start on Monday but the reference starts on Sunday | Plugin setting, not CSS | Settings → Heatmap Calendar → First day of the week |
| Emoji in cells are tiny | `--heatmap-content-size` defaults to 8 px | Raise it, and `--heatmap-cell-min` with it |
| Year reads 20xx for a 19xx calendar | The four-digit year is `"20"` prepended with `::before` | Edit the `content: "20"` rule in section 3 |

## Compatibility and limitations

- Written against Heatmap Calendar 0.7.1. It relies on the `month-jan` … `month-dec`, `isEmpty`, `hasData` and `today` classes and on the `background-color` inline style; a future plugin release that renames these will need a matching update here.
- Works in Reading view and Live Preview, on Obsidian desktop and mobile. Needs a 2022-or-later browser engine (`aspect-ratio`, `inset`, `:not()` with selector lists).
- CSS snippets are vault-wide, so every heatmap gets the skin. Per-note knobs can change colours and spacing, but not opt a single note out.
- Years that need 54 week columns (a leap year starting on the last day of the week, e.g. 2028 with Sunday start) get an implicit column automatically; cells shrink by about 2 percent for that year.
- Cells are fluid between the two size knobs, so at some pane widths they land on fractional pixels and the gaps may look a hair uneven. Pin `--heatmap-cell-min` and `--heatmap-cell-max` to the same integer for pixel-exact cells.
- `content` values that are HTML elements (hover-preview links) keep working; the link fills the cell and stays clickable.
- This is a snippet, not a theme: it coexists with any theme and other snippets.

## Repository layout

```text
.
├── heatmap-github.css   # the snippet (sections 1–8, documented inline)
├── README.md
├── LICENSE              # MIT
└── docs/
    ├── before.png
    └── after.png
```

## Contributing

Issues and pull requests are welcome. When reporting a rendering problem, include your Obsidian version, platform, theme, Heatmap Calendar version and a screenshot; with a theme name the conflicting rule is usually findable in minutes. For pull requests, keep two invariants: every selector starts with `div.heatmap-calendar-graph`, and every geometry declaration stays `!important` — both are what make the snippet survive themes.

## Licence

MIT. Do what you like; a link back is appreciated.

## Credits

- [Heatmap Calendar](https://github.com/Richardsl/heatmap-calendar-obsidian) by Richard Slettevoll (Apache-2.0) does all the real work; this snippet only restyles its output.
- Colour values follow GitHub's contribution-graph palette.
