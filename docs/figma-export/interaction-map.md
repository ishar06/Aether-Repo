# Orbit Interaction Map

Extracted from `index.html` and `iPad.html` via `data-act` and `data-n` attributes plus JS action handlers.  
Every interactive element, its trigger, and its destination are listed here.

---

## Conventions

| Symbol | Meaning |
|--------|---------|
| → screen | Pushes a new screen onto the navigation stack |
| ← back | Pops the top screen off the stack |
| ↑ sheet | Opens a bottom sheet overlay |
| ✕ close | Dismisses the current sheet |
| alert | Opens a centred alert dialog |
| toast | Shows a dismissing toast notification |
| inline | Updates state within the current screen (no navigation) |

---

## 1. Global Navigation

### Bottom Tab Bar (iPhone) / Sidebar (iPad)

| Element | Action | Destination |
|---------|--------|-------------|
| Tab: Events (Participant) | `tab` → `events` | Events List screen |
| Tab: Event Space (Participant) | `tab` → `space` | Event Space screen |
| Tab: Messages (Participant) | `tab` → `msgs` | Messages Hub screen |
| Tab: Home (Admin/Org) | `tab` → `ahome` | Org/Admin Home screen |
| Tab: Live (Admin/Org) | `tab` → `live` | Live Scoring screen |
| Tab: Messages (Admin/Org) | `tab` → `amsgs` | Organizer Messages Hub |
| iPad sidebar: collapse/expand | `sbtn` | Toggles `.app.rail` class (sidebar ↔ rail) |

### Top Bar Buttons

| Element | Action | Destination |
|---------|--------|-------------|
| Back button (`←`) | `back` | ← Pops current screen |
| Account / avatar button | `sheet` → `profile` | ↑ Profile sheet |
| Notifications button | `sheet` → `notifs` | ↑ Notifications sheet |

---

## 2. Auth Flow

### Landing Screen

| Element | Action | Destination |
|---------|--------|-------------|
| "Sign in with Apple" button | `auApple` → `sheet` → `apple` | ↑ Apple ID picker sheet |
| "Or continue with email" / "Get started" | `sheet` → `roles` | ↑ Choose Your Role sheet |

### Choose Role Sheet (`SH.roles`)

| Element | Action | Destination |
|---------|--------|-------------|
| Participant row | `authOpen` data-r="p" | ↑ Auth sheet (Participant start step) |
| Organizer row | `authOpen` data-r="o" | ↑ Auth sheet (Organizer start step) |
| Admin row | `authOpen` data-r="a" | ↑ Auth sheet (Admin start step) |

### Sign in with Apple Sheet (`SH.apple`)

| Element | Action | Destination |
|---------|--------|-------------|
| Apple ID row | `appleGo` data-i | Authenticates + enters app |

### Auth Sheet (`SH.auth`) — All Steps

| Element | Action | Destination |
|---------|--------|-------------|
| Sign In / Sign Up toggle | `auMode` | inline — switches mode |
| "Sign in with Apple" button (in auth) | `auApple` | ↑ Apple sheet |
| Email / Password fields | data-bind `au.*` | inline — updates form state |
| Show/hide password eye | `auPw` | inline — toggles input type |
| Primary CTA ("Sign In" / "Continue") | `auNext` | inline — advances step or completes auth |
| Back button | `auBack` | inline — pops auth history stack |
| Demo login shortcut | `auDemoIn` | inline — fills demo credentials |
| OTP digit input | data-otp | inline — updates digit display |
| "Resend code" button | `auResend` | inline — resets timer, resends |
| Institute code field (admin) | data-bind `au.code` | inline — looks up institute name |
| "Join Event" CTA (organizer) | `auNext` | Validates event ID/pw, enters org role |
| "Demo" event shortcut | `auDemo` | inline — fills demo event credentials |

---

## 3. Participant — Events Tab

### Events List (`SC.events`)

| Element | Action | Destination |
|---------|--------|-------------|
| Filter pills (Upcoming / Registered) | `fltSt` / filter toggle | inline — switches event list view |
| Event card / row | `evOpen` or `push` → `details` | → Event Details screen |
| Account button | `sheet` → `profile` | ↑ Profile sheet |

