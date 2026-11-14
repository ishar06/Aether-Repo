# Orbit

> **Discover. Build. Repeat. Compete.**
> One app that runs a campus hackathon end to end: from the poster on the notice board to the winner's announcement.

Orbit is a mobile and tablet app concept for **discovering, registering for, running and judging college hackathons**. This repository (codename *Aether*) holds the **interactive prototype** (iPhone and iPad), the **design system**, and the **Figma export documentation**.

---

## Table of Contents

1. [TL;DR](#1-tldr)
2. [The Need: Why Orbit Exists](#2-the-need-why-orbit-exists)
3. [What Orbit Is](#3-what-orbit-is)
4. [Who It Is For (Roles)](#4-who-it-is-for-roles)
5. [Feature Tour](#5-feature-tour)
6. [How It Is Implemented](#6-how-it-is-implemented)
7. [Design System](#7-design-system)
8. [Running the Prototype](#8-running-the-prototype)
9. [Repository Map](#9-repository-map)
10. [What Makes Orbit Different](#10-what-makes-orbit-different)
11. [Current Status and Known Limitations](#11-current-status-and-known-limitations)
12. [Future Scope](#12-future-scope)
13. [Project History](#13-project-history)
14. [License](#14-license)

---

## 1. TL;DR

| | |
|---|---|
| **What** | A three-role (Participant / Organizer / Admin) hackathon management app for colleges |
| **Problem** | Campus hackathons run on a patchwork of forms, spreadsheets, WhatsApp groups and paper lists |
| **Solution** | One place to discover events, register teams, check in with QR, submit projects, communicate and score stage by stage |
| **Form** | Fully interactive HTML/CSS/JS prototype in an iPhone frame and an iPad frame |
| **Look** | "Liquid Glass" dark-blue UI: translucent materials, blur, specular highlights |
| **Backend** | None yet. Everything runs on in-memory demo data |
| **Try it** | Open [`docs/index.html`](docs/index.html) (iPhone) or [`docs/iPad.html`](docs/iPad.html) (iPad) in a browser |

---

## 2. The Need: Why Orbit Exists

Hackathons are one of the most important ways students learn to build, collaborate and ship. But **running** one is far harder than attending one, and the tooling has not kept up.

### The problem today

A typical college hackathon is stitched together from tools that were never designed for it:

| Job to be done | What organizers use today | What goes wrong |
|---|---|---|
| Announce the event | Instagram posters, WhatsApp forwards, notice boards | Students miss it, or can't find rules, dates and prizes in one place |
| Register teams | Google Forms | No team-formation flow, duplicate or invalid entries, no verification that members really belong to the institute |
| Verify identity and attendance | Paper lists, manual roll calls | Slow queues at check-in, impersonation, no live headcount |
| Collect submissions | Email, Drive links, form uploads | Scattered across inboxes, hard to review per stage, no audit trail |
| Judge and progress teams | Spreadsheets | Version conflicts, no live view, eliminations communicated by hand |
| Answer questions | WhatsApp groups | Noise, missed messages, no separation between "team", "organizer" and "public" conversations |
| Announce deadlines and changes | Group messages | Participants miss updates; pinned messages get buried |
| Hand out organizer access | Sharing a single admin account | No scoping, no revocation, no accountability |

### Who feels the pain

- **Participants** juggle five apps to know what to do next, and often don't know whether they were shortlisted until someone messages them.
- **Organizers** (usually student volunteers) burn event-day energy on logistics, not on the experience.
- **Institutes and admins** have no consistent record of how many events ran, who organized them, or how teams progressed.

### The core insight

A hackathon is **not a single form. It is a pipeline**: *discover → register → check in → submit → get judged → advance or be eliminated → results*, with communication running alongside every step. The pain comes from the pipeline living in many disconnected tools. Orbit puts the whole pipeline, and everyone in it, inside one coherent product.

---

## 3. What Orbit Is

Orbit is a **role-aware, stage-driven hackathon platform**. The name is deliberate: every team *orbits* an event through successive stages, and the app is the gravitational centre that keeps participants, organizers and admins in sync.

Three ideas hold the product together:

1. **The Event is the unit.** Each hackathon is a self-contained space with its own poster, tracks, prizes, stages, teams, announcements, help desk and activity log.
2. **Stages are the spine.** An event is an ordered list of stages (for example *Registration → Ideation → Prototype → Final Pitch*). Each stage has a deadline, criteria and deliverables. Teams advance or are eliminated stage by stage, and everyone sees progress live.
3. **Roles shape the app.** The same app presents a different set of tabs, screens and powers depending on who you are.

---

## 4. Who It Is For (Roles)

| Role | Who they are | Tabs | Headline powers |
|---|---|---|---|
| **Participant** | A student looking to compete | Events · Event Space · Messages | Browse events, register a team, get a QR pass, submit projects, track stage progress, see results |
| **Organizer** | A volunteer running one event | Home · Live · Messages | Check teams in with QR, review submissions, advance or eliminate teams, shortlist, red-flag, answer the help desk, post announcements |
| **Admin** | The institute-level owner | Home · Live · Messages | Everything an organizer can do, plus creating events, building stages, issuing and revoking organizer access, closing events |

Sign-in is role-first. A *Choose Your Role* sheet routes users into the right onboarding:

- **Participant:** institute email + personal email, roll number, 4-digit OTP verification.
- **Admin:** institute code (for example `80002` → Chitkara University) plus verification.
- **Organizer:** joins a *specific event* using an **Event ID + Event password** issued by the admin. Access is scoped to that event and can be revoked at any time.
- **Sign in with Apple** is available as an alternative path (demo identities in the prototype).

---

## 5. Feature Tour

### 5.1 Participant journey

1. **Discover**: Events list with poster cards, status chips and *Upcoming / Registered* filters.
2. **Understand**: Event Details show the poster hero, domains/tracks, dates, team size, prizes, fee and rules.
3. **Register**: Team name, department picker, add or invite members, eligibility summary. Invited members get a notification to accept or decline.
4. **Get a pass**: **My Pass** with an animated QR ring, team roster and stage-progress dots, used for check-in on event day.
5. **Compete**: **Event Space** shows the live stage map, the active stage's criteria and deadline, and the **Submit Project** flow (link, file, repo, video, prototype).
6. **Plan**: *Add to Calendar* lets you choose which stage deadlines to add.
7. **Communicate**: Team Chat, Organizer Chat, Help Desk (Doubt / Technical / Other), Community feed and Announcements, all under one Messages tab.
8. **Finish**: **Result** screen with placement, stage summary, or the stage at which a team was eliminated.

### 5.2 Organizer and admin journey

1. **Create** *(admin)*: a **4-step New Event wizard**:
   1. Basic details (name, domain, poster)
   2. Schedule and team sizes
   3. Tracks, prizes and fee
   4. Links, rules and contact

   Publishing generates **organizer access credentials** (Event ID + password).
2. **Design the pipeline**: **Stage Builder** to add, reorder and edit stages with name, deadline, criteria/rubric and poster.
3. **Open registrations** and watch the dashboard: teams registered, check-ins, shortlisted, active.
4. **Review registrations**: filter by *All / Red-flagged / Eliminated*; red-flag teams with a reason; eliminate if needed.
5. **Check in on event day**: the **QR check-in** sheet uses the device camera (via jsQR) to scan participant passes, with live arrival stats.
6. **Judge live**: the **Live** tab shows a team grid per stage with filters and search. Advance (with an optional note) or eliminate (with an optional reason).
7. **Shortlist and finalize**: select teams, then run the animated *Finalize* sequence (check → QR → alerts → emails → deadline).
8. **Run support**: Help Desk inbox with unread counts, Organizer Chat, Announcements (priority, pinning, archiving), Community posts.
9. **Report and close**: stats grid (teams, arrived, shortlisted, eliminated, finalists) and a guarded **Close Event** action.
10. **Control access**: add organizers by roll number or email; revoke and restore the whole event's access or a single member.
11. **Audit**: a filterable **Activity Log** records who advanced, eliminated or changed what.

### 5.3 Cross-cutting

- **Notifications sheet** with actionable rows (accept invite, jump to destination).
- **Profile sheet**: phone, department, admission year, delete account, sign out.
- **Empty, locked and read-only states** handled throughout (for example chat locks when an event is closed).
- **Destructive actions always confirm** through an alert dialog (17 variants documented).

### 5.4 By the numbers

| Category | Count |
|---|---|
| Unique push screens | 30 |
| Auth flow steps | 5 |
| Bottom-sheet overlays | 24 |
| Alert dialog variants | 17 |
| Distinct interactive element types | ~130 |
| SVG icon assets (SF Symbols) | 207 |

Full details: [`docs/figma-export/screen-inventory.md`](docs/figma-export/screen-inventory.md) and [`docs/figma-export/interaction-map.md`](docs/figma-export/interaction-map.md).

---

## 6. How It Is Implemented

### 6.1 Architecture at a glance

The prototype is intentionally **dependency-light**: each device is a **single self-contained HTML file** with inline CSS and JavaScript. There is no build step, framework or package manager.

```
 ┌──────────────────────── Browser ────────────────────────┐
 │  index.html / iPad.html                                 │
 │                                                         │
 │   Device frame (CSS)  ──►  #app  ◄── render(state)      │
 │                              ▲                          │
 │            ┌─────────────────┼──────────────────┐       │
 │            │                 │                  │       │
 │        SC.*  screens     SH.*  sheets      ask()  alerts│
 │       (navigation stack)  (bottom overlays) (dialogs)   │
 │            │                 │                  │       │
 │            └──────── data-act dispatcher ───────┘       │
 │                              │                          │
 │                      A.*  action handlers               │
 │                              │                          │
 │                   S  (single in-memory state)           │
 │                   ev0 / ev1 / ev2 demo events           │
 └─────────────────────────────────────────────────────────┘
        External: qrcode-generator, jsQR (CDN)
```

### 6.2 Core patterns

| Pattern | What it is | Why |
|---|---|---|
| **Single state object `S`** | One in-memory object holds events, teams, chats, invites, form drafts and UI state | Easy to reason about; every screen is a pure view of `S` |
| **`SC.*` screens** | Functions that return HTML for a screen on a per-tab **navigation stack** (push/pop) | Mirrors native iOS navigation |
| **`SH.*` sheets** | Functions that render bottom sheets over any screen | Mirrors native modal sheets, including nested sheets |
| **`ask({...})` alerts** | A single helper for centred confirm dialogs, optionally with an input field | Consistent destructive-action UX |
| **`data-act` dispatch** | Elements declare `data-act="name"` (plus `data-n`, `data-i`, `data-bind`); one delegated handler routes to `A.name` | No per-element listeners; the full action surface can be extracted from the markup |
| **`data-bind`** | Declarative two-way binding of inputs to paths in state (for example `au.code`, `nd.tag`, `mm.*`) | Forms without a framework |
| **Role-conditional rendering** | The same `SC.ahome` or `SC.msgs` function renders differently for participant, organizer and admin | One code path, many experiences |
| **State-driven sub-screens** | One screen function renders different content by data state (pre-event, active, complete, closed) | Realistic lifecycle without extra routes |
| **Procedural graphics** | Logo, ring progress, checkmark animation, poster fallbacks and QR fallbacks are generated as inline SVG | No image dependencies; crisp at any scale |

### 6.3 Domain model (simplified)

```
Institute ── has ──► Admin(s)
    │
    └── hosts ──► Event
                    ├─ poster, venue, domains/tracks, years, departments
                    ├─ prizes[], min/max team size, fee, deadlines
                    ├─ stages[]  { name, deadline, criteria[], deliverables[], poster }
                    ├─ access    { eventId, password, revoked, members[] }
                    ├─ teams[]   { members[], problem, links, stage, flag, elim, arrived, short, subs }
                    ├─ help      { teamId → thread (topic, messages, unread) }
                    ├─ announcements[], chat[], activity log[]
                    └─ lifecycle { published → registrations open → running → finalized → closed }
```

### 6.4 Event lifecycle

```
Draft ─► Published ─► Registrations open ─► Event running ─► Shortlist finalized ─► Closed
                            │                     │                   │               │
                       teams register       check-in (QR),      emails + QR       read-only,
                       & get passes         stage-by-stage      passes issued     results visible
                                            advance/eliminate
```

### 6.5 Notable implementation details

- **Real QR in, real QR out.** Passes render real QR codes via `qrcode-generator`; check-in uses `getUserMedia` plus `jsQR` to scan from the camera. If either library fails to load, a deterministic fake-QR generator keeps the UI working.
- **Organizer credential generator.** Event access IDs are institute-prefixed (for example `#CU101`) with generated passwords, designed to be scoped, copyable and revocable.
- **Time-relative seed data.** Demo events are created relative to `Date.now()`, so deadlines always look realistic: one event is upcoming, one is mid-run, one is closed.
- **Responsive device framing.** The iPhone (426 × 898) and iPad (1224 × 864) frames scale to fit the browser window.
- **iPad is the same app, re-laid out.** Both files share the same screen/sheet/action stack. iPad swaps the floating tab bar for a collapsible sidebar and adds split-view panes for messages and live scoring.
- **Accessibility touches.** `prefers-reduced-motion` is honoured for animations; large touch targets (44 px minimum); consistent focus and disabled states.

### 6.6 iPhone vs iPad

| Aspect | iPhone | iPad |
|---|---|---|
| Navigation | Floating bottom tab bar | Left sidebar (268 px) collapsible to a 72 px rail |
| Messages / Live | Single column | Split view (352 px list + detail) |
| Live team grid | 2 columns | 3 columns |
| Sheets | Full width minus margin | 640 px centred |
| Poster ratio | 16:10 | 16:9 |
| Content width | Full | Max 780 px centred |

### 6.7 Tech stack

| Layer | Choice |
|---|---|
| Markup / logic | Vanilla HTML + JavaScript (no framework, no bundler) |
| Styling | Hand-written CSS with custom properties as design tokens |
| Icons | SF Symbols exported as SVG, inlined via an `ic()` helper |
| QR generation | [`qrcode-generator`](https://cdnjs.com/libraries/qrcode-generator) 1.4.4 (CDN) |
| QR scanning | [`jsQR`](https://cdnjs.com/libraries/jsQR) 1.4.0 (CDN) |
| Design handoff | Figma (screens exported; see documentation below) |

---

## 7. Design System

Orbit's visual language is **Liquid Glass**: translucent blue materials with backdrop blur, saturation boost and a specular rim highlight, floating over deep black.

**Colour rule: 60 % black base · 30 % deep blues · 10 % bright blue accent.**

| Token | Value | Role |
|---|---|---|
| `--bg` | `#000` | App background |
| `--s1` / `--s2` / `--s3` | `#020b26` / `#04143f` / `#03195b` | Surface layers |
| `--n1` | `#0b2cb1` | Navy |
| `--b1` | `#1951fc` | Primary blue |
| `--ac` | `#3781fc` | Interactive accent |
| `--ice` / `--fg` | `#cbe9fd` | Foreground |
| `--sky` | `#5fc4ff` | Highlight |
| `--em` | `#34c759` | Success |
| `--red` | `#FF3B30` | Destructive |

- **Typography:** system SF Pro stack, 17 px base, 34 px large titles, uppercase 13 px section headers.
- **Shape language:** generous radii (cards 28, sheets 48/38, buttons 27, fields 20).
- **Motion:** one signature curve, `cubic-bezier(.22,.8,.24,1)`, for pushes, sheets and tab bars; springy overshoot for alerts.
- **Iconography:** 207 SF Symbol SVGs, tinted through CSS `color`.

The full token list (colours, gradients, shadows, surface recipes, type scale, radii, spacing, motion, components, device frames) is in [`docs/figma-export/design-system.md`](docs/figma-export/design-system.md).

---

## 8. Running the Prototype

No install, build or server required.

```bash
git clone <this-repo-url>
cd Aether-Repo/docs
open index.html     # iPhone prototype
open iPad.html      # iPad prototype
```

(On Linux use `xdg-open`; on Windows just double-click the file.)

**Tips**

- Use a modern Chromium, Safari or Firefox. The frame auto-scales to the window.
- Internet access is needed only for the two CDN scripts (QR generation and scanning). Without them the app falls back to placeholder QRs.
- **Camera access** is requested only on the QR check-in sheet. Serve over `http://localhost` (for example `python3 -m http.server` inside `docs/`) if your browser blocks the camera on `file://`.
- Every sign-in step has a **demo shortcut**, so you can explore each role in seconds.

**Suggested walkthrough**

| Goal | Path |
|---|---|
| See the participant experience | Landing → *Get started* → **Participant** → demo shortcut → open *Green Code 25* → **View Pass** → **Event Space** |
| See organizing | Landing → **Organizer** → demo event shortcut → **Live** tab → advance or eliminate a team |
| See event creation | Landing → **Admin** → **New Event** → walk the 4-step wizard → **Stage Builder** |
| See a closed event | Open *Byte Fest 25* as a participant → **Result** |

---

## 9. Repository Map

```
Aether-Repo/
├── README.md                     ← you are here
├── LICENSE                       ← MIT
└── docs/
    ├── index.html                ← iPhone prototype (single file)
    ├── iPad.html                 ← iPad prototype (single file)
    ├── images/                   ← Event posters (Byte Fest 25, Green Code 25, Nexus Hack 26 ×2)
    ├── SF Symbols/               ← 207 SVG icons (numbered circles 01–50 + named symbols)
    └── figma-export/             ← Design-handoff documentation
        ├── screen-inventory.md   ← Every screen, sheet and alert, by role and state
        ├── interaction-map.md    ← Every tap target → its destination
        ├── design-system.md      ← Tokens, type, radii, spacing, motion, components
        └── asset-inventory.md    ← Posters, icons, procedural graphics
```

> The folder is named `docs/` so the prototype can be served directly through GitHub Pages.

### Where to look first

| If you want to... | Start here |
|---|---|
| Understand the product flow | [`screen-inventory.md`](docs/figma-export/screen-inventory.md) |
| Know what every button does | [`interaction-map.md`](docs/figma-export/interaction-map.md) |
| Restyle or extend the UI | [`design-system.md`](docs/figma-export/design-system.md) |
| Add or swap icons and images | [`asset-inventory.md`](docs/figma-export/asset-inventory.md) |
| Change behaviour or demo data | The `<script>` block in [`index.html`](docs/index.html) (state `S`, seed events `ev0`–`ev2`, `SC.*`, `SH.*`, `A.*`) |

---

## 10. What Makes Orbit Different

### Uniqueness

1. **The whole pipeline, not one step.** Most tools cover registration *or* judging *or* chat. Orbit covers discovery through results in one product.
2. **Stage-first modelling.** Multi-round hackathons (ideation → prototype → pitch) are the default, not a custom workaround. Every stage has its own deadline, criteria, deliverables and per-team status.
3. **Three roles, one app.** Participants, event-scoped organizers and institute admins share a single codebase and data model. Each role sees only the tools it needs.
4. **Event-scoped, revocable organizer access.** Volunteers join with an Event ID and password, can be added or removed individually, and can be locked out instantly. No more sharing one admin login.
5. **Institute-verified identity.** Dual-email verification (institute plus personal) and roll numbers tie participants to real students.
6. **Teams are first-class.** Invites, accept/decline, replace and remove members, plus eligibility rules (department, year, team size) built into registration.
7. **QR-driven event day.** Pass → scan → arrival stats → shortlist, with a live headcount and no paper lists.
8. **Segmented communication.** Team chat, organizer chat, help desk (with topics and threads), community feed and prioritized announcements are separate channels, not one noisy group.
9. **Accountability built in.** Every advance, elimination and flag carries an optional note or reason and lands in an activity log.
10. **Safe by default.** Destructive actions are always confirmed; closed events become read-only; locked chats explain themselves.

### Innovation in design and craft

- A cohesive **Liquid Glass** design language implemented in pure CSS (blur, saturation, specular sheen, layered gradients), consistent from tab bar to alert dialog.
- **Native-feeling interaction model** (navigation stacks, bottom sheets, nested sheets, springy alerts) delivered in a plain browser file.
- **Prototype-as-specification.** The `data-act` architecture lets the entire interaction surface be extracted mechanically, which is how the Figma documentation in `docs/figma-export/` was produced.
- **Fully offline-capable demo.** No backend means stakeholders can click through the real flows anywhere, instantly.

---

## 11. Current Status and Known Limitations

Orbit is a **high-fidelity interactive prototype**, built to validate flows and visuals before engineering a production system.

| Area | Status |
|---|---|
| iPhone and iPad UI | Complete, interactive |
| All three roles end to end | Complete on demo data |
| Design system and Figma export docs | Documented |
| Persistence | **None.** State resets on reload |
| Backend / API | **None.** All data is seeded in memory |
| Authentication | **Simulated.** OTP, Apple sign-in and institute lookup are demo flows |
| Emails / push notifications | **Simulated.** Shown as UI animation and toasts |
| QR | Real generation and camera scanning, but passes are not validated against a server |
| Light theme | Explored earlier in the history; the current build is dark-only |
| Automated tests | None |

---

## 12. Future Scope

> These are proposed directions, not commitments. They are roughly ordered by dependency: foundations first, then growth.

### Phase 1: Make it real
- **Backend and database** (events, teams, submissions, chat, audit log) with an API.
- **Real authentication:** institute SSO or email OTP, Sign in with Apple, role and permission enforcement on the server.
- **Server-validated QR passes**, signed and single-use, so check-in cannot be forged.
- **Push and email delivery** for shortlist results, deadline reminders and announcements.
- **Real-time sync** (WebSockets or similar) so chats, live scoring and announcements update instantly across devices.
- **File storage** for submissions, posters and attachments.
- **Native apps** (SwiftUI for iOS and iPadOS first), using the prototype and Figma export as the spec.

### Phase 2: Deepen the judging and participant experience
- **Rubric-based scoring** per stage with multiple judges, weighted criteria and automatic aggregation.
- **Judge role** and blind or double-blind review modes.
- **Leaderboards** and live results screens for projectors on event day.
- **Team formation marketplace:** find teammates by skill, department and interest.
- **Mentor scheduling:** book time slots with mentors per stage.
- **Submission tooling:** GitHub and Figma link previews, plagiarism or duplicate checks, and deadline enforcement.
- **Certificates and digital badges** auto-issued for participation, advancement and winning.
- **Calendar integration** with real `.ics` / system calendar sync.

### Phase 3: Platform and ecosystem
- **Multi-institute network:** cross-campus discovery, inter-college hackathons, shared leaderboards.
- **Participant portfolio:** a persistent profile with every hackathon, project and result, shareable with recruiters.
- **Sponsor and recruiter portal:** sponsor-branded tracks, prize management, talent discovery.
- **Analytics dashboards for admins:** participation by department and year, retention, stage drop-off, event comparisons.
- **Payments:** event fees, prize disbursement and sponsor invoicing (the fee field already exists in the event model).
- **Hybrid and virtual events:** video rooms, online check-in, timezone-aware stages.
- **Accessibility and localisation:** full screen-reader support, Dynamic Type, regional languages.
- **Light and dark theme parity** and **Android / web** clients.

### Intelligent features (exploratory)
- Smart event recommendations from skills and past participation.
- AI-assisted shortlist summaries and submission digests for judges.
- Automatic announcement drafting from schedule changes.
- Anomaly detection for suspicious registrations and duplicate teams.

---

## 13. Project History

| Date | Milestone |
|---|---|
| 2026-10-22 | Initial commit; first HTML prototype |
| 2026-10-23 – 25 | UX refinement, iPad version, Manage Teams sheet, SF Symbols, shortlist modal sheet, secured organizer credentials |
| 2026-10-26 – 27 | White-theme exploration, table view rework, messages fixes |
| 2026-10-28 | Event-closed and registrations-closed states |
| 2026-10-30 | **Orbit** defined as the product identity |
| 2026-10-31 | UI/UX changes after mentor feedback |
| 2026-11-13 | Colour theme unified; gradient background effects; illustrations |
| 2026-11-14 | Figma screens exported; `figma-export/` documentation written |

---

## 14. License

Released under the [MIT License](LICENSE). © 2026 Ishardeep Singh.
