# Carter's 5th Grade Dashboard — Project Notes

Family reference dashboard for Carter's 2026–2027 5th grade year at Spring-Ford
Intermediate School. Single self-contained static site — no build step, no
framework, no external JS libraries. Everything (calendar logic, schedule
data, rendering) lives in `index.html`.

## Live deployment

- **URL:** https://carters-5th-grade-dashboard-ericg1840-4155s-projects.vercel.app
- **Vercel project name:** `carters-5th-grade-dashboard`
- Deploys have been pushed manually (base64 file upload) up to now, always to
  the same project name / `target: production`, which is why the URL has
  stayed stable across every redeploy.
- **Not yet confirmed live:** the last push (adding `manifest.json` and the
  three `icon-*.png` files for "Add to Home Screen" support) hit a tool error
  and may not have fully landed. Verify `/manifest.json` and `/icon-192.png`
  actually resolve on the live URL before assuming the home-screen icon
  works — if they 404, just redeploy this folder as-is.

## Files in this folder

| File | Purpose |
|---|---|
| `index.html` | The entire app — HTML, CSS, and JS in one file |
| `manifest.json` | Web app manifest for "Add to Home Screen" (Android/Chrome) |
| `icon-180.png` | Apple touch icon (iOS home screen) |
| `icon-192.png` / `icon-512.png` | Manifest icons (Android/Chrome) |

All four icon files are the same layered "C" mark (see **Favicon / app icon**
below) rendered at different sizes with a solid navy background — regenerate
from the SVG source in that section if you ever need a different size.

## Recommended next step: move to git

This project has outgrown "paste base64 into a tool call" as a deploy method.
Recommended setup:

1. `git init` this folder, commit the five files above.
2. Push to a new GitHub repo.
3. In the Vercel dashboard, go to the existing `carters-5th-grade-dashboard`
   project → **Settings → Git** → connect it to that new GitHub repo. This
   keeps the existing production domain/aliases — you don't need to create a
   new Vercel project.
4. From then on: edit the file, `git commit`, `git push` → Vercel auto-deploys.

## Data sources & provenance

Everything school-specific in this dashboard was sourced from two real
documents Eric uploaded — **not** invented. If any of this needs correcting,
these are the actual sources of truth:

1. **Class schedule** — Carter's Skyward schedule printout
   (`skyward_iscorp_com_..._sfschedule002_w.pdf`)
2. **District calendar** — Spring-Ford Area School District 2026–2027 printable
   calendar PDF
3. **Busing** — Carter's Skyward busing page (screenshot)

### School year & day counter

- First day of school: **Aug 24, 2026** (Day 1)
- Last day of school: **Jun 3, 2027** (half day)
- The district's calendar states **180** official student days. Walking
  through every closure/holiday/break/in-service day listed on the printed
  calendar day-by-day, this dashboard's logic comes out to **179**. This gap
  is called out in the dashboard's footer but is **unresolved** — nobody has
  confirmed with the district whether there's an extra contingency day not
  marked on the calendar. If you get a confirmed number, it's a one-line fix
  (add/remove a date from `NON_SCHOOL_DAYS` in the JS).
- The day counter and rotation-day counter are both computed live in JS from
  the visitor's actual system clock (`new Date()`) — there is no hardcoded
  "today." All dates are built via `new Date(y, m, d)` constructors, never by
  parsing a date string, to avoid timezone off-by-one bugs.

### Non-school days (`NON_SCHOOL_DAYS` in the JS)

Type is one of `closed` (schools/offices fully closed), `noschool` (no
school for students, staff/offices may still operate), or `inservice`
(staff-only professional development day).

