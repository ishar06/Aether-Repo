# Orbit Design System

Extracted from `index.html` (iPhone) and `iPad.html` (iPad).  
Language: Liquid Glass — translucent blue material, backdrop blur + saturation boost, specular rim highlight.  
Color rule: 60 % black base · 30 % deep blues · 10 % bright blue accent.

---

## 1. Color Tokens

### CSS Custom Properties (`:root`)

| Token | Value | Role |
|-------|-------|------|
| `--bg` | `#000` | App background |
| `--s1` | `#020b26` | Surface 1 (card/field bg) |
| `--s2` | `#04143f` | Surface 2 (chip/switch bg) |
| `--s3` | `#03195b` | Surface 3 (switch track, etc.) |
| `--n0` | `#03195b` | Navy 0 |
| `--n1` | `#0b2cb1` | Navy 1 |
| `--b1` | `#1951fc` | Blue 1 (primary accent) |
| `--b2` | `#3781fc` | Blue 2 (lighter accent) |
| `--ice` | `#cbe9fd` | Ice (light foreground on blue) |
| `--sky` | `#5fc4ff` | Sky |
| `--fg` | `#cbe9fd` | Primary foreground text |
| `--fg2` | `rgba(203,233,253,.68)` | Secondary foreground (dim) |
| `--fg3` | `rgba(203,233,253,.38)` | Tertiary foreground (placeholder) |
| `--ac` | `#3781fc` | Accent (interactive) |
| `--ac2` | `#cbe9fd` | Accent secondary |
| `--red` | `#FF3B30` | Destructive / error |
| `--em` | `#34c759` | Emerald / success |
| `--line` | `rgba(203,233,253,.10)` | Divider / border |
| `--glass` | `rgba(3,25,91,.40)` | Toast/overlay tint |

### Gradients

| Token | Value | Usage |
|-------|-------|-------|
| `--g-btn` | `linear-gradient(165deg, #1951fc 0%, #0b2cb1 48%, #03195b 100%)` | Primary button, selected chip, FAB |
| `--g-btn2` | `linear-gradient(135deg, #0b2cb1 0%, #03195b 100%)` | Alternate button |
| `--g-surf` | `linear-gradient(155deg, rgba(55,129,252,.13) 0%, rgba(2,11,38,.62) 50%, rgba(0,0,0,.52) 100%)` | Card surface |
| `--g-deep` | `linear-gradient(180deg, rgba(11,44,177,.30) 0%, #000 62%)` | Deep blue-to-black |

### Screen backgrounds

| Class | Value |
|-------|-------|
| `.screen` / `.land` | `radial-gradient(120% 40% at 50% 0%, rgba(25,81,252,.26), transparent 70%), radial-gradient(90% 30% at 100% 0%, rgba(11,44,177,.28), transparent 70%), #000` |
| `.land` (landing) | Richer 3-stop radial with sky bloom at 22 %, 38 %, 100 % |
| `.sign-bg` (auth) | 2-stop radial |

---

## 2. Shadows & Sheens

| Token | Value | Usage |
|-------|-------|-------|
| `--sh-card` | `0 10px 28px rgba(0,0,0,.5), 0 2px 8px rgba(3,25,91,.45)` | Cards |
| `--sheen` | `inset 0 1px 0 rgba(203,233,253,.32), inset 0 0 0 1px rgba(203,233,253,.07)` | Glass specular rim |
| `--btn-glow` | `0 10px 26px rgba(11,44,177,.55), inset 0 1px 0 rgba(255,255,255,.36), inset 0 0 0 1px rgba(203,233,253,.22)` | Primary button |

### Surface recipes

| Element | Background | Blur | Border |
|---------|-----------|------|--------|
| `.glass` (tab bar, toast) | `linear-gradient(180deg, rgba(55,129,252,.16), rgba(3,25,91,.46))` | `blur(30px) saturate(1.9)` | `1px solid rgba(203,233,253,.16)` |
| `.card` | `--g-surf` | `blur(22px) saturate(1.6)` | `1px solid rgba(55,129,252,.22)` |
| `.fld` (input) | `linear-gradient(180deg, rgba(11,44,177,.22), rgba(3,25,91,.34))` | none | `1px solid rgba(55,129,252,.18)` |
| `.sheet` | `linear-gradient(180deg, rgba(11,44,177,.34) 0%, #000 34%), #000` | `blur(40px) saturate(1.8)` | `1px solid rgba(55,129,252,.34)` |
| `.alert` | `linear-gradient(180deg, rgba(11,44,177,.70), rgba(3,25,91,.92))` | `blur(40px) saturate(1.8)` | `1px solid rgba(203,233,253,.18)` |
| `.ddm` (dropdown) | `linear-gradient(180deg, rgba(11,44,177,.55), rgba(3,25,91,.96))` | `blur(30px) saturate(1.8)` | `1px solid rgba(55,129,252,.24)` |
| `.scrim` | `rgba(0,0,0,.58)` | — | — |

