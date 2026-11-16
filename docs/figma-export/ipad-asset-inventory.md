# Orbit iPad — Asset Inventory

Source: `docs/iPad.html`, `docs/images/`, `docs/SF Symbols/`. Static analysis; nothing was exported from or written to Figma.

---

## 1. Summary

| Category | Count | Where |
|---|---|---|
| Raster images (event posters) | **4 JPG** | `docs/images/` |
| Generated poster artwork (fallback) | 4 SVG designs (v0–v3, picked by `poster id % 4`) | built in `poster()` |
| SF Symbol glyphs embedded in the script | **119** (+1 hand-drawn `play.rectangle`) | `const SF` |
| SF Symbol aliases (name used in code → glyph) | **113** (109 referenced in source) | `const SFM` |
| `.svg` files in `SF Symbols/` | **216** (the glyphs are inlined in the script, so the files are not loaded at runtime) | folder |
| Stroke-icon fallback set | 64 (+ `sidebar`, `table`) | `const P`, only used if an alias has no glyph |
| Animated illustration icons (`HICO`) | **24** | script |
| Illustration strips (`HART`) | 8 definitions (5 render on iPad) | script |
| App logo | 1 (animated SVG, procedural) | `logo(size)` |
| QR codes | procedural | qrcode-generator |
| Fonts | none (system SF stack) | – |
| External scripts | 2 (cdnjs) | `<script src>` |
| Favicon / app icon / splash | none | – |

---

## 2. Raster images

| File | Pixels | Size | Used as |
|---|---|---|---|
| `images/Nexus Hack 26.jpg` | 4000 × 2666 | 3.3 MB | Poster of event 0 *Nexus Hack 26* (event cards, details hero, admin cards) |
| `images/Nexus Hack 26 (2).jpg` | 6000 × 4000 | 4.3 MB | **Stage poster** for event 0's *Registration* stage (second slide in the poster carousel; stage info sheet) |
| `images/Green Code 25.jpg` | 4032 × 3024 | 1.3 MB | Poster of event 1 *Green Code 25* |
| `images/Byte Fest 25.jpg` | 5472 × 3648 | 3.8 MB | Poster of event 2 *Byte Fest 25* |

Notes
- Referenced as relative URLs with URL-encoding (`images/Nexus%20Hack%2026.jpg`). Rendered with `object-fit: cover`. Aspect ratios differ (3:2 and 4:3), so the 16:9 card crop and the 1:.8 hero crop both trim the images.
- Aspect ratio of the poster frame on iPad: 16 / 9 (cards), about 1 : 0.8 (details / event hero, with a 140 px bottom fade to black).
- Users can also supply a poster at runtime (file input `#fposter`, read via `FileReader` into a data URL) for events and stages. These are not repo assets.
- Image sizes are large (up to 6000 px). Figma import will be heavy; plan to downscale to ≈ 2× the display size before placing.

### 2.1 Generated poster SVG (when `poster` is a number)
Four designs, 320 × 200 viewBox, all palette-locked:
0. Network graph on `#03195b → #000` with `#3781fc` links and `#cbe9fd` nodes.
1. Blue → ice gradient grid with a black tile showing `0101` (monospace).
2. Dot field on navy/black with a `</>` glyph in ice blue and a rounded square outline.
3. Wavy ice-blue contour lines on navy/black, an ice-blue sun disc top right, and a blue leaf/tree glyph with black veins in the centre.

---

## 3. Logo & brand art

| Asset | Description |
|---|---|
| Orbit logo (`logo(z)`) | Procedural SVG: tilted orbit ellipses with a gradient core and moving dots on `animateMotion` paths. Sizes in use: **188** (iPad landing) and **30** (sidebar header); no other call sites. Static under `prefers-reduced-motion`. |
| Landing star rain (`binRain`) | Two `<canvas>`: `.rain.stars` (twinkling field) and `.rain.shots` (falling streaks) in white / ice / sky `#5fc4ff`. Faded out toward the bottom with a mask. |
| Ambient clouds (`cloudLayer`) | 3 drifting radial blobs in palette colours `#03195b #0b2cb1 #1951fc #3781fc #cbe9fd #5fc4ff`, only on `events` and `ahome` roots and the first split `.col2`. |
| Chat aurora (`chatFx`) | Moving radial glows (emerald, azure, pink, white, cyan, violet at 17–40% opacity) behind every chat-style screen. |
| Event-picker art (`.pickart`) | Orbiting rounded-square icon chips around a gradient core, rings and a conic glow; shown on the empty event-picker state. |
| Success mark (`okmark`) | Drawn check inside a circle (48-unit viewBox), pops in. |
| Progress rings (`ring`) | SVG circles. |

### 3.1 Illustration icons (`HICO`, 64 × 64, animated)
`laptop, can, wifi, glasses, trophy, clock, chat, ticket, chart, map, flag, bell, lifebuoy, plan, rocket, bank, shield, calendar, pulse, mega, inbox, headset, qr, badge` (24 icons).