### Event Details (`SC.details`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← Events List |
| "Register" CTA | `goReg` → push `register` | → Register screen |
| "View Pass" button | `push` → `pass` | → My Pass screen |
| "Edit Team" button | `sheet` → `editTeam` | ↑ Edit Team sheet |
| Event Space link (if registered) | `push` → `space` | → Event Space screen |

### Register (`SC.register`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← Event Details |
| Department field (tap) | `sheet` → `deptPick` | ↑ Department picker sheet |
| "Add Member" / invite buttons | `sheet` → `addMember` | ↑ Add Member sheet |
| Remove member | `memDel` | inline — removes member from form |
| "Register" CTA | `submitReg` | inline → success + push `pass` |

### My Pass (`SC.pass`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← previous screen |
| Stage dot (tap) | `sheet` → `stageInfo` | ↑ Stage Info sheet |
| "View Result" (if closed) | `push` → `result` | → Result screen |

### Result (`SC.result`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← My Pass |

---

## 4. Participant — Event Space Tab

### Event Space (`SC.space`)

| Element | Action | Destination |
|---------|--------|-------------|
| Stage selector pill | `step` | inline — switches visible stage |
| Stage info button | `sheet` → `stageInfo` | ↑ Stage Info sheet |
| "Submit Project" CTA | `sheet` → `submit` | ↑ Submit sheet |
| "My Pass" button | `push` → `pass` | → My Pass screen |
| "Add to Calendar" | `sheet` → `calendar` | ↑ Calendar sheet |

### Submit Sheet (`SH.submit`)

| Element | Action | Destination |
|---------|--------|-------------|
| Submission type tile (link / file / etc.) | `subType` | inline — selects type |
| Link input | data-bind | inline |
| File picker | `pickFile` | opens file browser |
| "Submit" CTA | `submitProj` | ✕ sheet + toast |
| "Re-submit" link | `subAgain` | inline — resets form |

### Stage Info Sheet (`SH.stageInfo`)

| Element | Action | Destination |
|---------|--------|-------------|
| Close button | `closeSheet` | ✕ sheet |

### Add to Calendar Sheet (`SH.calendar`)

| Element | Action | Destination |
|---------|--------|-------------|
| Stage toggle (`.sw`) | `calT` data-i | inline — toggles stage selection |
| "Add to Calendar" button | `calAdd` | ✕ sheet + system calendar action |

---

## 5. Participant — Messages Tab

### Messages Hub (`SC.msgs`)

| Element | Action | Destination |
|---------|--------|-------------|
| Team Chat tile | `push` → `chat` / `msgTeam` | → Team Chat screen |
| Organizer Chat tile | `push` → `orgchat` | → Organizer Chat screen |
| Help Desk tile | `push` → `helpdesk` | → Help Desk screen |
| Community tile | `push` → `community` | → Community Feed screen |
| Announcements tile | `push` → `announcements` | → Announcements screen |

### Team Chat (`SC.chat`) / Organizer Chat (`SC.orgchat`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← Messages Hub |
| Message input | data-bind | inline |
| Send button | `sendChat` | inline — adds message to thread |
| Lock button (if locked) | `msgLock` | toast (locked info) |

### Help Desk (`SC.helpdesk`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← Messages Hub |
| Topic picker buttons | `hTopic` | inline — sets topic |
| Segment (open/closed) | `hSeg` | inline — switches thread list |
| Search bar | `hQ` | inline — filters threads |
| Thread row (organizer inbox) | `push` → `helpthread` | → Help Thread screen |
| Message input | data-bind | inline |
| Send button | `sendChat` | inline — adds to thread |

### Community Feed (`SC.community`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← Messages Hub |
| Like button on post | `likePost` | inline — toggles like |
| Delete button (own post) | `postDel` | inline — removes post |
| FAB "New Post" (admin/org) | `sheet` → `post` | ↑ New Announcement/Post sheet |