---

## 3. Typography

**Base font stack:** `-apple-system, BlinkMacSystemFont, "SF Pro Display", "SF Pro Text", "SF Pro", system-ui, sans-serif`  
**Base size / weight / line-height:** `400 17px / 1.35`  
**Anti-aliasing:** `-webkit-font-smoothing: antialiased`

### Type scale

| Class / Element | Size | Weight | Notes |
|-----------------|------|--------|-------|
| `.lname` (landing title) | 52px | 700 | Letter-spacing `-.5px`, iPad only |
| `.lt` (large title) | 34px | 700 | Letter-spacing `.3px`, margin-bottom 26px |
| `.stat .n` (stat number) | 46px | 700 | Line-height `1`, letter-spacing `-1px`, tabular-nums |
| `.tb b` (stat block number) | 30px | 700 | Tabular-nums, letter-spacing `.5px` |
| `.h1` | 22px | 700 | — |
| `.h2` | 17px | 600 | — |
| Body / `.tt` / `.gp` / `.btn` | 17px | 400–700 | Line-height 1.35 |
| `.gp` (glass pill btn) | 15px | 600 | — |
| `.chip` (small) | 13px | 600 | — |
| `.chip.lg` (large) | 15px | 600 | — |
| `.sm` | 13px | 400 | — |
| `.xs` | 11px | 400 | — |
| `.sec` (section header) | 13px | 600 | Uppercase, letter-spacing `.6px` |
| `.tab` label (iPhone) | 10px | 600 | — |
| `#tabs .tab` label (iPad) | 17px | 500–600 | — |
| `.badge` / `.nb` | 12–13px | 600 | Tabular-nums |
| `.live` | 12px | 700 | Uppercase, letter-spacing `.8px`, red |
| Status bar | 17px | 600 | — |
| iPad status bar | 14px | 600 | — |
| iPad sidebar section label | 12px | 600 | Uppercase, letter-spacing `.7px` |

---

## 4. Corner Radii

| Element | Radius |
|---------|--------|
| iPhone device | 68px outer, 56px screen |
| iPad device | 50px outer, 26px screen |
| Dynamic Island | 20px |
| Home bar | 3px |
| Tab bar / sidebar tab (iPhone) | 33px outer, 27px per tab |
| iPad sidebar tab | 15px |
| `.card` | 28px |
| `.sheet` (iPhone) | 48px |
| `.sheet` (iPad) | 38px |
| `.alert` | 30px |
| `.btn` (primary button) | 27px |
| `.chip` (small) | 17px |
| `.chip.lg` | 22px |
| `.fld` (input field) | 20px |
| `.gb` (glass circle btn) | 50% (circle) |
| `.gp` (glass pill btn) | 22px |
| `.li` (icon tile) | 14px |
| `.tile` (grid tile) | 24px |
| `.tc` (team card) | 26px |
| `.stat` | 26px |
| `.tb` | 18px |
| `.fab` (FAB) | 25px |
| `.bn` (banner) | 30px |
| `.nb` (number badge) | 11px |
| `.badge` (tab badge) | 9px |
| `.send` (send btn) | 50% |
| `.cbar` (chat bar) | 28px |
| `.m` (message bubble) | 20px |
| `.sw` (toggle switch) | 16px |
| `.av` (avatar) | 50% |
| Poster (event image) | inside card |
| Mark / highlight | 3px |

---

## 5. Spacing

| Token / Element | Value |
|-----------------|-------|
| Screen scroll padding (iPhone) | `118px 20px 180px` (top/sides/bottom) |
| Screen scroll padding (iPad) | `100px max(20px,(100%−780px)/2) 56px` |
| Top bar padding (iPhone) | `58px 20px 14px` |
| Top bar padding (iPad) | `38px 20px 12px` |
| Tab bar: inset (iPhone) | left/right 18px, bottom 24px, height 66px, padding 6px |
| CTA button (iPhone bottom) | left/right 20px, bottom 30px |
| CTA button (iPad) | centered max 560px, bottom 28px |
| Chat bar (iPhone) | left/right 12px, bottom 28px, height 56px |
| FAB (iPhone) | right 20px, bottom 104px |
| FAB (iPad) | bottom 36px |
| `.card` margin-bottom | 16px |
| `.card` padding (`.cp`) | 20px |
| `.lrow` padding | 18px 20px |
| `.lrow` gap | 14px |
| `.sec` margin | 32px 8px 14px |
| `.fld` min-height | 60px |
| `.fld` padding | 0 18px |
| `.fld` margin-bottom | 14px |
| `.li` icon tile size | 44 × 44px |
| `.gb` glass button size | 44 × 44px |
| `.gp` glass pill height | 44px |
| `.chip` height | 34px |
| `.chip.lg` height | 44px |
| `.btn` height | 54px |
| `.sw` toggle | 51 × 31px, thumb 27px |
| iPad sidebar width | 268px (expanded), 72px (rail) |
| iPad main content left offset | 288px (expanded), 92px (rail) |
| iPad split view col1 | 352px |
| iPad sheet width | 640px |
| iPad alert width | 330px |
| iPhone alert width | 288px |
| Sheet padding (iPhone) | 18px 24px 36px |
| Sheet padding (iPad) | 18px 30px 34px |

