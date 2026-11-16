# Orbit iPad — Design System

Source: `docs/iPad.html`. `<style>` (iPhone base) followed by `<style id="ipad-os">` (iPadOS layer). The stylesheet is a long cascade, and later rules override earlier ones, including several earlier "themes". **Values below are the effective values after the whole cascade**, not the first definition seen. Where an earlier definition is still visible in the source but overridden, it is not listed.

---

## 1. Viewport & device

| Item | Value | Source |
|---|---|---|
| Target device | iPad Air 11″, landscape | `#ipad-os` comment, `meta screen-orientation=landscape` |
| Logical screen | **1180 × 820 pt** | `#screen` |
| Bezel | padding 22 px, radius 50 px → device frame **1224 × 864** | `.device`, `#fit` |
| Screen radius | 26 px | `#screen` |
| Status bar | 32 px high, padding 9 28 0, 14 px text; time "9:41" + date left, Wi-Fi + "100%" + battery right | `#status` |
| Home indicator | 230 × 5 px, bottom 7 px, hidden on the landing screen | `#homebar` |
| Camera dot | 7 px at top-centre | `#cam` |
| Page scaling | `scale(min(1, (innerHeight−20)/864, (innerWidth−20)/1224))`; there is **no CSS media query for width** | `fit()` |
| Orientation | Locked to landscape via `screen.orientation.lock` (try/catch) | JS |

**Responsive breakpoints:** none. The layout is fixed at 1180 × 820 and scaled as a whole. The only layout "breakpoints" are state-based:
- sidebar expanded vs rail;
- content width via `max(20px, (100% − 780px)/2)`;
- sheet/modal widths via `min()`/`max-width: calc(100% − 16px)`.
- `prefers-reduced-motion` disables ambient animation.

---

## 2. Layout geometry

### 2.1 Sidebar (floating, collapsible)
| Property | Expanded | Rail (`#app.rail`) |
|---|---|---|
| Position | left 10, top 36, bottom 12 | same |
| Width | **268** | **72** |
| Height | 772 (820 − 36 − 12) | same |
| Radius | 30 | 30 |
| Padding | 14 10 10 | same |
| Header | logo 30 + "Orbit" 21/700 + collapse button 36×36 | logo stacked above button; text hidden |
| Section label | role name, 12/600 uppercase, tracking .7, `--fg3` | hidden |
| Nav item | height 46, radius 15, gap 14, padding 0 14, icon 20, label 17/500 | icon only, centred |
| Active item | `rgba(255,255,255,.14)` fill, 1 px inner white-12% border, text white 600 | same |
| Badge | red pill min 18, 12/600 | absolute, top 2 / right 2 |
| Footer | top divider; account row: avatar 36, name 15/600, role 12 | avatar only |
| Material | glass `linear-gradient(180deg, rgba(255,255,255,.06), rgba(6,8,16,.62))`, border `1px rgba(255,255,255,.12)`, blur 30 saturate 1.5 | same |
| Motion | width transition 380 ms `cubic-bezier(.22,.8,.24,1)` | |

`#main` starts at **x = 288** (expanded, width **892**) or **x = 92** (rail, width **1088**).

### 2.2 Navigation structure
- Primary: sidebar list navigation (3 items per role).
- Secondary: **split view** on 4 of the 6 tabs.

| Split pane | `.col1` (primary) | `.col2` (secondary) |
|---|---|---|
| Width | **352** (fixed), right hairline `--line` | `#main width − 352` → **540** expanded / **736** rail |
| Background | `#000` | gradient: radial `rgba(55,129,252,.38)` top-right + radial `rgba(25,81,252,.34)` bottom-left on `#000` |
| Empty state | – | centred `.nosel`: 56 px icon, title 20/600, sub 15, side padding 48 |
| Selected row | `.selrow`: blue 14% fill (list rows) or `0 0 0 1px var(--ac)` border (cards) | |

- Tertiary: stacked pushes inside each pane (slide in from the right, 400 ms `cubic-bezier(.22,.8,.24,1)`, screen beneath moves −26% and dims to 55% brightness).
- Non-split tabs (Event Space; Live) use the whole `#main` area.

