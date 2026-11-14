# Orbit Screen Inventory

Extracted from `index.html` (iPhone) and `iPad.html` (iPad).  
iPad uses an identical screen/JS stack as iPhone; device-specific notes cover layout differences.

---

## Conventions

- **Push screen** — rendered by an `SC.*` function, lives on a navigation stack inside a tab pane.
- **Sheet overlay** — rendered by an `SH.*` function, slides up from the bottom over any screen.
- **Alert dialog** — rendered by `ask()`, centred modal requiring a decision.
- **Inline state** — the same `SC.*` function renders visually different content depending on data state; listed as sub-states, not separate screens.

---

## 0. Pre-Auth — Landing

### 0a. Landing / Sign-in Gate
**Screen function:** `showSignIn()` (renders `.land` class screen)  
**Visible to:** unauthenticated users of any role  
**Content:** Orbit logo, tagline, "Sign in with Apple" button, "Or continue with email" link  
**iPad difference:** centred column (`.lstack` 400px wide), background is simpler radial gradient

---

## Role Selector

### 0b. Choose Your Role
**Sheet:** `SH.roles`  
**Triggered from:** landing (before auth), or demo mode  
**Content:** Radio-list of 3 roles with icon, name, and description:
- Participant — cap icon — "Find hackathons, register, submit your project and see your results."
- Organizer — orgrole icon — "Run an event: check in teams, review submissions and announce winners."
- Admin — adminrole icon — "Set up your institute, create hackathons and manage organizers."

---

## Auth Flow Screens (Sheet: `SH.auth`)

A single sheet that steps through 5 states via `S.au.step`:

| Step key | Content | Notes |
|----------|---------|-------|
| `start` | Sign in / Sign up toggle + email + password fields + demo shortcut | First step for all roles |
| `profile` | Full name + institute code (admin) OR roll number (participant) + institute email + personal email + password | Account creation details |
| `otp` | 4-digit code entry with digit-box UI + resend countdown + attempt counter | Email verification |
| `linked` | Success screen showing both emails confirmed with animated checkmark | No-dismiss, auto-advances |
| `event` | Event ID + Event password fields + demo shortcut | Organizer joining event only |

**Sub-state: Sign in with Apple (`SH.apple`)**  
Triggered from auth step 1 or landing "Sign in with Apple" button.  
Shows a list of demo Apple IDs with name + email. While busy: Face ID animation spinner.

---

## Participant Screens (Role `p`)

Participant tabs: **Events** · **Event Space** · **Messages**

### Tab 1 — Events

#### 1a. Events List
**Function:** `SC.events`  
**Route:** `events` (root of Events tab)  
**Content:** Large title bar "Events", account button (top-right), two event filter views (upcoming / registered) switchable via pill segments  
**States:**
- Empty state (no events): icon + prompt
- Events list: `.whc` event cards with poster, name, date, status chips, and open arrow

#### 1b. Event Details
**Function:** `SC.details`  
**Route:** pushed from event card (`push` action, `data-n="details"`)  
**Content:** Poster hero, event name, tracks/domain chips, date, team size, description, stats row (registered teams, stages, prize), CTA button  
**States:**
- Registration open (`canReg = true`, not registered): "Register" CTA
- Registered (`e.me` set): "View Pass" + "Edit Team" buttons
- Registration closed (not registered): disabled "Register" / info message
- Event closed/over: read-only result state

#### 1c. Register
**Function:** `SC.register`  
**Route:** pushed from event details  
**Content:** Multi-step form — team name, department picker, member invitation section, eligibility summary  
**States:**
- Incomplete form: disabled submit
- Form valid: active "Register" button
- Already registered: redirects to pass

#### 1d. My Pass
**Function:** `SC.pass`  
**Route:** pushed from event details or Messages tab  
**Content:** "My Pass" heading, event name, QR code ring animation, team member list, stage progress dots, status chip  
**States:**
- Active pass (shortlisted): QR code with ring, animated glow
- Not-shortlisted / pending: status chip only, no QR
- Event closed: result redirect prompt

#### 1e. Result
**Function:** `SC.result`  
**Route:** pushed from pass or via notification  
**Content:** Trophy icon, placement, stage summary, elimination notice  
**States:**
- No team (never registered): empty state with trophy icon
- Shortlisted / finalist: placement card
- Eliminated: eliminated-at-stage info
- Pending (event not finalized): "Results pending" state

---

### Tab 2 — Event Space