```
Sep 7, 2026   closed      Labor Day
Sep 21, 2026  closed      Yom Kippur
Oct 19, 2026  inservice   Staff In-Service Day
Nov 3, 2026   inservice   Election Day — Staff In-Service
Nov 23, 2026  noschool    Conferences — No School for Students
Nov 24, 2026  noschool    Conferences — No School for Students
Nov 25, 2026  closed      Fall Break
Nov 26, 2026  closed      Fall Break — Thanksgiving
Nov 27, 2026  closed      Fall Break
Dec 24, 2026  closed      Winter Break
Dec 25, 2026  closed      Winter Break — Christmas
Dec 28, 2026  closed      Winter Break
Dec 29, 2026  closed      Winter Break
Dec 30, 2026  closed      Winter Break
Dec 31, 2026  closed      Winter Break
Jan 1, 2027   closed      Winter Break — New Year's Day
Jan 18, 2027  closed      Martin Luther King Jr. Day
Feb 15, 2027  noschool    Presidents' Day
Mar 10, 2027  inservice   Eid al Fitr — Act 80 Staff In-Service
Mar 25, 2027  closed      Spring Break
Mar 26, 2027  closed      Spring Break
Mar 29, 2027  closed      Spring Break
May 17, 2027  noschool    Eid al Adha
May 18, 2027  inservice   Election Day — Staff In-Service
May 31, 2027  closed      Memorial Day
```

### Half days (school day happens, early dismissal)

```
Jan 27, 2027   PM Staff In-Service
Feb 12, 2027   PM Staff In-Service
Mar 24, 2027   PM Staff In-Service (also End of Quarter 3)
Apr 21, 2027   PM Staff In-Service
Jun 3, 2027    Last Day of School (also End of Quarter 4)
```

### Quarter ends (informational marker only, not an exclusion)

```
Oct 28, 2026 — End of Quarter 1
Jan 19, 2027 — End of Quarter 2
Mar 24, 2027 — End of Quarter 3
Jun 3, 2027  — End of Quarter 4 / Last Day
```

### Class schedule — 6-day rotation

Carter's schedule runs on a **Day 1–6 cycle**, not the calendar week. The
dashboard's core assumption (unconfirmed with the school, but the only
sensible reading): **the cycle only advances on days Carter actually attends
school** — it pauses over weekends/holidays and simply resumes at the correct
next day-number afterward, rather than advancing on the calendar's own
rhythm. Day 1 = Aug 24, 2026 (day the school year starts).

Periods that repeat every rotation day:
- **Homeroom** — Brian Kraeer, Rm W252 *(no clock time given by Skyward)*
- **ELA** — Jamie Cross, Rm W249, **8:50 AM–12:20 PM** *(only block time Skyward gives)*
- **Period 2** — Brian Kraeer, Rm W252, **12:21–3:40 PM** *(only other block time given)*
  - Days 1, 2, 4, 5: "Target Time"
  - Days 3, 6: "Pro Social / Class Meeting"
- **Math** — Christa Puco, Rm W250 *(no clock time given)*
- **Science** — Christa Puco, Rm W250 *(no clock time given)*
- **Social Studies** — Brian Kraeer, Rm W252 *(no clock time given)*

Period 5 ("Specials") rotates by day:

```
Day 1 — Art                  — Katherine Beerer   — Rm W169
Day 2 — Music                — Bevin Seislove     — Rm W148
Day 3 — Library / Ram Quest  — Shayne Tobin        — Rm LIB · W269  (ambiguous, see below)
Day 4 — Health                — Jeffrey Moyer       — Rm E178
Day 5 — Physical Education   — Sheila Cemini       — Gym 4
Day 6 — Library / Ram Quest  — Shayne Tobin        — Rm LIB · W269  (ambiguous, see below)
```

**Open question:** Days 3 and 6 both list "Library" and "Ram Quest with
Shayne Tobin" without saying which is which on a given week. The dashboard
shows both and flags this as unresolved — a teacher/school confirmation
would let you split this into two distinct, correctly-alternating entries.

**Important:** Don't add specific bell times for Homeroom, Math, Science, or
Social Studies unless you get them from an updated Skyward printout — the
current source document genuinely doesn't list them, and inventing times
would misrepresent the actual schedule.

### Busing

```
Bus number:  937
Route:       SFIS/UPE 937 SP
Stop:        Crown Pointe Dr & N Lewis Rd
AM Pickup:   8:04 AM
PM Drop-off: 4:05 PM
```
No day-of-week restriction was listed on the Skyward busing page, so this is
shown as applying every school day.