Used in the *"illustration strip"* cards (`HART`), three per card:
| Screen | Title | Icons |
|---|---|---|
| `space` | Follow the flow | clock · map · flag |
| `msgs` | Stay in the loop | chat · bell · lifebuoy |
| `amsgs` | Keep teams moving | inbox · mega · headset |
| `live` | Watch it happen | wifi · chart · pulse |
| `pass` | Your event-day kit | ticket · qr · badge |
| `events`, `ahome`, `ahomeA` | *(defined but never rendered; `art()` skips the home roots)* | glasses/laptop/can · plan/rocket/trophy · bank/shield/calendar |

Palette-locked to `#03195b #0b2cb1 #1951fc #3781fc #cbe9fd #5fc4ff` and white. Animation classes: typing bars, blinking cursor, bobbing, bubbles, wifi arcs, hand sweep, twinkles, dots, bars, dashed route, shine, draw, ECG, drop, flame, scan.

---

## 4. Icons — SF Symbols

How they are used
- `ic(name, size, class, filled)` looks up `SFM[name]` (alias → SF Symbol name) and `SF[symbol]` (inline `viewBox` + path data), and draws `<svg fill="currentColor">`. Colour always inherits from CSS.
- Sidebar tab icons are emitted twice (outline + `.fill`) and the CSS shows the filled one when the tab is selected: `calendar`, `map`, `message`, `house`, `dot.radiowaves.left.and.right`. Only symbols with a `.fill` entry in `SF` get this; others show one glyph.
- Sizes in use (literal size arguments): 12, 14, 15, 16, 17, 18, 20, 22, 24, 26, 28, 32, 34, 38, 40, 42, 44, 50, 56, 58, 60, 64, 72. Sidebar nav 20, buttons 22, chips 15–16, empty-state glyphs 42–72.
- Numbered stage icons: `n1 … n10` → `01.circle … 10.circle`.
- Drawn directly in code (not from the folder): `play.rectangle` (alias `proto`).

### 4.1 Alias → SF Symbol → file
Alias names referenced as strings in the iPad script (109 of 113; a few are generic words, so treat as an upper bound). "File" is whether `docs/SF Symbols/<name>.svg` exists; "Fill" is whether `<name>.fill.svg` exists.