#### 2a. Event Space / Stage Map
**Function:** `SC.space`  
**Route:** `space` (root of Event Space tab), also `data-n="space"` push  
**Parameters:** `ro` (read-only flag)  
**Content:** Stage selector (top pill), active stage card, progress ring, team status, submission CTA  
**States:**
- Pre-event: locked/countdown view
- Active stage (progress < 100%): live stage content
- Stage complete (all teams done): done view
- Event closed: read-only results view
- No team registered: prompt to register

---

### Tab 3 — Messages

#### 3a. Messages Hub
**Function:** `SC.msgs`  
**Route:** `msgs` (root of Messages tab)  
**Content:** "Messages" title, tiles linking to: Team Chat, Organizer Chat (if in event), Help Desk, Community, Announcements  
**Badge logic:** Unread counts on tiles and tab badge

#### 3b. Team Chat
**Function:** `SC.chat`  
**Route:** pushed from msgs hub or directly  
**Content:** Scrollable message list (`.msgs`), team avatar + name in top bar, chat input bar  
**States:**
- No team: empty state
- Locked (event over): input bar disabled with lock icon

#### 3c. Organizer Chat
**Function:** `SC.orgchat`  
**Route:** pushed from msgs hub  
**Content:** Same layout as team chat; "Organizers" header with people icon  
**States:**
- Available only when registered for an event

#### 3d. Help Desk (Participant)
**Function:** `SC.helpdesk`  
**Route:** pushed from msgs hub  
**Content:** Topic picker (Doubt / Technical / Other), message input, thread list  
**States:**
- First visit: topic selection prompt
- Active thread: chat interface
- No team: empty state with shield icon

#### 3e. Help Desk Thread
**Function:** `SC.helpthread` (from organizer side)  
**Route:** pushed from inbox  
**Content:** Chat thread with specific team, header shows team name  

#### 3f. Help Desk Inbox (Organizer)
**Function:** `SC.inbox`  
**Route:** pushed from organizer messages  
**Content:** List of teams with open help threads, unread count badges  
**States:**
- Empty: empty state with bubble icon

#### 3g. Community Feed
**Function:** `SC.community`  
**Route:** pushed from msgs hub  
**Content:** Card-list of community posts (`.post`), like button, FAB for new post (organizers/admin only)

#### 3h. Announcements
**Function:** `SC.announcements`  
**Route:** pushed from msgs hub  
**Content:** Segmented by Active / Archived, announcement cards with priority chips, "New" button for admin/org  
**States:**
- Active announcements list
- Archived list (via segment switch)
- Empty states for each segment

---

## Organizer Screens (Role `o`)

Organizer tabs: **Home** (aevent) · **Live** · **Messages**

### Tab 1 — Home (Organizer)

#### 4a. Organizer Home
**Function:** `SC.ahome` (when org is set)  
**Route:** `home` / `ahome`  
**Content:** "My Events" title, list of events the organizer manages, account button  
**States:**
- No org assigned: empty / onboarding prompt

#### 4b. Event Overview (Organizer)
**Function:** `SC.aevent`  
**Route:** pushed from organizer home  
**Content:** Event poster, name, stat tiles (teams, check-ins, shortlisted, active actions), status chip, quick action buttons  
**States:**
- Pre-event (not open): "Open Registrations" CTA
- Registrations open: stat tiles + manage buttons
- Event running: live stats
- Event closed: read-only summary

#### 4c. Report
**Function:** `SC.report`  
**Route:** pushed from event overview  
**Content:** Stat grid (total teams, shortlisted, arrived, eliminated, finalists), stage breakdown table  
**States:**
- No teams registered yet: empty state + info message

#### 4d. Access / Organizers
**Function:** `SC.access` / `SC.orgs` (aliased: `SC.access = SC.orgs`)  
**Route:** pushed from event overview  
**Content:** Event ID + password display, copy button, revoke/restore toggle, member list with individual revoke/restore per organizer, "Add Organizer" button

#### 4e. Close Event
**Function:** `SC.close`  
**Route:** pushed from event overview  
**Content:** Confirmation summary (teams, arrivals, eliminations), "Close Event" destructive button

#### 4f. Registrations
**Function:** `SC.regs`  
**Route:** pushed from event overview  
**Content:** Filter bar (All / Red-flagged / Eliminated), list of team cards with status chips  
**States:**
- Filter: `all`, `flag`, `elim`
- Empty per-filter state