### 2.3 Content widths & columns
| Context | Rule | Resulting width |
|---|---|---|
| `.scroll` side padding | `max(20px, (100% − 780px)/2)` | col1: 352 → **312** content; col2 expanded: 540 → **500**; non-split expanded: 892 → **780**; non-split rail: 1088 → **780** |
| `.scroll` vertical padding | top 100, bottom 56 (130 when `.nb`) | |
| Top bar | padding 38 20 12, min-height 116, centred title at `top:48` (17/600), blurred fade-out gradient behind | |
| Floating CTA (`.cta`) | `left/right: max(20px, (100% − 560px)/2)`, bottom 28 → **560 wide** | |
| Inline CTA (`.cta-in`) | used by `create`, `newev`, `register`, `stages`, `shortlist`: static, margin 28 0 8 | |
| Chat bar (`.cbar`) | `left/right: max(12px, (100% − 720px)/2)`, bottom 26, height 60, radius 28 → **720 wide** | |
| FAB | right 20, bottom 36, height 50, radius 25, padding 0 22 0 18, 17/600 label | |
| Event Space body (`.bd`) | max-width **760**, centred, single column | |
| Team grid (Live, non-split) | `.grid2` → **3 columns** | |
| Standard `.grid2` | 2 columns, gap 14 | |
| Submit-project type tiles (`.segt`) | **5 columns**, gap 10, min-height 92, radius 22 | |
| Poster (`.poster`) | aspect-ratio **16 / 9** (iPad); hero poster in `details`/`aevent` uses inline 1 / .8 with a −118 px top bleed | |
| Tile (`.tile`) | min-height 112 (aspect-ratio none) | |

### 2.4 Overlays
| Overlay | Size / position | Notes |
|---|---|---|
| Scrim | full screen, `rgba(0,0,0,.58)`, fade 350 ms | |
| Sheet (standard) | **640** wide (max `calc(100% − 16px)`), centred, bottom 8, max-height `calc(100% − 56px)`, radius **38** | padding in `.sb`: 18 30 34; grab handle 38 × 5 (`--fg3`), margin 14 auto 4 |
| Sheet `tall` | 640 × **764** (`min(780px, 100% − 56px)`), `.sb` has no padding | Manage / Shortlist / Team Details / Filters / Select Event / Edit Event |
| Sheet `full` | 1180 × 820, radius 0, background `linear-gradient(180deg, rgba(25,81,252,.20), #000 28%)` | QR scanner |
| Sheet `wide` | 1120 × 760 | **defined, unused** |
| Sheet header (`.shh`) | min-height 56, padding 8 20 4, centred title 17/600 between 88 px gutters; trailing 26 px fade to black below header | buttons: `.gb` 44 or `.dnb` 36 pill |
| Alert | **330** wide, radius **30**, centred; message block padding 24 20 18, title 17, body 13; buttons height 54 with hairline dividers; pop-in 300 ms | |
| Banner | **460** wide, top 34, centred; radius 30, padding 12 14; icon tile 40 × 40 radius 12 | |
| Toast | bottom 44, centred, radius 26, padding 12 18, 15/600, glass | |
| Dropdown menu `.ddm` | radius 24, padding 8, items height 52 radius 16; opens 8 px below trigger with a "pop" animation | small variant `.ddsm` 250 px wide |
| Sheet entry | translateY(110%) → 0, 400 ms `cubic-bezier(.22,.8,.24,1)`; inter-sheet swap slides ±28 px | |

---

## 3. Colour

### 3.1 Core tokens (`:root` after all overrides)
| Token | Value | Use |
|---|---|---|
| `--bg` | `#000000` | page and screen base |
| `--s1` | `#06174a` | surface 1 |
| `--s2` | `#0b2575` | surface 2 (chips, steppers) |
| `--s3` | `#16419f` | surface 3 (tracks, off switches) |
| `--fg` | `#cbe9fd` | primary text (ice blue) |
| `--fg2` | `rgba(220,238,255,.84)` | secondary text |
| `--fg3` | `rgba(203,233,253,.60)` | tertiary text, placeholders (`.66` for `::placeholder`) |
| `--line` | `rgba(203,233,253,.18)` | hairlines |
| `--ac` | `#3781fc` | accent / links / active |
| `--ac2` | `#cbe9fd` | accent text |
| `--red` | `#FF3B30` (components often use `#ff453a`, label `#ff6b61` / `#ff8f87`) | destructive, error |
| `--em` / `--emerald` | `#10B981` (success text `#6ee7b7`; check-mark green `#34c759`) | success |
| `--n0` `--n1` `--b1` `--b2` `--ice` `--sky` | `#03195b` `#0b2cb1` `#1951fc` `#3781fc` `#cbe9fd` `#5fc4ff` | blue ramp (60/30/10 rule); `--sky` only on the landing hero and illustrations |
| `--glass` | `rgba(3,25,91,.40)` | legacy glass fill |
| `--pale`, `--vink` | `#fff` | |

