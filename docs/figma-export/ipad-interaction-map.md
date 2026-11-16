# Orbit iPad — Interaction Map

Source: `docs/iPad.html`. Static analysis of the markup and JavaScript; no runtime crawl.
Companion files: `ipad-screen-inventory.md`, `ipad-design-system.md`, `ipad-asset-inventory.md`.

---

## 1. How interaction works

- **One global click handler** (`document.addEventListener('click', …)`) finds the nearest `[data-act]` element and calls `A[data-act](dataset, element)`. Elements with class `.dis` are ignored.
- **Ownership matters.** An event is owned by exactly one admin account (Nexus Hack 26 → Ishar, Green Code 25 → Meera Iyer, Byte Fest 25 → Arjun Nair). Every other admin is a non-owner for that event. See `ipad-screen-inventory.md` §10 for the account × event matrix.
- **Gates run before the action** (admin/organizer permission layer):
  - A non-owner Admin (`role a`, no `V.org`, viewing another admin's event) is blocked from mutating actions (`OWN_MUT`), from `sheet post`, from `push close|access|orgs`, and from `ownLock` rows. Result: lock toast "View only · created by …" or "Only … can manage organizers".
  - An Organizer (`V.org`) passes through `orgGate()`. It re-validates access on every action; if revoked it shows the "Access revoked" alert and removes the event. If the event is closed and the action is in `MUT`, it toasts "Event closed · read-only".
- **Native handlers outside `data-act`:** `input`/`change` (two-way binding via `data-bind`, search boxes, date pickers, range sliders, OTP boxes, phone, file inputs), `keydown` (Enter on `[role=button]`, chat input, post input, auth sheet, add-member sheet), `pointer*` (sheet drag-to-dismiss on `.grab`), `scroll` (poster carousel dots), sidebar toggle `#sbtn`, and a click-outside handler that closes open dropdowns.
- **Re-render model:** `refresh(el)` re-runs a screen's builder and swaps its HTML only if the output changed (scroll position preserved). `refreshAll()` refreshes every stacked screen; `refreshSheet()` does the same for the open sheet.

---

## 2. Navigation engine (iPad-specific)

| Function | Behaviour on iPad |
|---|---|
| `switchTab(t, reset)` | Cross-fades panes, highlights the sidebar item. If `reset` or the same tab is tapped, pops the stack back to the root. For split tabs, refreshes the root column too. Calls `syncTabs()` (badges, split selection, sidebar account). |
| `push(name, params)` | Builds the screen and appends it to the **current host**. **Split tab:** the first push goes to `.col2` with a fade (no slide, and the back button is hidden). Later pushes slide in over the previous `.col2` screen (`.behind` shifts the one beneath −26%, dimmed). If a detail is open and the tap came from the `.col1` root, the stack is popped to the root before pushing, so selecting a different row **replaces** the detail. **Non-split tab:** slides over the root across the full `#main` pane. |
| `pop()` / `data-act=back` | Pops one screen, un-dims the one beneath, slides the popped screen out (420 ms). No-op at the root. |
| `sheet(name, params, mode)` | Opens `#sheets` (scrim + `#sheetbox`). `mode===true` pushes the current sheet onto `SS` (slide from right). `mode==='back'` slides from the left. No mode clears the sheet stack. |
| `closeSheet(force)` | If the sheet has `nodismiss` and not forced: ignored. Else if the sheet stack is non-empty and not forced: **pops to the previous sheet**. Else closes. Closing `editEvent` also tears down its nested stack; closing `qr` stops the camera. |
| `ask(opts, cb)` | Opens the alert; `alertK k=0` cancels, `k=1` confirms (`req` fields must be non-empty, or the field outlines red). |
| `dest(n)` | Notification deep-link: `pass` → Events tab reset, push `details` then `pass`; `space` → Event Space tab; `ann` → Messages + `announcements`; `help` → Messages + `helpdesk`; `result` → Event Space + `result`. |

**Dismissal paths for sheets:** scrim tap (`data-act=closeSheet` on `.scrim`), header Cancel/Done buttons, drag-down on the grab handle (more than 90 px), the QR scanner's grab handle. Sheets marked `nodismiss` (`apple` while busy, `send` while running) ignore all of these.

**Sidebar:** `data-act=tab` closes any sheet (forced) and switches tabs. The `#sbtn` button toggles the rail (72 px) / expanded (268 px) state and shifts `#main` by CSS transition (380 ms). The footer account button opens `SH.profile`.

---

## 3. Original navigation sequences

### 3.1 Cold start → role
1. Landing → **Get Started** → `SH.roles` → pick a role → `SH.auth` (`start`).
2. `start` → success → `enterSession()` → `startRole()` → sidebar + panes; first tab opens.
3. Alternative: Landing → **Sign in with Apple** → `SH.apple` → pick an ID → either direct session (existing account) or `SH.auth` at `profile` (new).
4. Sign-up: `profile` → `otp` (banner shows code) → `linked` → session. Organizer with no remaining events → `event` step (Event ID + password) → `auJoin`.

### 3.2 Participant
1. **Events** (col1) → tap card → `details` (col2).
2. `details` → **Register Team** → `register` (col2, slide) → **Submit Registration** → (busy) → registered state; `goEv` then lands on `details` → `pass`.
3. `details` → **View My Pass** → `pass`.
4. **Event Space** → pick event (picker sheet) → roadmap row → `SH.stageInfo`; **Submit Project** → `SH.submit` → Submitted → Submit Another; **Result** row → `result`; **Add to Calendar** → `SH.calendar` → download `.ics`; **Remind Me** switch toggles banner reminders.
5. **Messages** → pick event → conversation row → `chat` / `helpdesk` / `community` / `announcements` (col2). Search results jump to a message (`msgGo`, scrolled and flashed).
6. Team leader: Pass → team card **Edit** → `SH.editTeam` → **Add / Replace** → `SH.addMember` → Save → back to `editTeam`.

### 3.3 Admin
1. **Home** (col1) → **Create Event** FAB → `newev` (col2) → steps 1–4 → **Save Draft** or **Publish** → alert "Organizer access ready" → event in list.
2. **Home** → tap event card → `aevent` (col2) → Registrations → `regs` → **Shortlist Teams** → `SH.regManage` → team → `SH.regDetail` → **Shortlist / Red-flag / Eliminate**.
3. `aevent` → **Edit** → `SH.editEvent` (nested stack: `create` → `stages` → `stage`; `orgs`).
4. `aevent` → **Manage Organizers** → `orgs` → **Add Organizer** → `SH.addOrg`.
5. `aevent` → **Event Report** → `report`; **Close Event** → `close` → alert → closed.
6. **Live** → pick event → **Scan** → `SH.qr`; **Manage Teams** → `SH.manage` → team → `SH.teamD`; **View Log** → `SH.logs`; eye button → read-only `space`.
7. **Messages** → Announcements → **New** → `SH.post` → Broadcast; Help Desk → `inbox` → `helpthread`; Organizers → `orgchat`.
8. Finalize path: `SH.regManage` has no Finalize control; `finalize` (alert → `SH.send` → Open Live Monitor → `goLive`) is wired only from the unreachable `SC.shortlist` (see Ambiguities).

### 3.4 Organizer
Same as Admin minus: All Events (scoped to the organizer's events), FAB, Manage Organizers, Close Event owner controls, Edit sections that are locked. `joinOpen` (from the event picker "Join an event") reopens `SH.auth` at the `event` step.

---

## 4. Action → destination table

`data-act` values with their effect. Actions marked † are invoked through a parameterised helper (`usgH`, `sw`, `dd`, `posterPick`, …) rather than a literal `data-act="…"`.

### 4.1 Navigation
| Action | Effect / destination |
|---|---|
| `tab` | Close sheet, switch sidebar tab (reset to root if the same tab) |
| `back` | `pop()` |
| `push` | `push(data-n, {id, ro})`; `report` with no teams → alert "No Report Yet" |
| `sheet` | `sheet(data-n, {id})` |
| `closeSheet` | Pop sheet stack or close |
| `closeAll` | Force close all sheets |
| `openEvent` | push `details` |
| `openReg` | push `register` (or toast + `profile` sheet if profile incomplete; toast if ineligible) |
| `goReg` | Close sheet → Events tab reset |
| `goEv` | Close sheet → participant: Events tab, push `details` + `pass`; admin: Home tab, push `aevent` |
| `openRegs` | push `regs` |
| `aOpen` | Set current event, push `aevent` |
| `goLive` | Close sheet → Live tab reset |
| `evOpen` | `SH.evSwitch` |
| `setEv`† | Close sheet; select event, or route to `joinOpen` / `ndOpen` for the special "join" and "new" rows |
| `msgGo` | Push target screen, scroll to and flash the matching message |
| `msgTeam` / `hOpen` | push `helpthread` |
| `tdOpen` / `mgTeam`† | `SH.teamD` (slide-forward) |
| `rgTeam` | `SH.regDetail` (slide-forward) |
| `tdMsg` | Close sheet, then `msgTeam` |
| `mgOpen` / `rgOpen` / `lgOpen` / `annOpen` / `fltOpen` / `memAdd` / `orgAdd` / `tmAdd` / `tmRep`† | Open `manage` / `regManage` / `logs` / `annFilter` / `filters` / `addMember` / `addOrg` / `addMember` / `addMember` |
| `stOpen` | push `stage` |
| `stInfo` | `SH.stageInfo` |
| `aEdit` | Set event, `SH.editEvent` |
| `ndOpen` | Reset wizard state, push `newev` |
| `ntGo` | From notifications: close sheet and deep-link (see `dest`) |
| `bnTap` | Dismiss banner and `dest()` |
| `joinOpen` | `SH.auth` at `event` step |
| `authOpen` | `SH.auth` at `start` or `profile` (slides forward when coming from `roles`) |
| `auBack` | Previous auth step, or back to `roles` |
| `auApple` / `appleGo` | `SH.apple` / start Apple session |
| `signOut` | Close sheet → Landing |

### 4.2 Forms, state toggles, inline controls
| Action | Effect |
|---|---|
| `accT` | Toggle an accordion (state remembered in `S.open`) |
| `ddT` | Open/close a dropdown popover; closes the others |
| `tdStage`† / `mgStage`† | Change the stage shown in `teamD` / `manage` |
| `hSeg` / `annSeg` / `regF`† / `rgF`† / `liveF`† | Segmented-control filters |
| `fltSt` / `fltMe` / `fltQ` / `fltBack`† | Status chips, eligibility switch, clear search, return from filters to picker |
| `fsReset` / `fsTog` / `fsStep` / `fsClr` / `fsMe`† / `fltApply` | Filter sheet controls and Apply |
| `afTog` / `afReset` / `afApply` | Announcement filter controls |
| `annType` / `annPri`† / `annUntil`† / `broadcast` | Announcement composer |
| `sendChat` / `sendPost` / `likePost` / `sendHelp` | Messaging |
| `hQ` / `lQ` / `msgQ` / `annQ` | Clear search fields |
| `hTopic` | Help-desk topic chip |
| `regDept` / `pickDept` | Department selection |
| `memDel` / `mmFill` / `mmSave` / `mmBack` / `tmRem`† | Registration and team members |
| `submitReg` | Validate and register (busy 650 ms) |
| `subType` / `pickFile` / `submitProj` / `subAgain` | Project submission |
| `calT`† / `calAdd` | Calendar switches and export |
| `togRemind` / `togRemind2`† / `togNotif`† | Reminder and notification switches |
| `pfPick` / `pfOther` / `pfAdm` / `pfSave` | Profile completion |
| `pwShow` / `accCopy` / `accRev` / `accMem` / `orgSave` | Organizer access |
| `ndBack` / `ndGo` / `ndNext` / `ndTog` / `ndAddDom` / `ndAddPrize` / `ndDelPrize` / `ndPoster`† / `ndUpPoster`† / `ndCreate` | Event wizard |
| `togDraft` / `step` / `pickPoster`† / `upPoster`† / `postDel` / `publish` | Edit-event form |
| `stAdd` / `stBack` / `stDelHere` / `crAdd` / `crDel` / `stDelv` / `stPoster` / `stUpPoster` / `stSave` | Stage builder |
| `auMode`† / `auPw` / `auDemoIn` / `auDemo` / `auResend` / `auNext` | Auth form |

### 4.3 Decisions (open an alert)
`advance`, `eliminate`, `elimReg`, `flagT`, `shT`, `finalize`, `closeEvent`, `accRev`, `accMem`, `tmRem`, `stDelHere`, `delAcct`, and `push report` when empty. `alertK` resolves them.

### 4.4 Check-in
`scanT` (select a team's QR chip → scanning → found after 1.1 s) → `markIn` (marks arrived, "done" check for 1.4 s, back to idle).

### 4.5 Toast-only / blocked
`swLock`, `msgLock`, `ownLock`, `tdFile` (toasts the file name), `selT` (toggles selection in the unreachable `shortlist` screen).

---

## 5. Interactive element inventory by type

| Type | Where | Count / notes |
|---|---|---|
| Sidebar tab buttons | `#tabs` | 3 per role (6 distinct) |
| Sidebar collapse button | `#sbtn` | 1 |
| Sidebar account button | `.acct` | 1 |
| Top-bar glass buttons | back, bell, A/O/R account, filter, eye, "more", Scan, New | per screen |
| Primary buttons | `.btn` / `.lbtn` / `.fab` / `.subcta` / `.dnb` / `.mg-done` | CTA row per form |
| Chips (tappable) | `.chip[data-act]` / `button.chip` / `.chip.go` / `.chip.edit` | filters, toggles, department/year pickers |
| Segmented controls (`.usg`) | inbox, announcements, regs, live, regManage, post | 7 template sites |
| Switches (`.sw`) | calendar, filter "Eligible for me", notifications, reminders | 5 distinct actions |
| Dropdown popovers (`.dd`) | `mgs` (Manage Teams stage), `tds` (Team Details stage) | 2 |
| Accordions | Create form (`c-po`, `c-dom`, `c-el`, `c-pr`, `c-lk`, `c-dl`, `c-sz`), Details (`d-el`, `d-dl`, `d-dom`, `d-pr`), Team roadmap (`tdrm-<id>`), Team members (`tm-<id>`) | 13 keys |
| Rows / cards that navigate | `.lrow.tap`, `.conv`, `.card.tap`, `.tc.tap`, `.tl.tap`, `.nlr`, `.pcard` | event cards, conversations, team rows |
| Text inputs / textareas | 47 `<input>` + 5 `<textarea>` in templates (including 4 date/datetime, 2 range, OTP, hidden file inputs `#fposter`, `#fsub`) | forms, search, chat |
| Links (`<a>`) | mailto, tel, external team / submission links | 4 template sites |
| Role-button elements (keyboard-activatable) | `[role=button][tabindex]` | 15 template sites |
| Drag / swipe | Sheet grab handle (drag down > 90 px); poster carousel scroll-snap | 2 |
| Auto events | Count-down timers, count-up numbers, cloud and star animations, banner timers | non-interactive |

**Totals:** 194 `data-act` template sites; 158 distinct action handlers (`A.*`): 127 appear as a literal `data-act` value in markup, 27 are invoked through helpers (†), 3 are orphans (`deptOpen`, `openPass`, `newEvent`) and 1 is an internal helper (`sendHelp`, called by `sendChat`). `ownLock` appears in markup but is handled by the gate, not by `A`.

---

## 6. Navigation destinations (total: 28 screens + 24 sheets + overlays)

**Screens reachable by `push`/tab:** `events`, `details`, `register`, `pass`, `space`, `result`, `msgs`, `chat`, `orgchat`, `inbox`, `helpthread`, `helpdesk`, `community`, `announcements`, `ahome`, `aevent`, `report`, `orgs` (alias `access`), `close`, `newev`, `stages`, `stage`, `regs`, `live`, `team`, `amsgs` (+ `create` inside `editEvent`). That is 27 reachable screens + Landing = 28 destinations; `shortlist` is unreachable.

**Sheets:** 24 types (one, `deptPick`, unreachable → 23 reachable).

**Overlay surface count:** 24 sheets, 18 alert call-sites, 3 banner variants, 1 toast, 2 dropdowns.

---

## 7. Ambiguities / incomplete flows that affect the map

1. **Finalize has no reachable trigger.** `A.finalize` (alert → progress sheet `send` → `goLive`) is only exposed by `SC.shortlist`, which nothing pushes. `SH.regManage`'s Shortlist action issues passes team by team and does not finalize. Needs a product decision before this flow is drawn in Figma.
2. **`SC.team` is reachable only via the help-desk "more" button.** Live-grid taps open `SH.teamD` instead; two near-duplicate "team detail" surfaces exist.
3. **Split-view "back".** In `.col2` the first screen has no back button; deeper screens do. Re-tapping the same sidebar tab pops the stack to the root. The `.col2` placeholder is only restored when the stack is empty again.
4. **Dismiss rules for stacked sheets.** `closeSheet()` pops to the previous sheet whenever a stack exists, so the scrim tap does not always close. Only `closeAll` or a forced close does.
5. **Orphans:** `deptOpen` + `deptPick` + `pickDept`; `openPass`; `newEvent` (targets a non-existent `setup` tab).
6. **Event picker gate.** `space`, `msgs`, `amsgs` and `live` show a "pick an event" screen until an event has been selected (`S.picked`); `ahome` does not. Picker is a sheet (`evSwitch`) opened from the glass pill; selecting dismisses it.
7. **Camera / clipboard / download / file chooser** are real browser APIs and cannot be represented as static prototype links.
8. **Timed transitions:** Auth `busy` states (650 ms), Apple sign-in (1300 ms), finalize steps, scan found (1.1 s) and done (1.4 s), notification read marking (900 ms). These are auto-advancing steps with no tap.
