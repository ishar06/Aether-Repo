# Orbit iPad — Screen Inventory

Source: `docs/iPad.html` (2,578 lines; one `<style>` block, one `<style id="ipad-os">` block, one inline `<script>`).
Method: static analysis of the markup, CSS cascade and JavaScript. No runtime crawl was run, so the counts below come from reading the source. `iPad.html` and `index.html` were not modified.

> **The iPad JS is not the iPhone JS with a new stylesheet.** The two scripts share about 99% of their code (the screen builders `SC.*`, the sheet builders `SH.*` and the action table `A`), but iPad.html replaces the shell and navigation engine. The differences are: a sidebar replaces the tab bar, two tabs per role are split-view, there is a new landing layout, and the iPad has its own overlay sizes. Everything in this file is derived from `iPad.html` itself. Where iPad behaviour differs from the older iPhone docs in this folder, the iPad behaviour is what's written here.

---

## 0. Terminology

| Term | Meaning in the code |
|---|---|
| **Screen** | A full-pane view built by `SC.<name>()`. It is mounted as `<div class="screen" data-n="<name>">` inside a pane. |
| **Sheet** | A modal panel built by `SH.<name>()`. It lives in `#sheets > #sheetbox`. On iPad it is a centred panel, not a bottom drawer. |
| **Alert** | A centred dialog opened by `ask({...})` into `#alerts`. |
| **Banner / Toast** | Transient notifications: `#banners` (top, tappable) and `#toast` (bottom). |
| **State** | The same `SC`/`SH` function rendering different content depending on data. It is not a separate screen. |
| **Role** | `V.role` is only `'p'` (participant) or `'a'`. An **Organizer** is `role 'a'` with `V.org` set, so there are three logical roles on two tab sets. |

Two tab sets exist (`TABS`):

| Role | Sidebar items (key · SF symbol · label · root screen) |
|---|---|
| Participant (`p`) | `events` · calendar · **Events** · `SC.events` / `space` · map · **Event Space** · `SC.space` / `msgs` · bubble · **Messages** · `SC.msgs` |
| Admin / Organizer (`a`) | `home` · house · **Home** · `SC.ahome` / `live` · live · **Live** · `SC.live` / `msgs` · bubble · **Messages** · `SC.amsgs` |

---

## 1. Totals

| Metric | Count |
|---|---|
| Unique full-pane screens (landing + 28 `SC.*`) | **29** |
| Tab roots | 6 (3 participant, 3 admin/organizer) |
| Distinct screens reachable in the shipped UI | 28 (`SC.shortlist` has no entry point; see §9) |
| Sheet types (`SH.*`) | **24** |
| Alert dialog call-sites (`ask`) | **18** (24 dialog variants, plus 18 wizard validation messages) |
| Banner variants | 3 (info, red reminder, invitation with Accept/Decline) |
| Toast | 1 component, 28 distinct messages |
| Dropdown popovers (`.dd`) | 2 instances (`mgs`, `tds`) |
| Accordions (`acc`) | 13 keys |

---

## 2. Authentication & entry (no role yet)

### 2.1 Landing — `showSignIn()` (not an `SC`; renders `.land` into `#app`)
- Sidebar, tab nav and home bar are absent (`#homebar` is hidden, `V.role=null`).
- Content: a 400 px-wide centred stack `.lstack`:
  - animated orbit logo (`logo(188)`)
  - wordmark "Orbit" (52 px)
  - tagline "Discover. Build. Repeat. Compete."
  - `Get Started` button (`data-act=sheet data-n=roles`)
  - divider "or"
  - white `Sign in with Apple` button (`data-act=sheet data-n=apple`)
- Background: layered radial gradients, plus two `<canvas class="rain">` layers (twinkling stars and "bin-rain" shots) injected by `binRain()`.
- **iPad difference:** the iPhone landing has absolutely-positioned content and no divider. The iPad landing is a flex-centred 400 px column with an "or" divider.

### 2.2 Auth sheets (all centred 640 px sheets over the landing)
| Sheet | Opened from | Notes |
|---|---|---|
| `SH.roles` "Choose Your Role" | Get Started; `auBack` from first auth step | 3 radio-style rows: Participant, Organizer, Admin. Each is `data-act=authOpen data-r=<p|o|a> data-from=roles`. |
| `SH.auth` | `authOpen`, `joinOpen`, Apple flow | One sheet, 5 steps (below). |
| `SH.apple` "Sign in with Apple" | Landing button, `auApple` | Lists 4 demo Apple IDs. While `busy` it shows a spinning Face ID glyph and is non-dismissable (`nodismiss`). |