Extra fixed colours: orange `#FF9F0A` (important / deadline), grey `#8E8E93` (normal priority), report palette `#00B4D8` cyan, `#F472B6` pink, `#FBBF24 → #F97316` amber (event report only), departments donut `#A78BFA #A3E635 #94A3B8 #FDE047 #E2E8F0 #CBD5E1`, announcement type colours: schedule `#3781fc`, deadline `#FF9F0A`, rules `#cbe9fd`, result `#1951fc`, logistics `#cbe9fd`, urgent `#FF453A`; event chip teal `#7DD3FC`.

### 3.2 Gradients
| Token | Value |
|---|---|
| `--g-btn` | `linear-gradient(180deg, #4d90fd 0%, #3781fc 55%, #2f72ec 100%)` |
| `--g-surf` (cards) | `linear-gradient(155deg, rgba(255,255,255,.12) 0%, rgba(255,255,255,.05) 55%, rgba(55,129,252,.10) 100%)` |
| `--btn-glow` | `0 10px 26px rgba(55,129,252,.5), inset 0 1px 0 rgba(255,255,255,.45), inset 0 0 0 1px rgba(255,255,255,.2)` |
| `--sheen` | `inset 0 1px 0 rgba(255,255,255,.36), inset 0 0 0 1px rgba(160,210,255,.16)` |
| Screen background | layered: `radial(110% 42% at 50% 0%, rgba(55,129,252,.40))` + `radial(80% 34% at 100% 30%, rgba(25,81,252,.34))` + `radial(90% 34% at 0% 100%, rgba(11,44,177,.46))` over `#000` |
| Landing background | the same three-radial stack as `.screen` (THEME v3 sets `.screen,.land` together and is the last rule); the earlier iPad-only `radial(45% 55% at 50% 38%)` glow and the sky-blue `.land` stack are both overridden. Two star `<canvas>` layers sit on top |
| Sheet background | `linear-gradient(180deg, rgba(25,81,252,.22) 0%, #000 30%), #000`, border `1px rgba(203,233,253,.22)` |
| Stat tiles | `.stat.hi` `linear-gradient(150deg,#5b9bfd,#3781fc 55%,#2a6be6)`; `.stat.em` emerald `#34d399→#10B981→#0a8f66`; `.stat.wt` white; `.stat.pk` / `.lt2` navy; event-report variants (`.rpt`) use cyan, emerald, pink, white |

### 3.3 Component fills (effective)
| Component | Fill |
|---|---|
| Primary button, FAB, selected tile, send, subcta | `--g-btn`, white text, `--btn-glow` |
| Ghost button | `rgba(118,118,128,.28)`, white text |
| Danger button | `rgba(255,69,58,.16)` text `#ff6b61` |
| Destructive "strong" | `linear-gradient(135deg,#e01b24,#ff453a)` |
| Locked CTA | `#0b2575`, text `rgba(203,233,253,.6)`, 1 px inner outline |
| Static detail chip | `#fff` fill, `#03195b` text |
| Tappable chip | `rgba(118,118,128,.28)`, white text |
| Selected / action chip | `#3781fc`, white text |
| Success chip (`.em`) | `rgba(16,185,129,.22)`, text `#6ee7b7`, 1 px `rgba(16,185,129,.5)` ring |
| Error chip (`.red`) | `rgba(255,69,58,.22)`, text `#ff8f87` |
| Poster capsule | `rgba(24,24,28,.58)` + blur 16, white text |
| Input field (`.fld`, `.sbar`, `.usg`) | `rgba(118,118,128,.24)`, no border |
| Icon tile (`.li`) | `rgba(118,118,128,.28)` |
| Segmented "on" | `#636366` |
| Switch on | `#3781fc`; knob `#fff` |
| Chat bubble (me) | `linear-gradient(165deg,#1951fc,#0b2cb1)` |
| Chat bubble (other) | `rgba(118,118,128,.28)` |
| Card (`.card`, `.tc`, `.tb`) | `--g-surf`, border `rgba(255,255,255,.16)`, backdrop blur 22 saturate 1.6 |
| Event card (`.card.ev`, `.whc`) | `--g-surf`, border `rgba(255,255,255,.16)` |
| Glass (`.glass`, tab, `.cbar`) | `linear-gradient(180deg, rgba(255,255,255,.16), rgba(255,255,255,.07))`, border `rgba(255,255,255,.18)`, blur 30 saturate 1.9 |
| Round glass button `.gb` / pill `.gp` | `rgba(118,118,128,.32)`, no border |