#### 4g. Shortlist
**Function:** `SC.shortlist`  
**Route:** pushed from event overview  
**Content:** Submission review grid/table, select/deselect teams, "Finalize" CTA  
**States:**
- Pre-finalize: selectable teams
- Finalized: locked, read-only

#### 4h. Create / Edit Event
**Function:** `SC.create`  
**Route:** pushed from admin home (create new) or via `SH.editEvent` (edit existing)  
**Content:** Event poster (tap to change), editable fields: name, domain chips, dates, team size, tracks, fee, links; preview of current state  
**Note:** Also used in a tall sheet overlay (`SH.editEvent`) for editing from organizer dashboard

#### 4i. New Event Wizard
**Function:** `SC.newev`  
**Route:** pushed from admin home  
**Content:** 4-step wizard with step indicator dots:
1. Basic Hackathon Details (name, domain, poster)
2. Schedule & Teams (dates, team size limits)
3. Tracks, Prizes & Fee (track tiles, prize amounts, fee toggle)
4. Links, Rules & Contact (website, rules, contact email)

**States per step:**
- Incomplete: "Next" disabled
- Valid: "Next" active, then "Publish" or "Save Draft" on step 4

#### 4j. Stage Builder (List)
**Function:** `SC.stages`  
**Route:** pushed from new event wizard or event settings  
**Content:** Ordered list of stages, drag-to-reorder (implied), "Add Stage" button, "Save" action  

#### 4k. Stage Detail (Edit)
**Function:** `SC.stage`  
**Route:** pushed from stage builder list  
**Parameters:** `{i}` (stage index)  
**Content:** Stage name, deadline date picker, criteria/rubric tiles, poster upload, delete button  
**States:**
- Valid stage: save enabled
- Invalid (no name): save disabled with shake animation

---

### Tab 2 — Live

#### 5a. Live Scoring
**Function:** `SC.live`  
**Route:** `live` (root of Live tab)  
**Content:** Stage selector pill, filter bar (All / Active / Done / Eliminated), search, team cards in grid  
**iPad difference:** 3-column grid (`.grid2` with `repeat(3,1fr)`)  
**States:**
- No active stage: "Event not started" empty
- Active stage: team grid with advance/eliminate buttons
- Filtered view

#### 5b. Team Detail (Live)
**Function:** `SC.team`  
**Route:** pushed from live grid  
**Parameters:** `{id}` (team id)  
**Content:** Team name/members, submission details, stage history timeline, advance/eliminate action buttons  
**States:**
- Active team: action buttons enabled
- Eliminated: red state, no actions
- Last stage (finalist): finalist badge

---

### Tab 3 — Messages (Organizer)

#### 6a. Messages Hub (Organizer)
**Function:** `SC.amsgs`  
**Route:** `msgs` (root of organizer messages tab)  
**Content:** Tiles for: Announcements (with unread badge), Help Desk (with unread count), Organizer Chat  
**States:**
- Unread counts on each tile

---

## Admin Screens (Role `a`)

Admin tabs: **Home** · **Live** (shared with organizer) · **Messages** (shared with organizer)

#### 7a. Admin Home
**Function:** `SC.ahome` (same function, different content when no org set)  
**Route:** `home` / `ahome`  
**Content:** "My Events" title, list of all events for the institute, "New Event" button  
**States:**
- No events: empty state with prompt to create

---

## Shared Screens (All Roles)

#### 8a. Stage Info (read-only)
**Sheet:** `SH.stageInfo`  
**Triggered from:** event space, pass screen stage dots  
**Content:** Stage name, deadline, submission criteria list, poster if set

---

## Sheet Overlays (SH.*)

Sheets slide up from the bottom over any screen. All share the grab-handle header.

