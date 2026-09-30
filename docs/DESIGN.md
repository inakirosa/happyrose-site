# Happy Rose LLC website: design

All tokens are CSS variables at the top of `assets/css/site.css`. The site is **light only for now** (`color-scheme: light`, with no dark overrides), per the Potato Tracker website kit. The dark values below are kept for when dark mode comes back.

## Company brand

**Logo:** a smiling rose (`assets/img/logo.svg`, also `favicon.svg`), drawn in the same flat, ink-outlined style as the app art.

| Token | Light (in use) | Dark (not in use) | Use |
|---|---|---|---|
| `--bg` | `#FFF9F6` | `#1B1317` | Page |
| `--surface` | `#FFFFFF` | `#271C22` | Cards, panels |
| `--tint` | `#FCE8EC` | `#3A2530` | Pills, notes |
| `--text` | `#3A2230` | `#F6E8EC` | Text |
| `--muted` | `#76596A` | `#C9ADB8` | Secondary text |
| `--accent` | `#B33A5A` | `#F28CA6` | Links, buttons, kickers |
| `--line` | `#F0DDE0` | `#3D2C34` | Borders |

Logo colours: petals `#E8637F`, cheeks `#F7A7B8`, leaf `#6DBA5A`, stem `#3B7A34`, outline `#3A2230`.

**Type:** Nunito throughout, with ExtraBold 800 for headings. It's self-hosted.

**Shape:** 18px corners on small cards, 26px on panels, pill-shaped buttons, and 1.5px borders.

## App pages

An app's pages add a body class that overrides the tokens. `.app-potato` uses Potato Tracker's `docs/DESIGN.md` values:
- **Colours:** page `#FAF7F0`, stage `#F6E7BD`, Spud Dark `#8B5E34`, Ink `#3B2A1E`, the kit README's Light column only.
- **Type:** Fredoka SemiBold for headings.

The shared header and footer stay the same on every page.

## Layout

Pages are built for phones first:
- **Gutter:** 16px.
- **Width:** content maxes out at 68rem, and long text at 44rem.
- **Grids:** `auto-fit` with `minmax(min(100%, …))`, so nothing scrolls sideways.
