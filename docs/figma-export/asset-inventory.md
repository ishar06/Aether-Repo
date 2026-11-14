# Orbit Asset Inventory

Extracted from `docs/images/` and `docs/SF Symbols/`.

---

## 1. Event Poster Images

Located in `docs/images/`. Used as hackathon banner images in event cards, detail screens, and pass screen.

| File | Aspect used |
|------|-------------|
| `Byte Fest 25.jpg` | 16:10 (iPhone) · 16:9 (iPad) |
| `Green Code 25.jpg` | 16:10 (iPhone) · 16:9 (iPad) |
| `Nexus Hack 26.jpg` | 16:10 (iPhone) · 16:9 (iPad) |
| `Nexus Hack 26 (2).jpg` | 16:10 (iPhone) · 16:9 (iPad) |

**Usage contexts:**
- Event list cards (`.poster`) with chip overlays (bottom-left)
- Event detail hero
- My Pass screen header
- Stage builder poster picker

---

## 2. SF Symbols (SVG)

Located in `docs/SF Symbols/`. All files are SVG. Used via `ic()` helper function in JS (inlined as `<svg>` elements). Rendered at various sizes (14–64px) and tinted via CSS `color`.

### Numbered Circle Sets (stage indicators)

50 numbered circles, each in outline + filled variants (100 files total):

`01.circle.svg` through `50.circle.svg`  
`01.circle.fill.svg` through `50.circle.fill.svg`

**Usage:** Stage progress indicators in timeline (`.tl`), stage builder, live scoring grid.

---

### Named Symbols

#### Communication & Social
| File | Usage in Orbit |
|------|---------------|
| `bubble.left.svg` | Messages tab icon |
| `bubble.left.and.bubble.right.svg` | Organizer chat, help desk |
| `message.svg` | Message preview rows |
| `message.fill.svg` | Active message state |
| `megaphone.svg` | Announcements |
| `envelope.svg` | Email field icon |
| `paperplane.svg` | Send action |
| `speaker.wave.2.fill.svg` | Broadcast action |

#### Navigation & UI Chrome
| File | Usage in Orbit |
|------|---------------|
| `chevron.right.svg` | List row disclosure indicator |
| `chevron.left.svg` | Back button (`chl` alias) |
| `chevron.down.svg` | Dropdown arrow |
| `chevron.up.svg` | Collapse indicator |
| `chevron.up.2.svg` | Double-up (advance) |
| `chevron.left.forwardslash.chevron.right.svg` | Code / developer |
| `ellipsis.svg` | More options |
| `xmark.svg` | Close / dismiss / eliminate |
| `plus.svg` | Add actions |
| `minus.svg` | Remove |
| `sidebar.left.svg` | iPad sidebar toggle |

#### Status & Verification
| File | Usage in Orbit |
|------|---------------|
| `checkmark.svg` | Generic confirm |
| `checkmark.circle.svg` | Done state |
| `checkmark.circle.fill.svg` | Completed stage |
| `checkmark.seal.svg` | Shortlisted |
| `checkmark.seal.fill.svg` | Shortlisted (filled) |
| `checkmark.shield.svg` | Verified / admin |
| `person.fill.checkmark.svg` | Member confirmed |
| `person.crop.circle.badge.checkmark.svg` | Participant verified |
| `person.crop.circle.badge.xmark.svg` | Participant issue |
| `flag.svg` | Close event |
| `flag.fill.svg` | Red-flag team |
| `exclamationmark.triangle.svg` | Warning |
| `hourglass.svg` | Pending / waiting |
| `clock.svg` | Scheduled / deadline |
| `timer.svg` | Countdown |

#### People & Identity
| File | Usage in Orbit |
|------|---------------|
| `person.svg` | Name field, profile |
| `person.2.svg` | Team / members |
| `person.3.svg` | Group / organizers |
| `person.2.wave.2.svg` | Community |
| `person.badge.plus.svg` | Add member |
| `person.badge.key.svg` | Organizer role |
| `person.badge.shield.checkmark.svg` | Admin role |
| `person.2.badge.gearshape.svg` | Manage organizers |
| `person.text.rectangle.svg` | ID card / roll number |
| `graduationcap.svg` | Student / participant role |
| `faceid.svg` | Face ID auth |

#### Events & Hackathons
| File | Usage in Orbit |
|------|---------------|
| `ticket.svg` | Join event / pass |
| `ticket.fill.svg` | Active pass |
| `calendar.svg` | Calendar tab / schedule |
| `calendar.badge.plus.svg` | Add to calendar |
| `calendar.badge.checkmark.svg` | Calendar confirmed |
| `calendar.badge.clock.svg` | Calendar deadline |
| `trophy.svg` | Results / winner |
| `star.svg` | Rating |
| `star.fill.svg` | Favourite |
| `gift.svg` | Prize |
| `tag.svg` | Event ID / track |
| `map.svg` | Event space |
| `map.fill.svg` | Event space (active) |
| `mappin.and.ellipse.svg` | Venue pin |