| Sheet | Function | Description |
|-------|----------|-------------|
| Event switch | `SH.evSwitch` | Filter pills for switching active event view |
| Department picker | `SH.deptPick` | List of departments for registration form |
| Filters | `SH.filters` | Advanced filter sheet (events list, registrations) |
| Edit event | `SH.editEvent` | Tall sheet wrapping `SC.create` as an overlay pane |
| Stage info | `SH.stageInfo` | Read-only stage details with criteria |
| Activity log | `SH.logs` | Scrollable admin/org action log with filter |
| Sign in with Apple | `SH.apple` | Apple ID picker (demo list) |
| Auth | `SH.auth` | Full 5-step auth flow (see §Auth above) |
| Add member | `SH.addMember` | Member search + invite form (team or registration) |
| Notifications | `SH.notifs` | Notification list with read/unread, action buttons |
| Edit team | `SH.editTeam` | Team roster management (replace/remove members) |
| Profile | `SH.profile` | User profile with editable phone, complete-profile flow |
| Add to calendar | `SH.calendar` | Toggles per stage deadline, single "Add to Calendar" btn |
| Submit project | `SH.submit` | Submission type selector + link/file upload |
| New announcement | `SH.post` | Type picker, priority toggle, title + body fields |
| Finalize shortlist | `SH.send` | Multi-step progress animation (check → QR → alerts → emails → deadline) |
| QR check-in | `SH.qr` | Scanner overlay, found/done phases, arrival stats |
| Choose role | `SH.roles` | Role picker (see §Auth above) |
| Add organizer | `SH.addOrg` | Search for user by roll/email to grant organizer access |
| Manage submissions | `SH.manage` | Stage-scoped team submission review with search |
| Manage registrations | `SH.regManage` | Searchable/filterable reg list in a sheet |
| Registration detail | `SH.regDetail` | Tall sheet: team header + member list + status |
| Team detail (admin/org) | `SH.teamD` | Tall sheet: team scoring per stage, submission, log |
| Announcement filter | `SH.annFilter` | Filter toggles for announcements |

---

## Alert Dialogs (ask())

Triggered by `ask({...})` calls. All render as a centred `.alert` over a scrim.

| Trigger | Title / Message | Buttons |
|---------|----------------|---------|
| Revoke organizer access | "Revoke access?" | Cancel · Revoke (danger) |
| Restore organizer access | "Restore access?" | Cancel · Restore |
| Revoke individual member access | "Revoke [name]?" | Cancel · Revoke (danger) |
| Remove organizer from event | "Remove [name]?" or "Cancel invite for [name]?" | Cancel · Remove/Cancel invite (danger) |
| No teams yet (report push) | "No Report Yet" | OK only |
| Delete account | "Delete account?" | Cancel · Delete (danger) |
| Event published / draft saved | "Organizer access ready" (ID + PW) | Done only |
| Validation failure (new event) | "Add a name" / "Choose a poster" / "Pick a domain" / "Check dates" | OK only (danger) |
| Delete stage | "Delete this stage?" | Cancel · Delete (danger) |
| Stage name missing | "Name every stage" | OK only (danger) |
| Red-flag team | "Red-flag [name]" | with reason field | Cancel · Red-flag (danger) |
| Eliminate team (from regs) | "Eliminate [name]?" | Cancel · Eliminate (danger) |
| Finalize shortlist | "Finalize shortlist?" | Cancel · Finalize |
| Close event | "Close [name]?" | Cancel · Close Event (danger) |
| Advance team | "Advance [name]" | with optional note field | Cancel · Advance |
| Eliminate team (from live) | "Eliminate [name]" | with optional reason field | Cancel · Eliminate (danger) |
| Shortlist team (QR) | "Shortlist [name]?" | Cancel · Shortlist |
| ndWarn (wizard validation) | Various field-specific messages | OK only (danger) |

---

## iPad-Specific Layout Notes

The iPad uses the same `SC.*` and `SH.*` functions as iPhone. Layout differences:

| Element | iPhone | iPad |
|---------|--------|------|
| Navigation | Bottom tab bar (66px, floating) | Left sidebar (268px expanded, 72px rail) |
| Sidebar collapse | N/A | Rail mode via `.app.rail` toggle |
| Screen splitting | None | `pane.split` on messages/live screens: col1 (352px list) + col2 (detail) |
| Pass / Space screens | Single column | 2-column layout (`.bd column-count:2`) |
| Live grid | 2-column | 3-column |
| Sheet width | Full width − 16px | 640px centred |
| Tall sheet | Full height | `min(780px, 100% − 56px)` |
| Alert width | 288px | 330px |
| Content max-width | Full | 780px centred |
| Poster aspect ratio | 16:10 | 16:9 |
| Scroll padding | 118px top | 100px top |

---

## Counts

| Category | Count |
|----------|-------|
| Unique iPhone push-stack screens | **30** |
| Auth flow steps (within single sheet) | **5** |
| Unique sheet overlays | **24** |
| Alert dialog variants | **17** |
| **Total unique iPad screens** | **30** (same set, different layout) |
| iPad split-view panes | **~4** (messages, live, help desk, announcements) |