### Homeroom teacher contact

```
Name:   Brian Kraeer
Role:   Homeroom · Target Time / Pro Social · Social Studies — Rm W252
Email:  bkrae@spring-ford.net (primary contact)
Phone:  610-705-6003 x3503 (school phone)
```

### Weather

Live current conditions + today's high/low for **Royersford, PA** (lat
40.1843, lon -75.5380 — approximate town center, not the exact school
building) via **Open-Meteo** (`api.open-meteo.com`), a free API that needs no
key and works fine called directly from client-side JS. Imperial units,
`America/New_York` timezone.

### What was deliberately left out

- **No 5th-grade-specific events** (open house, assemblies, field trips) are
  included — the district calendar PDF only has district-wide dates. If Eric
  gives you real dates from the school, add them to a small events list; don't
  invent plausible-sounding ones.
- No "lunch" period or invented per-period bell times — see the schedule
  section above.

## Design system

- **Palette (Spring-Ford navy & gold):**
  - Navy: `#081327` (bg), `#0c1c38`, `#122a4d`, `#1a3a68`, `#254a80`
  - Gold: `#b98c3e`, `#cda454`, `#ddbd78`, `#f2e3bf`
  - Cream (text): `#f6f1e4`
  - Steel blue (staff in-service tag): `#4c7096`
  - Rose (no-school tag): `#c1546a`
- **Font:** system font stack — `-apple-system, BlinkMacSystemFont, 'SF Pro
  Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`.
  Deliberately *not* loading a web font (previously used Google Fonts
  Oswald/Source Sans, removed per request to match Apple's native look and
  cut a network request).
- **Layout:** top bar (title + live date/status pill) → info banner → weather
  strip → 3 stat cards (Up Next / Next Day Off / School Year, each with an
  icon, badge, and the School Year card getting a progress bar) →
  two-column section — left column is the hero: Today's Schedule as a
  vertical timeline (time on the left, connecting dots, current/up-next
  period highlighted using the two known bell-time blocks) followed by the
  compact mini-calendar and the Day Detail panel; right column holds Bus
  Info, Homeroom Teacher, Specials Legend, and What's Coming Up.
- **Card borders:** `rgba(255,255,255,0.13)` (not gold-tinted) so cards read
  as distinct surfaces against the navy background, plus a subtle inset
  top-highlight for depth.
- Each subject/special has its own icon + color defined in `SUBJECT_STYLE` in
  the JS (e.g. Art = 🎨 purple, Math = 📐 teal) — reuse these if you add UI
  elsewhere referencing a subject.

## Favicon / app icon

A layered "C" mark in Spring-Ford colors (navy back layer, gold middle layer,
white front face outlined in navy), styled after a reference "bubble letter"
image Eric provided. Built as inline SVG text layers (three `<text>`
elements offset diagonally), not a hand-drawn path — cheap to regenerate at
any size:

```html
<svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
  <text x="57" y="76" font-family="Arial, Helvetica, sans-serif" font-weight="900" font-size="76" text-anchor="middle" fill="#1a3a68">C</text>
  <text x="53.5" y="72.5" font-family="Arial, Helvetica, sans-serif" font-weight="900" font-size="76" text-anchor="middle" fill="#cda454">C</text>
  <text x="50" y="69" font-family="Arial, Helvetica, sans-serif" font-weight="900" font-size="76" text-anchor="middle" fill="#ffffff" stroke="#12203f" stroke-width="2.5" paint-order="stroke fill">C</text>
</svg>
```

The favicon in `index.html`'s `<head>` is this same SVG inlined as a
`data:image/svg+xml,` URL (no separate file needed for the browser-tab icon).
The `icon-*.png` files are a variant of the same mark rasterized onto a solid
navy (`#0c1c38`) square background, since transparent home-screen icons look
odd once iOS/Android apply their own rounded-corner mask.

## Conversation history for more context

If you need the full back-and-forth that produced any of these decisions
(e.g. why the day-count assumption was made a certain way, or the exact
reasoning on the rotation-pause logic), the original chat thread has the
complete derivation — worth a skim if something here seems ambiguous.