---

## 4. Typography

Font stack for everything: `-apple-system, BlinkMacSystemFont, "SF Pro Display", "SF Pro Text", "SF Pro", system-ui, sans-serif` (system font only; no web fonts are loaded). `font-variant-numeric: tabular-nums` on counters.

| Role | Size / weight | Class / use |
|---|---|---|
| Landing wordmark | 52 / 700, tracking −.5, line-height 1.1 | `.lname` |
| Landing tagline | 17 / 600 | `.ltag` |
| Large title | **34 / 700**, +.3 tracking | `.lt` |
| Report / access title | 28 / 700 (inline) | `rp` screens |
| Sheet title (`mg-t`) | 24 / 700, −.3 | Manage / Shortlist headers |
| Display numerals | stat 46 / 700 (−1); report hero 68 / 800 (−2); space % 30 / 800; space big 56 / 800; countdown 34 / 700; OTP digit 34 / 700; kpi 18 | `.stat .n`, `.rp-big`, `.sp-pc`, `.cdt b` |
| Heading 1 | 22 / 700 | `.h1` |
| Heading 2 / body | 17 / 600; body 17 / 400, line-height 1.35 | `.h2`, base |
| Subhead | 15–16 / 500–600 | list titles, fields |
| Secondary | 13 / 400–600 | `.sm`, `.dim` |
| Caption | 11–12 / 600 | `.xs` |
| Section label | 13 / 600, uppercase, tracking .6, `--fg2` | `.sec` |
| Chip | 13 / 600 (large 15 / 600) | `.chip`, `.chip.lg` |
| Button | 17 / 700 | `.btn`, `.lbtn` |
| Tab label | 17 / 500 (active 600) | sidebar |
| Chat text | 17 / 400, line 1.3 | `.m` |
| Alert title / body | 17 / 600; 13 | |
| Toast | 15 / 600 | |
| Table header (legacy) | 12 / 600 uppercase | `.mgt th` (table layout exists in CSS; the shipped sheets use `.nl` lists instead) |

---

## 5. Spacing, radii, shadows

### 5.1 Spacing patterns
Base grid is ad hoc but dominated by 4/6/8/10/12/14/16/18/20/24/28 px.
- Card padding `.cp` 20; list row padding 18 × 20 (compact rows 12 × 16); section label margin 32 8 14; field height ≥ 60, margin-bottom 14; field gap 12; stack gap `.col` 10.
- Screen gutters 20 (compact panes) → 56 (wide panes); sheet gutters 30.
- `gap` between cards 16; `.grid2` gap 14; chip row gap 10.

### 5.2 Corner radii
| Value | Applied to |
|---|---|
| 50 / 26 | device frame / screen |
| 48 → **38** | sheets (iPad) |
| 32 | report hero |
| 30 | alerts, banners, sidebar, sub-CTA, space cards, rgtile |
| 28 | cards `.card`, QR box |
| 26 | `.tc`, `.stat` |
| 24 | tiles, dropdown menu |
| 22 | `.pcard`, `.segt` tiles |
| 20 | fields, OTP digit, countdown tiles |
| 17 → pill | chips (height/2) |
| 27 | big buttons (`.btn`, `.lbtn` 29 on landing) |
| 25 | FAB |
| 50% | round icon buttons, avatars, switches knob |
| 16 | dropdown items, field-inset rows; 15 nav items; 14 icon tile; 12/9 segmented control |
| 10 / 6 | chart bars (top / bottom 6) |

### 5.3 Shadows
| Use | Value |
|---|---|
| `--sh-card` | `0 10px 28px rgba(0,0,0,.5), 0 2px 8px rgba(3,25,91,.45)` (card base) |
| Card / glass rim | `inset 0 1px 0 rgba(203,233,253,.18)` / `--sheen` |
| Primary button | `--btn-glow` |
| FAB | `0 10px 28px rgba(55,129,252,.45), inset 0 1px 0 rgba(255,255,255,.4), 0 2px 8px rgba(0,0,0,.6)` |
| Sheet | `0 0 0 1px rgba(0,0,0,.9), 0 -6px 34px rgba(25,81,252,.26), 0 -18px 70px rgba(0,0,0,.85), inset 0 1px 0 rgba(203,233,253,.26)` |
| Alert | `--sheen, 0 24px 60px rgba(0,0,0,.6)` |
| Sidebar | `inset 0 1px 0 rgba(255,255,255,.14), 0 10px 34px rgba(0,0,0,.55)` |
| Device bezel | `0 0 0 2px #3a3d44, 0 0 0 4px #121316, inset 0 0 0 1.5px #1b1c20, 0 50px 140px rgba(25,81,252,.14)` |
| Stat tiles | `0 12px 28px rgba(…,.32–.42)` in tile colour |
| Chat bubble (me) | `inset 0 1px 0 rgba(255,255,255,.3), 0 4px 14px rgba(11,44,177,.4)` |