#### Actions & Operations
| File | Usage in Orbit |
|------|---------------|
| `square.and.pencil.svg` | Compose announcement |
| `pencil.svg` | Edit |
| `trash.svg` | Delete |
| `archivebox.svg` | Archive |
| `square.and.arrow.up.svg` | Share / export |
| `arrow.up.svg` | Advance team |
| `arrow.down.svg` | Reorder / demote |
| `arrow.up.circle.fill.svg` | Submit |
| `arrow.trianglehead.branch.svg` | Branch / stage |
| `magnifyingglass.svg` | Search |
| `gearshape.svg` | Settings |

#### Media & Files
| File | Usage in Orbit |
|------|---------------|
| `photo.svg` | Poster / image picker |
| `link.svg` | URL / project link |
| `document.badge.ellipsis.svg` | Submission file |
| `document.on.document.svg` | Copy |
| `text.document.svg` | Rules / description |
| `list.bullet.svg` | List view |
| `list.bullet.rectangle.portrait.svg` | Registrations list |
| `list.bullet.rectangle.portrait.fill.svg` | Registrations (filled) |
| `chart.bar.svg` | Report / statistics |
| `tablecells.svg` | Grid / table view |
| `square.2.layers.3d.svg` | Tracks |
| `square.2.layers.3d.fill.svg` | Tracks (filled) |
| `square.3.layers.3d.svg` | Multi-stage |
| `slider.horizontal.3.svg` | Filters |
| `slider.vertical.3.svg` | Vertical filter |
| `line.3.horizontal.decrease.svg` | Filter/sort |

#### System & Security
| File | Usage in Orbit |
|------|---------------|
| `lock.svg` | Locked state / locked fields |
| `lock.fill.svg` | Locked (filled) |
| `lock.open.svg` | Unlocked |
| `key.svg` | Password / access key |
| `bell.svg` | Notifications |
| `bolt.svg` | Live / processing |
| `bolt.fill.svg` | Live (filled) |
| `qrcode.svg` | QR display |
| `qrcode.viewfinder.svg` | QR scanner |
| `eye.svg` | Show password |
| `dot.radiowaves.left.and.right.svg` | Live broadcast |
| `play.circle.svg` | Start / open event |
| `lifepreserver.svg` | Help desk |
| `lightbulb.svg` | Tip / info |
| `heart.svg` | Like |
| `heart.fill.svg` | Liked |
| `indianrupeesign.circle.svg` | Fee |
| `phone.svg` | Phone number |
| `video.svg` | Video/virtual |
| `display.svg` | Screen / presentation |
| `building.2.svg` | Organisation |
| `building.columns.svg` | Institute |
| `apple.meditate.circle.svg` | Wellness/break |

#### Platform Icons
| File | Usage in Orbit |
|------|---------------|
| `apple.logo.svg` | Sign in with Apple button |
| `iphone.svg` | iPhone illustration |
| `ipad.svg` | iPad illustration |
| `macbook.and.iphone.svg` | Multi-device |

---

## 3. Inline SVG Assets (Generated in JS)

The following graphics are rendered procedurally in JavaScript — they are not files but need Figma equivalents:

| Element | Description |
|---------|-------------|
| Orbit wordmark / logo | Rendered inline in landing screen `lhero` section |
| Ring progress indicator | SVG arc animation (`.ring > svg`) used in pass, QR, and send screens |
| Check-mark animation (okmark) | Animated SVG check used in success states |
| Poster SVG fallback | Placeholder vector inside `.poster` when no image is set |
| OTP dot indicators | 4 `<i>` elements styled as large digit boxes |
| Stage progress dots (`.dots`) | Small 7px circles |
| Progress bar (`.bar`) | CSS bar with gradient fill |

---

## 4. Dynamic Color-Tinted Icons

All SF Symbol SVGs inherit `color` via CSS, so the same SVG appears in multiple tints:

| Tint | Contexts |
|------|---------|
| `--ac` (`#3781fc`) | Default icon tint inside `.li` tiles, field icons |
| `--ac2` (`#cbe9fd`) | Info-row icons in profile/organizer screens |
| `--fg2` | Inactive/dim icons |
| `#fff` | Icons inside filled buttons, selected tiles |
| `--red` | Danger icons, red-flag, eliminate |
| `--em` (`#34c759`) | Success / shortlisted icons |

---

## Summary

| Category | Count |
|----------|-------|
| Poster images (`.jpg`) | 4 |
| Numbered circle SVGs (outline + fill, 1–50) | 100 |
| Named SF Symbol SVGs | 107 |
| **Total SVG files** | **207** |
| Inline / procedural graphics | ~7 |