---

## 6. Motion

| Animation | Curve | Duration |
|-----------|-------|----------|
| Screen push/pop | `cubic-bezier(.22,.8,.24,1)` | 400ms |
| Pane fade in | — | 280ms opacity + transform |
| Sheet slide up | `cubic-bezier(.22,.8,.24,1)` | 450ms |
| Tab bar hide/show | `cubic-bezier(.22,.8,.24,1)` | 350ms |
| Sidebar collapse | `cubic-bezier(.22,.8,.24,1)` | 380ms |
| Button press scale | — | 150ms |
| Toggle switch | — | 250ms |
| Progress bar fill | `cubic-bezier(.2,.8,.2,1)` | 800ms |
| Alert pop | `cubic-bezier(.2,1.2,.3,1)` | 300ms |
| Banner slide in | `cubic-bezier(.2,1,.3,1)` | 500ms |
| Spinner | linear | 700ms |

---

## 7. Component Catalogue

### Buttons

| Variant | Height | Radius | Style |
|---------|--------|--------|-------|
| Primary (`.btn`) | 54px | 27px | `--g-btn` fill + `--btn-glow` |
| Ghost (`.btn.ghost`) | 54px | 27px | Translucent glass border |
| Danger (`.btn.danger`) | 54px | 27px | `rgba(255,69,58,.16)` tint, red text |
| Strong (`.btn.strong`) | 54px | 27px | Solid `#E01B24` + shadow |
| Disabled (`.btn.dis`) | 54px | 27px | Primary at 32% opacity |
| App Store (`.btn.appbtn`) | 54px | 27px | White bg, black text |
| Glass circle (`.gb`) | 44px | 50% | Glass gradient |
| Glass pill (`.gp`) | 44px | 22px | Glass gradient |
| FAB | 50px | 25px | `--g-btn` + heavy shadow |

### Chips

| Variant | Height | Radius | Style |
|---------|--------|--------|-------|
| Default | 34px | 17px | `--s2` bg |
| Large (`.chip.lg`) | 44px | 22px | `--s2` bg |
| Selected (`.chip.sel`) | 34/44px | 17/22px | `--g-btn` |
| Accent | 34px | 17px | `rgba(55,129,252,.18)` + ice text |
| Red | 34px | 17px | `rgba(255,69,58,.16)` + red text |
| Dim | 34px | 17px | fg2 text |

### Cards

- Corner radius: 28px
- Background: `--g-surf` with blur 22px
- Border: `1px solid rgba(55,129,252,.22)`
- Shadow: `--sh-card` + `--sheen`

### Input Fields (`.fld`)

- Height: 60px min
- Radius: 20px
- Background: glass with blur
- Icon prefix, focus state border brightens to `rgba(203,233,253,.30)`
- Textarea height: 90px with `line-height: 1.35`

### Toggle (`.sw`)

- Size: 51 × 31px, track radius 16px, thumb 27px
- Off: `--s3` track
- On: `--g-btn` track, white thumb

### Stat Cards (`.stat`)

- Radius: 26px
- Large number: 46px / 700
- Variants: `.hi` (blue gradient), `.pk` (deeper blue), `.em` (success)

### Chat Bubbles

- Received: `rgba(11,44,177,.34)` bg, 20px radius
- Sent (`.m.me`): `--g-btn`, no border
- Org: `linear-gradient(180deg, rgba(55,129,252,.20), rgba(3,25,91,.55))`

---

## 8. Device Frames

### iPhone
- Outer frame: 426 × 898px, border-radius 68px
- Screen: 402 × 874px, border-radius 56px, padding 12px
- Dynamic Island: 124 × 36px, border-radius 20px, top 11px
- Status bar height: 54px
- Home indicator: 140 × 5px, bottom 8px

### iPad (Air 11", landscape)
- Outer frame: 1224 × 864px, border-radius 50px, padding 22px
- Screen: 1180 × 820px, border-radius 26px
- Camera: 7px circle, top-center
- Status bar height: 32px, font-size 14px
- Home indicator: 230 × 5px
- Aspect ratio change: poster 16:9 (vs 16:10 on iPhone)