| Alias | SF Symbol | File | Fill |
|---|---|---|---|
| `active` | `person.2.wave.2` | yes | – |
| `adminrole` | `person.badge.shield.checkmark` | yes | – |
| `apple` | `apple.logo` | yes | – |
| `archive` | `archivebox` | yes | – |
| `arrived` | `person.crop.circle.badge.checkmark` | yes | – |
| `arrup` | `arrow.up` | yes | – |
| `bank` | `building.columns` | yes | – |
| `bell` | `bell` | yes | – |
| `bl` | `bubble.left` | yes | – |
| `bolt` | `bolt` | yes | yes |
| `bubble` | `message` | yes | yes |
| `bulb` | `lightbulb` | yes | – |
| `caldl` | `calendar.badge.clock` | yes | – |
| `calendar` | `calendar` | yes | – |
| `calplus` | `calendar.badge.plus` | yes | – |
| `cap` | `graduationcap` | yes | – |
| `chart` | `chart.bar` | yes | – |
| `chats` | `bubble.left.and.bubble.right` | yes | – |
| `chd` | `chevron.down` | yes | – |
| `check` | `checkmark` | yes | – |
| `checkc` | `checkmark.circle` | yes | yes |
| `chl` | `chevron.left` | yes | – |
| `chr` | `chevron.right` | yes | – |
| `clock` | `clock` | yes | – |
| `code` | `chevron.left.forwardslash.chevron.right` | yes | – |
| `compose` | `square.and.pencil` | yes | – |
| `copy` | `document.on.document` | yes | – |
| `dept` | `building.2` | yes | – |
| `display` | `display` | yes | – |
| `doc` | `text.document` | yes | – |
| `draft` | `document.badge.ellipsis` | yes | – |
| `eye` | `eye` | yes | – |
| `face` | `faceid` | yes | – |
| `flag` | `flag` | yes | yes |
| `flagf` | `flag.fill` | yes | – |
| `gear` | `gearshape` | yes | – |
| `gift` | `gift` | yes | – |
| `group` | `person.3` | yes | – |
| `heart` | `heart` | yes | yes |
| `heartf` | `heart.fill` | yes | – |
| `help` | `lifepreserver` | yes | – |
| `hourglass` | `hourglass` | yes | – |
| `house` | `house` | yes | yes |
| `hybrid` | `macbook.and.iphone` | yes | – |
| `idcard` | `person.text.rectangle` | yes | – |
| `image` | `photo` | yes | – |
| `invite` | `paperplane` | yes | – |
| `joined` | `person.fill.checkmark` | yes | – |
| `key` | `key` | yes | – |
| `layers` | `square.2.layers.3d` | yes | yes |
| `link` | `link` | yes | – |
| `list` | `list.bullet` | yes | – |
| `listp` | `list.bullet.rectangle.portrait` | yes | yes |
| `live` | `dot.radiowaves.left.and.right` | yes | – |
| `lock` | `lock` | yes | yes |
| `lockf` | `lock.fill` | yes | – |
| `mail` | `envelope` | yes | – |
| `map` | `map` | yes | yes |
| `megaphone` | `megaphone` | yes | – |
| `minus` | `minus` | yes | – |
| `more` | `ellipsis` | yes | – |
| `n1` | `01.circle` | yes | yes |
| `n10` | `10.circle` | yes | yes |
| `n2` | `02.circle` | yes | yes |
| `n3` | `03.circle` | yes | yes |
| `n4` | `04.circle` | yes | yes |
| `n5` | `05.circle` | yes | yes |
| `n6` | `06.circle` | yes | yes |
| `n7` | `07.circle` | yes | yes |
| `n8` | `08.circle` | yes | yes |
| `n9` | `09.circle` | yes | yes |
| `openl` | `lock.open` | yes | – |
| `orgkey` | `person.badge.key` | yes | – |
| `orgrole` | `person.2.badge.gearshape` | yes | – |
| `out` | `rectangle.portrait.and.arrow.right` | yes | – |
| `pencil` | `pencil` | yes | – |
| `people` | `person.2` | yes | – |
| `person` | `person` | yes | – |
| `phone` | `phone` | yes | – |
| `pin` | `mappin.and.ellipse` | yes | – |
| `play` | `play.circle` | yes | – |
| `plus` | `plus` | yes | – |
| `proto` | `play.rectangle` | **no (inline only)** | – |
| `publish` | `paperplane` | yes | – |
| `qr` | `qrcode` | yes | – |
| `regadd` | `person.badge.plus` | yes | – |
| `regclosed` | `person.crop.circle.badge.xmark` | yes | – |
| `repo` | `arrow.trianglehead.branch` | yes | – |
| `rupee` | `indianrupeesign.circle` | yes | – |
| `seal` | `checkmark.seal` | yes | yes |
| `search` | `magnifyingglass` | yes | – |
| `sendc` | `arrow.up.circle.fill` | yes | – |
| `shield` | `checkmark.shield` | yes | – |
| `sidebar` | `sidebar.left` | yes | – |
| `sliders` | `line.3.horizontal.decrease` | yes | – |
| `stage` | `square.2.layers.3d` | yes | yes |
| `star` | `star` | yes | yes |
| `table` | `tablecells` | yes | – |
| `tag` | `tag` | yes | – |
| `ticket` | `ticket` | yes | yes |
| `timer` | `timer` | yes | – |
| `trash` | `trash` | yes | – |
| `trophy` | `trophy` | yes | – |
| `up2` | `chevron.up.2` | yes | – |
| `upload` | `square.and.arrow.up` | yes | – |
| `urgent` | `exclamationmark.triangle` | yes | – |
| `vf` | `qrcode.viewfinder` | yes | – |
| `video` | `video` | yes | – |
| `xmark` | `xmark` | yes | – |

### 4.2 Files in `docs/SF Symbols/` not mapped by any alias
216 files = 151 base symbols + 65 `.fill` variants. 110 base symbols are mapped by `SFM`. Of the 100 numbered `NN.circle` / `NN.circle.fill` files (01–50), only `01–10` are mapped (stages are capped at 6). Non-numbered files with no alias: `calendar.badge.checkmark`, `ipad`, `iphone`, `slider.vertical.3`, `square.3.layers.3d`.

---

## 5. External resources

| Resource | URL | Purpose |
|---|---|---|
| qrcode-generator 1.4.4 | `cdnjs.cloudflare.com/ajax/libs/qrcode-generator/1.4.4/qrcode.min.js` | Passes and the scanner tray render QR codes as SVG |
| jsQR 1.4.0 | `cdnjs.cloudflare.com/ajax/libs/jsQR/1.4.0/jsQR.min.js` | Decode QR from the camera feed in `SH.qr` |

Browser APIs used as resources: `getUserMedia` (rear camera), `navigator.clipboard`, Blob download (`Orbit.ics`), `FileReader`, `<input type=file>`. There are no web fonts, no icon fonts, no CSS images and no `<link>` tags.

---

## 6. Notes for Figma export
1. Photographs are the only heavy bitmaps. Everything else is vector or procedural.
2. SF Symbols are filled outlines (not strokes). Use the `SF Symbols/*.svg` files or the inline glyphs; both match.
3. The animated logo, illustration icons, rain, clouds and aurora are decorative and animated; a static frame is appropriate for a design file.
4. The QR code on the pass is generated from team data (`qrPay(team)`); use a static placeholder QR in Figma.
5. Colours inside `HICO` and `HART` are hard-coded hex values, not tokens. They follow the palette in `ipad-design-system.md`.