**`SH.auth` steps** (`S.au.step`): `start` → `profile` → `otp` → `linked` → `event`.

| Step | Content | Exits |
|---|---|---|
| `start` | Sign In / Sign Up segmented control (`auMode`), email + password with show/hide (`auPw`), role info blurb, "demo" fill (`auDemoIn`), OR + Sign in with Apple | `auNext` validates and goes to the session; errors: no account, wrong role, wrong password. |
| `profile` (sign-up) | Full name; admin: institute code, else roll no.; institute email; personal email; password with strength message | `auNext` → `otp` |
| `otp` | 4 digit boxes (64 × 76) over a hidden input; resend countdown 30 s (`auResend`); 3 attempts; the code is delivered as a **banner** ("Your Orbit code is ####") | verified → `linked` |
| `linked` | Success mark; both emails confirmed | continues into session; an Organizer with no events goes to `event` |
| `event` | Organizer only: Event ID + Event password, demo fill (`auDemo`) | `auNext` → `auJoin` |

Back (`auBack`) pops step history; from the first step it re-opens `SH.roles` with the slide-back animation.
Successful sign-in calls `enterSession()` → `startRole()`, which builds the sidebar and panes.

---

## 3. Shell (post-auth)

Not a screen, but it shapes every screen:

- `#side` (floating glass sidebar): logo, collapse button `#sbtn`, role label, `nav#tabs`, and an account button (opens `SH.profile`).
- `#main`: holds one `.pane` per tab. Panes are cross-faded; only one has `.on`.
- Sidebar has two states: **expanded** (268 px) and **rail** (72 px, `#app.rail`). Toggling is JS-driven (`#sbtn`).
- Tab badges: `i.badge` on Messages (unread announcements + help-desk threads) and `i.dot`. The `pass` dot is a leftover; no `pass` tab exists on iPad.
- Participant panes `events` and `msgs`, and admin panes `home` and `msgs`, are **split view** (`.pane.split`): primary column `.col1` 352 px, secondary column `.col2` fills the rest. An empty `.col2` shows the "nosel" placeholder.

| Split tab | Placeholder icon / title / sub |
|---|---|
| Participant · Events | calendar · "Select an Event" · "Choose an event from the list to see its details." |
| Participant · Messages | bubble · "No Conversation Selected" · "Choose something from the list." |
| Admin · Home | house · "Select an Event" · "Choose an event to manage it." |
| Admin · Messages | bubble · "Nothing Selected" · "Choose something from the list." |

Placeholder state count: 4.

---

## 4. Participant screens (role `p`)

### 4.1 Events tab (split)

| # | Screen (`SC`) | Pane | Reached by | States |
|---|---|---|---|---|
| P1 | `events` | col1 root | tab Events | Search; filter button with badge; status chips Open / Active / Registration closed / Closed (multi-select); list of published events as glass cards; empty "No Results"; filter-active state. Shows ambient "cloud" layer. |
| P2 | `details` | col2 | tap event card (`openEvent`) | Registered (CTA "View My Pass"); open + profile incomplete ("Complete Profile to Register"); open + ineligible (locked CTA "Not eligible · Year n · DEPT"); open + eligible ("Register Team"); registration closed / event closed (locked CTA). Accordions: Eligibility (open), Deadlines (open), Domains, Prizes. Optional About and Contact rows. Poster carousel with page dots when the stage poster exists. First detail in col2 has its back button hidden. |
| P3 | `register` | col2 (stack 2) | `openReg` (blocked with toast if profile incomplete or ineligible) | Progress ring n/k; fields: team name, roll, department chips, problem statement (min 20, max 200 chars with counter), optional link fields (LinkedIn / Video / Prototype per event), members list with remove, "Add Member"; CTA disabled until all checks pass; busy spinner on submit. Inline CTA (not floating). |
| P4 | `pass` | col2 (stack 2 or 3) | `goEv` from details CTA | No team (empty + Browse Events); Pending (hourglass ring); Shortlisted (QR); Shortlisted + checked in (QR with "Checked in" stamp); Eliminated-after-shortlist (QR with "Out" chip); Not shortlisted / Eliminated (red ring). Team card with Edit button (leader only) or "Fixed" lock chip. |
| P5 | `result` | pushed in the Event Space pane | Event Space "Result" row | No team (empty trophy); Pending; Advanced; Eliminated. Stage roadmap. |