### Announcements (`SC.announcements`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← Messages Hub |
| Active / Archive segment | `annSeg` | inline — switches list |
| Announcement card (tap) | `annOpen` | inline — expands / marks read |
| Filter button | `sheet` → `annFilter` | ↑ Announcement filter sheet |
| "New" button (admin/org) | `sheet` → `post` | ↑ New Announcement sheet |

### New Announcement Sheet (`SH.post`)

| Element | Action | Destination |
|---------|--------|-------------|
| Type chip (General / Alert / Pin) | `annType` | inline |
| Priority toggle | inline toggle | inline |
| Title / Body fields | data-bind | inline |
| "Post" CTA | `publish` | ✕ sheet + inline list update |

### Announcement Filter Sheet (`SH.annFilter`)

| Element | Action | Destination |
|---------|--------|-------------|
| Filter toggles | `afTog` | inline |
| Reset | `afReset` | inline |
| "Apply" | `afApply` | ✕ sheet + inline list refresh |

---

## 6. Organizer Screens

### Organizer Home (`SC.ahome`)

| Element | Action | Destination |
|---------|--------|-------------|
| Event row | `evOpen` → push `aevent` | → Organizer Event Overview |
| Account button | `sheet` → `profile` | ↑ Profile sheet |

### Organizer Event Overview (`SC.aevent`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← Org Home |
| "Open Registrations" CTA | `openRegs` | inline → state change |
| "Open Event" (start) | `openEvent` | inline → opens event |
| Report tile | `push` → `report` | → Report screen |
| Registrations tile | `push` → `regs` | → Registrations screen |
| Shortlist tile | `push` → `shortlist` | → Shortlist screen |
| Access / Organizers tile | `push` → `orgs` | → Access screen |
| Close event tile | `push` → `close` | → Close Event screen |
| Edit event button | `sheet` → `editEvent` | ↑ Edit Event tall sheet |
| Stage builder button | `push` → `stages` | → Stage Builder list |
| "Add to Calendar" | `sheet` → `calendar` | ↑ Calendar sheet |
| Manage submissions (org) | `sheet` → `manage` | ↑ Manage sheet |
| QR check-in button | `sheet` → `qr` | ↑ QR check-in sheet |

### Report Screen (`SC.report`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← Org Event Overview |
| Team row (tap) | `tdOpen` → `sheet` → `teamD` | ↑ Team Detail sheet |

### Access / Organizers (`SC.access`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← Org Event Overview |
| Copy ID/PW button | `accCopy` | toast — copies to clipboard |
| "Revoke access" toggle | `accRev` | alert → inline |
| Member revoke/restore button | `accMem` data-i | alert → inline |
| "Add Organizer" button | `sheet` → `addOrg` | ↑ Add Organizer sheet |

### Registrations (`SC.regs`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← Org Event Overview |
| Filter chips | `rgOpen` / filter toggle | inline — switches filter |
| Team row | `sheet` → `regDetail` | ↑ Reg Detail sheet |
| Red-flag button | `flagT` | alert (with reason field) → inline |
| Eliminate button (from list) | `elimReg` | alert → inline |

### Shortlist (`SC.shortlist`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← Org Event Overview |
| Team select checkbox/tile | `accT` | inline — toggles selection |
| "Finalize" CTA | `finalize` | alert → ↑ Send/Progress sheet |
| "Send shortlist" | `sheet` → `send` | ↑ Finalize Progress sheet |

### Close Event (`SC.close`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← Org Event Overview |
| "Close Event" button | `closeEvent` | alert → inline → closed state |

### New Event Wizard (`SC.newev`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← previous step or admin home |
| "Next" button | `ndNext` | inline — advances step |
| Step indicator dots | non-interactive (display only) | — |
| Poster picker | `ndOpen` / `stPoster` | inline — opens image picker |
| Domain chip | `ndTog` data-k | inline — toggles domain |
| Prize add | `ndAddPrize` | inline — adds prize row |
| Prize delete | `ndDelPrize` | inline — removes prize row |
| Domain add | `ndAddDom` | inline — custom domain |
| "Publish" CTA | `ndCreate` / `ndGo` | alert (access creds) → ← back to home |
| "Save Draft" | `ndCreate` data-draft | toast → ← back |