---

## 6. Components

| Component | Spec |
|---|---|
| **Top bar** | height 116; left: round glass `back` (44); right: row of glass buttons (44) or pill (`.gp` height 44, radius 22, 15/600); centre title; gradient fade + blur 14 under it |
| **Button** (`.btn`) | min-height 58, padding 0 28, radius 27, 17/700, icon 22, gap 10; variants: primary, ghost, danger, strong, em, white, pink, locked, dis (opacity .32–.45), busy (spinner 22) |
| **Landing button** (`.lbtn`) | height 54 (iPad), radius 29, white-pill variant for Apple |
| **Chip** | height 34, padding 0 14, radius 17, icon 15; large 44 / radius 22 / padding 0 18 |
| **Field** (`.fld`) | min-height 60, padding 0 18, radius 20, icon 20, trailing "ok" check; textarea variant 90 px tall |
| **Search bar** (`.sbar`) | height 44, radius 22, icon 18, 17 px input, clear button 20 |
| **Segmented control** (`.usg`) | padding 3, radius 12, button height 34 / radius 9, 14/600 |
| **Switch** | 51 × 31, knob 27 |
| **Row** (`.lrow`) | padding 18 20, bottom hairline, icon tile 44 × 44 radius 14 |
| **Conversation row** (`.conv`) | min-height 76, 56 px tile, title 17/600, time 14, preview 15 (2 lines) |
| **Team list row** (`.nlr`) | 12 14 12 16 padding, hairline inset 78 px |
| **Card** | radius 28, border 1 px, blur backdrop |
| **Stat tile** | radius 26, padding 18, number 46 |
| **Tile** (`.tile`) | min-height 112, radius 24 |
| **Accordion** | header padding 16 20, chevron rotates 180°, body padding 2 20 22 |
| **Timeline** (`.tl`) | number badge 32, connector 2 px, min-height 70 (92 with chip) |
| **Ring** | SVG circle track `--s3`, progress `--ac`, animated draw |
| **Avatar** | initial on `rgba(55,129,252,.15)` circle, sizes 30–92 |
| **Poster** | image or generated SVG; capsule chips overlay; carousel with dots |
| **QR box** | white, radius 28, padding 18, QR 230 |
| **OTP** | 4 boxes 64 × 76, radius 20, gap 12 |
| **Poster carousel dots** | 6 px, active scale 1.2 |
| **Pager / progress segments (`.ndsegs`)** | height 6, radius 3, done `#3781fc`, current `#fff` |
| **Banner** | glass, radius 30, 40 px icon tile |
| **Tab (sidebar)** | see §2.1 |

---

## 7. Motion

| Item | Value |
|---|---|
| Screen push | 400 ms `cubic-bezier(.22,.8,.24,1)`; underlying screen −26% and brightness .55 |
| Pane switch | opacity 280 ms + translateY 8 / scale .99 |
| Sheet in | 400–450 ms same curve |
| Pop | scale .7 → 1, 250–300 ms |
| Press feedback | scale .97 (`.tap:active`, `.btn:active`), `.95` for small chips |
| Rings / bars | 800 ms – 1 s ease-out draw-in |
| Ambient | drifting cloud layers (home panes), twinkling star canvas (landing), chat aurora, orbiting logo, pulsing picker pill, CTA shine; all disabled under `prefers-reduced-motion` |

---

## 8. Icons

- Symbols are **inline SVG paths of Apple SF Symbols**, embedded in the script as `SF` (119 glyphs) with an alias table `SFM` (113 names) and a stroke-icon fallback `P` (64 line glyphs, only used if an alias has no glyph).
- `ic(name, size, class, filled)` outputs `<svg viewBox=… fill="currentColor">`; colour comes from `currentColor`. Default 22 px; typical sizes 14–26, sidebar nav 20, big empty-state glyphs 42–72.
- Sidebar tabs use `tic()`: outline glyph with a `.fill` variant swapped in when selected (both emitted; CSS shows one).
- 75 distinct alias names are referenced directly in the source. Full list and file mapping in `ipad-asset-inventory.md`.
- Stage icons are numbered circles: `n1 … n10` → `01.circle … 10.circle`.