### 4.2 Event Space tab (single pane)

| # | Screen | States |
|---|---|---|
| P6 | `space` | **Gated by the event picker:** until an event is chosen (`S.picked`), `pickTab` renders a "Pick an event" empty state with orbiting-icon art (`.pickart`) and a pulsing pill. After picking: current-stage hero with progress ring and segmented bar; countdown (days, hours, min, sec); Roadmap list (each row opens `SH.stageInfo`); "Submit Project" call-to-action; Result / Add to Calendar / Remind Me rows. States: pre-event, stage in progress, all stages done ("Completed"), event closed (read-only, submit hidden). Read-only variant (`ro=1`) is pushed from the admin Live tab. |

### 4.3 Messages tab (split)

| # | Screen | Pane | States |
|---|---|---|---|
| P7 | `msgs` | col1 root | Event picker gate; search across all sources (results grouped by Team Chat / Help Desk / Community / Announcements, "No Results"); conversation rows with unread badges. Help Desk row appears only if the user has a team. |
| P8 | `chat` | col2 | Team chat. Empty (`bl` glyph), populated, locked (non-members see a lock bar instead of the input). Animated aurora backdrop. |
| P9 | `helpdesk` | col2 | No team (shield); first visit (topic chips Issue / Doubt / Logistics / Other + intro); active thread. |
| P10 | `community` | col2 | Posts with like toggle; composer; locked bar for non-members. |
| P11 | `announcements` | col2 | Active / Archive segments with counts; search; filter sheet; cards with type colour, priority, unread dot; empty states: "All caught up", "Archive is empty", "No Results". Participants cannot compose. |

---

## 5. Admin & Organizer screens (role `a`; `V.org` set for organizers)

### 5.1 Home tab (split)