### Stage Builder List (`SC.stages`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← new event wizard / org overview |
| Stage row | `stOpen` data-i | → Stage Detail screen |
| "Add Stage" button | `stAdd` | inline — appends new stage |
| "Save" button | `stSave` | alert if invalid · toast + ← |

### Stage Detail (`SC.stage`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `stBack` | ← Stage Builder list |
| Name input | data-bind | inline |
| Deadline picker | data-bind | inline |
| Criteria add | `crAdd` | inline |
| Criteria delete | `crDel` | inline |
| Poster upload | `stUpPoster` / `upPoster` | inline — file picker |
| Delete stage button | `stDelHere` | alert → ← Stage Builder list |
| Delete via swipe/button | `stDelv` | inline |

### QR Check-in Sheet (`SH.qr`)

| Element | Action | Destination |
|---------|--------|-------------|
| Team tile (found state) | `scanT` | inline — selects team |
| "Check In" / "Shortlist" CTA | `markIn` | inline → done phase |
| Close button | `closeSheet` | ✕ sheet |

### Manage Submissions Sheet (`SH.manage`)

| Element | Action | Destination |
|---------|--------|-------------|
| Stage selector | `mgOpen` | inline |
| Search bar | inline | inline — filters |
| Team row | `tdOpen` → `sheet` → `teamD` | ↑ Team Detail (nested sheet) |

### Manage Registrations Sheet (`SH.regManage`)

| Element | Action | Destination |
|---------|--------|-------------|
| Filter tabs | `rgOpen` | inline |
| Search | inline | inline |
| Team row | ↑ `regDetail` | ↑ nested sheet |

### Registration Detail Sheet (`SH.regDetail`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button (sheet back) | inline sheet stack pop | ← previous sheet |
| Red-flag button | `flagT` | alert → inline |
| Eliminate button | `elimReg` | alert → inline |
| File link | `tdFile` | opens URL |

### Team Detail Sheet (`SH.teamD`)

| Element | Action | Destination |
|---------|--------|-------------|
| Stage selector pills | inline | inline — switches stage view |
| Submission file/link | `tdFile` | opens URL |
| Message team button | `tdMsg` | → Org chat / help thread |
| Advance button | `advance` | alert (optional note) → inline |
| Eliminate button | `eliminate` | alert (optional reason) → inline |

### Add Organizer Sheet (`SH.addOrg`)

| Element | Action | Destination |
|---------|--------|-------------|
| Search/name input | data-bind | inline — previews match |
| "Add" CTA | `orgAdd` → `orgSave` | ✕ sheet + toast |

### Finalize Shortlist Sheet (`SH.send`)

| Element | Action | Destination |
|---------|--------|-------------|
| (Auto-runs) progress animation | — | inline — steps through check/QR/alerts/emails/deadline |
| Done state "Close" | `closeSheet` | ✕ sheet |

---

## 7. Live Scoring (Shared: Admin + Org)

### Live Screen (`SC.live`)

| Element | Action | Destination |
|---------|--------|-------------|
| Stage selector pill | `fsStep` | inline — switches stage |
| Filter bar chips | `fltSt` | inline — All / Active / Done / Elim |
| Search input | `fltQ` | inline |
| Team card (tap) | `tdOpen` → push `team` | → Team Detail screen |
| "Advance" quick-action | `advance` | alert → inline |
| "Eliminate" quick-action | `eliminate` | alert → inline |
| "Flag" quick-action | `flagT` | alert → inline |
| Filter reset | `fsClr` / `fsReset` | inline |

### Team Detail Screen (`SC.team`)

| Element | Action | Destination |
|---------|--------|-------------|
| Back button | `back` | ← Live screen |
| Stage selector | inline | inline |
| Submission link/file | `tdFile` | opens URL |
| "Advance" button | `advance` | alert → inline |
| "Eliminate" button | `eliminate` | alert → inline |
| "Message" button | `tdMsg` → `msgGo` | → Org chat screen |

---

## 8. Admin Home

### Admin Event List (`SC.ahome`)