| # | Screen | Pane | Reached by | States |
|---|---|---|---|---|
| A1 | `ahome` | col1 root | tab Home | **Admin:** title "All Events"; search, filter, status chips; event cards (poster, status chip, team count, "View only" chip for other admins' events); FAB "Create Event". **Organizer:** title "My Events"; same list without FAB, scoped to the organizer's events. Cloud layer; no picker gate. |
| A2 | `aevent` | col2 | `aOpen` (card) | Owner: Registrations row (count), Event Report row, Manage Organizers row (admin only), stat tiles (Registered / Shortlisted / Arrived / Active), "Close Event" button (hidden when closed), Edit chip. Non-owner admin: "View only" chip and explanatory note; Registrations and Manage Organizers rows are removed. Organizer: no Manage Organizers; "Read only" chip when the event is closed. Draft vs published status. |
| A3 | `report` | col2 | Event Report row | Hero with check-in ring, 4 metric tiles, Funnel bars, Stage columns, Department donut, Submissions card. Count-up animation. Blocked by an alert if there are no teams. |
| A4 | `regs` | col2 | `openRegs` | Segments All / Flagged / Eliminated; list; "Shortlist Teams" tile (opens `SH.regManage`). Non-owner admin sees `lockedTab('regs')` ("Registrations are private"). |
| A5 | `orgs` (aliased as `access`) | col2 | Manage Organizers row | Event ID and masked/shown password, Copy, Revoke/Restore, member list with per-member revoke/restore, Add Organizer. Locked variant for non-owner admins ("Only <owner> can manage organizers"). |
| A6 | `close` | col2 | Close Event button | Pre-close summary rings + red CTA; after close: success mark, no CTA. |
| A7 | `newev` | col2 | FAB "Create Event" (`ndOpen`) | 4-step wizard with segmented progress: (1) Basic Hackathon Details, (2) Schedule & Teams, (3) Tracks, Prizes & Fee, (4) Links, Rules & Contact. Completed segments are tappable (`ndGo`). Footer: Next, or Save Draft / Publish on the last step. |
| A8 | `stages` | pushed from `newev` or from `create` | Stage Builder row | List of 2–6 stages (max enforced by `stAdd`, min 2 by `stDelHere`), Save. |
| A9 | `stage` | pushed from `stages` | `stOpen` | Name, deadline, criteria list (add/remove), deliverable toggles, poster picker/uploader, delete. |
| A10 | `create` | **inside `SH.editEvent` only** | Edit chip on `aevent` | Accordion form (name, poster, domains, years, departments, links, deadlines, team size) + Stage Builder and Manage Organizers rows; "Publish Event" / "Update Event". Organizer variant has locked sections. |

### 5.2 Live tab (single pane)

| # | Screen | States |
|---|---|---|
| A11 | `live` | Event picker gate; LIVE badge; read-only Event Space eye button; Scan button (opens `SH.qr`); per-stage column chart; search; All / Arrived n/N / Eliminated segments; **3-column** team grid (iPad override); "Manage Teams" dashed tile; "View Log" row. Non-owner admin sees `lockedTab('live')` ("Live is private"). Empty state when no teams. |
| A12 | `team` | Pushed only from the help-desk thread "more" button. Team header, roadmap, members accordion, links/submission chips, Advance / Eliminate, history. |

### 5.3 Messages tab (split)

| # | Screen | Pane | States |
|---|---|---|---|
| A13 | `amsgs` | col1 root | Event picker gate; search; rows Announcements, Help Desk (locked + unread badge), Organizers (locked). Locked rows toast "Only members of this event can read this channel". |
| A14 | `inbox` | col2 | Unread / All segments, search, thread list, empty states. |
| A15 | `helpthread` | col2 (stack 2) | Team header + topic chip, conversation, reply bar; `more` button → `team`. |
| A16 | `orgchat` | col2 | Same layout as `chat`; "Organizers" title. |
| A17 | `announcements` | col2 | Same screen as P11 with an extra "New" button that opens `SH.post`. |

---

## 6. Shared / cross-role screens
`announcements`, `chat`-style screens, `space` (read-only), and sheets `profile`, `notifs`, `evSwitch`, `filters`, `stageInfo` are used by more than one role. They are listed once in their primary role section above.

---

## 7. Sheets (`SH.*`) — 24

All sheets are 640 px wide (`max-width: calc(100% − 16px)`), pinned bottom-centre with an 8 px gap and a 38 px radius. The `tall` variant is a fixed 764 px height (min(780, 100% − 56)). The `full` variant (QR scanner) covers the whole 1180 × 820 screen. A `.wide` variant (1120 px) exists in CSS but **no sheet uses it**.

| # | Sheet | Size | Dismiss | Opened from | States / sub-views |
|---|---|---|---|---|---|
| 1 | `roles` | std | yes | Landing Get Started | 3 roles |
| 2 | `auth` | std | yes | roles / join | 5 steps (see §2.2) + Sign-in/Sign-up modes |
| 3 | `apple` | std | **no while busy** | Landing, auth | ID list; busy spinner |
| 4 | `profile` | std | yes | sidebar account button; the round A/O/R button in tab-root top bars (`acctBtn()`); Register / details "Complete Profile" gate | Participant/Admin/Organizer variants; "Complete your profile" form (department chips + "Other", admission year, phone); recent events; pending invites; notifications switch; reminder switch; Sign Out; Delete Account |
| 5 | `notifs` | std | yes | bell button | Empty; list; unread subtitle; pending invitation with Accept/Decline; accepted/declined chips; rows with a destination are tappable (`ntGo`) |
| 6 | `evSwitch` | tall | yes | event picker pill (`evOpen`) | Search; filter pills; Recents; "Join an event" and "New" entries for organizers/admins; locked rows (toast); nested `filters` |
| 7 | `filters` | tall | yes (Cancel) | filter button on lists / picker | Status, My eligibility switch, domains, years, departments, prize range (dual range slider), duration steppers, date range; live result count on the apply button; Reset |
| 8 | `deptPick` | std | yes | **no entry point** | Orphan; see §9 |
| 9 | `annFilter` | std | yes (Cancel) | announcements filter button | Type chips, Priority chips, Reset, apply with count |
| 10 | `stageInfo` | std | yes | Roadmap rows / Details chip | Stage name, deadline, description, criteria list, poster |
| 11 | `calendar` | std | yes | Event Space "Add to Calendar" | Per-stage switches; downloads `Orbit.ics` |
| 12 | `submit` | std | yes | Event Space "Submit Project" | Gate states: register first / team eliminated / event closed; form with 5 type tiles (Link, Repo, File, Video, Slides) in a **5-column grid**; file upload progress bar; "Submitted" success with Submit Another |
| 13 | `editTeam` | std | yes | Pass "Edit" chip | Gates: not leader / team fixed; member rows (Leader, Joined, Invited, Declined) with Replace / Remove / Cancel invite / Add someone else / Dismiss; nested `addMember` |
| 14 | `addMember` | std | yes | Register "Add Member"; editTeam | Search + suggestions (`mmFill`), eligibility check, back to `editTeam` (`mmBack`) |
| 15 | `post` | std | yes | Announcements "New" | Type tiles (3 × 2), priority segments, title (60), body (160), archive rule, Broadcast |
| 16 | `editEvent` | tall | yes | `aevent` Edit chip | Hosts a **nested navigation stack** (`V.ov='edit'`): `create` → `stages` → `stage`, and `orgs` |
| 17 | `addOrg` | std | yes | Manage Organizers | Name, roll, email; save |
| 18 | `logs` | std | yes | Live "View Log" | Filter chips by category with counts; "Show more · n"; empty |
| 19 | `manage` | tall | yes | Live "Manage Teams" tile | Stage dropdown, search, team rows; Done; opens `teamD` (slide-forward) |
| 20 | `regManage` | tall | yes | Registrations "Shortlist Teams" tile | All / Flagged / Shortlisted / Out segments, search; opens `regDetail` |
| 21 | `regDetail` | tall | yes (back via nested nav) | `regManage` row | Team header, problem, links, roadmap accordion, members, flag note; Shortlist / Red-flag(Unflag) / Eliminate |
| 22 | `teamD` | tall | yes | `manage` row, Live team card | Stage dropdown (`tds`), per-stage submissions, roadmap, members, Advance / Eliminate, history, Message Team |
| 23 | `send` | std | **no while running** | Finalize alert confirm | Running (ring + step list) → "Shortlist Finalized" with Open Live Monitor |
| 24 | `qr` | **full** | drag-down on grab handle or `closeSheet` | Live "Scan" | idle (viewfinder + camera feed if permitted), scanning (laser), found (info card 520 px + Mark Attendance), done (check mark); bottom tray of shortlisted teams' QR chips (720 px); locked "Finalize shortlist first" when no shortlist |

Sheet transitions: `sheet(name, params, mode)` supports `mode===true` (push: stack + slide-in from the right), `'back'` (slide from the left) and default (replace + stack reset). Nested pairs: `roles`↔`auth`, `evSwitch`↔`filters`, `editTeam`↔`addMember`, `manage`/`regManage`→`teamD`/`regDetail`.

---

## 8. Alerts, banners, toasts

### 8.1 Alert dialogs (`ask`) — 330 px wide, radius 30
| # | Trigger | Title | Buttons |
|---|---|---|---|
| 1 | `orgGate` (organizer access revoked) | Access revoked | OK |
| 2 | `accRev` | Revoke access? / Restore access? | Cancel · Revoke (danger) / Restore |
| 3 | `accMem` | Revoke \<name\>? / Restore \<name\>? | Cancel · Revoke / Restore |
| 4 | `tmRem` | Remove \<name\>? / Cancel invite for \<name\>? | Cancel · Remove / Cancel invite (danger) |
| 5 | `push('report')` with no teams | No Report Yet | OK |
| 6 | `delAcct` | Delete account? | Cancel · Delete (danger) |
| 7 | `ndCreate` (publish/save) | Organizer access ready (shows ID · password) | Done |
| 8 | `publish` (Update/Publish Event) | Add a name / Choose a poster / Pick a domain / Check dates | OK (danger) |
| 9 | `ndWarn` (wizard validation) | 18 messages: Add an event name · Add a short description · Choose a mode · Add a platform or link · Add a venue · Choose a poster · Set all three dates · Check dates · Enter the minimum team size · Check team size · Enter how many teams can join · Pick eligible years · Pick departments · Pick a domain · Enter every prize amount · Choose Free or Paid · Enter the fee per team · Add an organizer email | OK (danger) |
| 10 | `stDelHere` | Delete this stage? | Cancel · Delete (danger) |
| 11 | `stSave` | Name every stage | OK |
| 12 | `flagT` | Red-flag \<team\> (required reason field) | Cancel · Red-flag (danger) |
| 13 | `elimReg` | Eliminate \<team\>? | Cancel · Eliminate (danger) |
| 14 | `shortlistTeam` (`shT`) | Shortlist \<team\>? | Cancel · Shortlist |
| 15 | `finalize` | Finalize shortlist? | Cancel · Finalize |
| 16 | `closeEvent` | Close \<event\>? | Cancel · Close Event (danger) |
| 17 | `advance` | Advance \<team\> (optional note field) | Cancel · Advance |
| 18 | `eliminate` | Eliminate \<team\> (optional reason field) | Cancel · Eliminate (danger) |

### 8.2 Banners (`#banners`, 460 px, top-centre)
1. Standard (blue icon tile): announcement, team events, OTP code (held 14 s).
2. Red reminder (`.bn.red`): stage deadline reminder, or elimination notices.
3. Invitation: shows Decline / Accept buttons; stays until answered.
Tapping a banner (`bnTap`) navigates to its `dest` (pass, space, announcements, help desk, result).

### 8.3 Toasts (`#toast`, bottom 44 px)
Distinct messages: A hackathon needs at least 2 stages · Account deleted · Added to Calendar · Already registered in this event · Already shortlisted · Announcement sent · Complete your profile to register · Credentials copied · Event closed · Event closed · read-only · Event joined · Invitation declined · Invite sent · Invites sent to … · Joined … · New code sent · New event · Not eligible for this event · Only … can view/manage … · Only members of this event can message here · Only members of this event can read this channel · Organizer added · Phone saved · Profile saved · Stages saved · Team is fixed · Team is full · Team needs n+ members.

---

## 9. Ambiguous, orphaned or unreachable items

1. **`SC.shortlist`** is fully built (toggle list, channel chips, Finalize / Open Live Monitor) but nothing calls `push('shortlist')`. Shortlisting now happens in `SH.regManage` → `SH.regDetail` (the `finalize` action is still wired from the unreachable screen).
2. **`SH.deptPick`**, `A.deptOpen`, `A.pickDept`: no markup references them. Department choice on Register uses chips.
3. **`A.newEvent`**: references a tab `'setup'` that does not exist on iPad, so `switchTab` is a no-op. It is not referenced in markup.
4. **`A.openPass`**: not referenced in markup.
5. **`SC.create`** can only appear inside `SH.editEvent`; there is no full-pane Create route.
6. **`.sheet.wide`** (1120 px) exists in CSS; no sheet sets `wide:1`, so Manage Teams / Shortlist Teams use the standard tall sheet.
7. `HART` illustration strips are keyed for `events`, `ahome` and `ahomeA`, but `art()` returns early for `HOMEN` (`events`, `ahome`), so those three never render. Strips render only on `space`, `msgs`, `live`, `amsgs` and `pass`.
8. The `pass` tab dot in `updateBadges()` and the CSS rule that makes `pass` a two-column layout are leftovers: `pass` is never a tab or a non-split screen on iPad, so that rule never matches.
9. `SC.access` is assigned and then replaced by `SC.orgs` (`SC.access=SC.orgs`); both names resolve to the same screen.
10. "Eliminate" from `SC.team` and from `SH.teamD` are the same action but are reached from different places (help-desk thread vs. Live grid).
11. State data is in-memory demo data only (three events: Nexus Hack 26 [upcoming, id 0], Green Code 25 [finalized, live, id 1], Byte Fest 25 [closed, id 2]); persistence, real e-mail and real Apple auth are not implemented.
12. `SH.qr` uses the real camera and jsQR if permission is granted; otherwise it falls back to tapping a team's QR chip. Both paths are real code, but a camera view cannot be captured statically.

---

## 10. Admin states by account and event (added after review)

My first pass read the code statically and did not log in as any account. The Admin UI changes with **who owns the event** (`ownsEv`: event `owner` must equal the signed-in admin's institute email) and with the event's own state. The seed data is below, taken from the `DB.accts` and `ev0/ev1/ev2` definitions.

### 10.1 Admin accounts and the events they own
| Admin account | Email | Owns | Sees the others as |
|---|---|---|---|
| Arjun Nair | `arjun.nair@campus.edu` | **Byte Fest 25** (no explicit owner, so it takes the default admin at creation) | View only |
| **Meera Iyer** | `meera.iyer@campus.edu` | **Green Code 25** (`ev1.owner` is set explicitly) | View only |
| Ishar | `ishardeep@chitkara.edu.in` | **Nexus Hack 26** (`ev0.owner`) | View only |

**Nexus Hack 26 is not owned by Meera Iyer.** Meera sees it in read-only mode: no Registrations row, Manage Organizers and Live replaced by "private" screens, no editing, no announcements. To see Nexus Hack's full admin controls (owner mode), sign in as the **Ishar** account; Meera's full owner controls are on **Green Code 25**. All three accounts share institute `80002`, so every admin sees all three events in the "All Events" list.

### 10.2 Event fixtures (what each event shows)
| Event | Status | Teams | Notable data | Admin states it exercises |
|---|---|---|---|---|
| Nexus Hack 26 (id 0) | Published, **not finalized**, stage 1 of 4 (Registration) | 5 (Null Pointers, Pixel Pirates, Neural Nomads, Stack Smashers, Code Blooms); none shortlisted, none arrived | 3 help threads (2 with unread), 4 announcements (2 unread, one urgent), invitation pending for Null Pointers, 2 organizers (Divya, Kiran) | Pre-finalize: Live shows all teams, scanner tray shows the lock "Finalize shortlist first", Shortlist Teams sheet with nothing shortlisted, `send` flow still available |
| Green Code 25 (id 1) | **Finalized, live**, stage 3 of 4 (Prototype) | 6: 4 shortlisted and arrived (one at stage 2, one at stage 1), 1 shortlisted but not arrived, 1 eliminated (Pixel Pirates), 1 red-flagged and not shortlisted (Null Pointers) | Action log with 2 advances and 1 elimination, 1 submission, Required links limited to Video and Prototype | Live monitor with arrived/out filters, flagged, eliminated, history, scanner tray populated, Advance / Eliminate dialogs |
| Byte Fest 25 (id 2) | **Closed**, finalized, result announced | 5, all arrived; 3 eliminated, 2 finalists (Code Blooms stage 4) | 1 "Winners announced" announcement | Read-only everywhere: Close Event hidden, Event Space read-only, Close summary success mark, messaging locked for non-members |
| A new event from the wizard | **Draft** (unpublished) | 0 | – | "Draft" chip, "No Report Yet" alert, empty Live and Registrations, Publish / Update button states |

Event status labels come from `evStatus()` (Open / Active / Registration closed / Closed / Draft).

### 10.3 Admin screen states: owner vs not owner
| Screen / control | Owner of the open event | Admin who is not the owner |
|---|---|---|
| All Events card | normal | extra "View only" chip and "by \<owner\>" line |
| `aevent` | Edit chip, Registrations row, Event Report, Manage Organizers, 4 stat tiles, Close Event (hidden once closed) | "View only" chip, note "Created by … Only they can edit this event", Event Report and stat tiles only |
| `regs` | full list and Shortlist Teams | `lockedTab`: "Registrations are private" (animated lock art) |
| `live` | full | `lockedTab`: "Live is private" |
| `orgs` / `access` | full | lock screen "Only \<owner\> can manage organizers" |
| `create` (Edit sheet) | full; Manage Organizers row | "Manage Organizers" shown as a locked row (`ownLock`) |
| Announcements | **New** button, `SH.post` | New is blocked with the toast "View only · created by …" |
| `amsgs` | Help Desk and Organizers rows active, unread badge | both rows locked (`msgLock` toast), Announcements readable |
| Mutating actions (`OWN_MUT`: advance, eliminate, flag, shortlist, close, etc.) | run | toast "View only · created by \<owner\>" |
| Event Space (read-only variant via the Live eye button) | available | not reachable (Live is locked) |

Organizer accounts (Divya: Nexus Hack + Green Code; Kiran: Nexus Hack) follow the owner column but without Manage Organizers or Close ownership, and become read-only (`orgGate`) once the event is closed.

### 10.4 Consequence for the earlier ambiguities
- The Finalize gap (§9 item 1) is unchanged for every admin account, because it depends on code reachability, not ownership. Nexus Hack is the only seed event where finalizing is still pending, which is why the gap shows up there.
- None of the per-account states above changes the screen count or the overlay counts; they are states of existing screens.