| Element | Action | Destination |
|---------|--------|-------------|
| Event row | `aOpen` → push `aevent` | → Admin Event Overview |
| "New Event" button | `ndOpen` → push `newev` | → New Event Wizard |
| Account button | `sheet` → `profile` | ↑ Profile sheet |

---

## 9. Profile Sheet (`SH.profile`)

| Element | Action | Destination |
|---------|--------|-------------|
| Close button | `closeSheet` | ✕ sheet |
| Phone number input | `data-phone` | inline — edits phone |
| Department chips | `pfPick` | inline — selects dept |
| "Other" chip | `pfOther` | inline — shows custom field |
| Admission year chips | `pfAdm` | inline |
| "Save" profile CTA | `pfSave` | inline → profile complete |
| "Delete account" button | `delAcct` | alert → sign out + landing |
| "Sign out" button | `signOut` | inline → landing screen |
| Notification toggle | inline | inline |
| Event row (org/recent events) | `evOpen` / `goEv` | ✕ sheet → Event Details |
| Accept invite | `invYes` data-id | inline — joins team |
| Decline invite | `invNo` data-id | inline — declines |

---

## 10. Notifications Sheet (`SH.notifs`)

| Element | Action | Destination |
|---------|--------|-------------|
| Notification row (tappable) | `ntGo` data-id | ✕ sheet → navigates to destination screen |
| Accept invite (inline action) | `invYes` | inline |
| Decline invite (inline action) | `invNo` | inline |
| Close button | `closeSheet` | ✕ sheet |

---

## 11. Edit Team Sheet (`SH.editTeam`)

| Element | Action | Destination |
|---------|--------|-------------|
| Close button | `closeSheet` | ✕ sheet |
| "Add Member" button | `tmAdd` → `sheet` → `addMember` | ↑ Add Member sheet (nested) |
| "Replace" button | `tmRep` | ↑ Add Member sheet (replace mode) |
| "Remove" button | `tmRem` | inline — removes member |
| "Cancel invite" | `tmRem` data-pend | inline |

---

## 12. Add Member Sheet (`SH.addMember`)

| Element | Action | Destination |
|---------|--------|-------------|
| Suggested mate chip | `mmFill` data-i | inline — fills form with mate |
| Name / Roll / Email inputs | data-bind `mm.*` | inline |
| Back button (replace mode) | `mmBack` | ✕ nested sheet |
| "Send Invite" CTA | `mmSave` | ✕ sheet + toast |

---

## 13. Activity Log Sheet (`SH.logs`)

| Element | Action | Destination |
|---------|--------|-------------|
| Filter chip | `lgF` | inline — filters log type |
| "Load more" button | `lgMore` | inline — expands log |
| Close button | `closeSheet` | ✕ sheet |
| Log row (tappable) | `lgOpen` | → relevant screen |

---

## 14. Department Picker Sheet (`SH.deptPick`)

| Element | Action | Destination |
|---------|--------|-------------|
| Department row | `pickDept` data-v | ✕ sheet + inline form update |

---

## 15. Filters Sheet (`SH.filters`)

| Element | Action | Destination |
|---------|--------|-------------|
| Filter toggles | `fltMe` / `fltTog` | inline |
| "Apply" button | `fltApply` | ✕ sheet + inline list refresh |
| "Back" (sub-filter) | `fltBack` | inline — pops filter stack |
| "Reset" | `afReset` | inline |

---

## 16. Alert Dialog Interactions

All alerts share this pattern:

| Button | Action |
|--------|--------|
| Cancel / No button | `alertK` data-k="0" → dismisses |
| Confirm button | `alertK` data-k="1" → executes callback |
| Input field (some alerts) | `askin` — text captured by callback |

---

## Summary of Interactive Element Counts

| Category | Count |
|----------|-------|
| Tab / sidebar navigation targets | 6 |
| Unique `data-act` handler types | 115+ |
| Push navigation actions (`push` / `evOpen` / `ndOpen` etc.) | ~30 |
| Sheet open actions (`sheet` data-n) | 24 |
| Alert dialog types | 17 |
| Inline state-change actions | ~40 |
| Form inputs / data-bind fields | ~25 |
| **Total distinct interactive element types** | **~130** |
