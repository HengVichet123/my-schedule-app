# Shift PWA — Project Notes

Live at: https://HengVichet123.github.io/my-schedule-app/

## What This Is

A personal schedule PWA (installable on iPhone home screen) that shows a 2-week calendar with work shifts and school classes. Works offline. Syncs with GitHub.

---

## How It Works

### Two projects work together:
- `shift-timetable/` — Flask app on the computer. Scrapes TUT school schedule (Playwright) and reads shift screenshots (OCR via Tesseract). Saves data to `shifts.json` and `school_shifts.json`.
- `shift-pwa/` — The actual phone app. Pure HTML/JS, hosted on GitHub Pages. No server needed on the phone.

### Data flow:
```
Computer OCR/scraper → sync.sh → GitHub → 🔄 phone pulls
Phone edits → 🔄 phone pushes (needs GitHub token) → GitHub
```

### `sync.sh` (run on computer):
Copies `shifts.json` and `school_shifts.json` from `shift-timetable/` into this folder and pushes to GitHub.

---

## Phone App Features

| Button | What it does |
|--------|-------------|
| ＋ | Add entry manually |
| 📷 | Upload shift screenshot → OCR reads dates/times |
| 🎓 | Pull school schedule from GitHub |
| 🔄 | Sync shifts — pulls from GitHub, pushes phone edits back (needs token) |
| ⚙️ | Settings: GitHub token, export/import data, restore backups |

### Calendar
- Shows 2 weeks (Mon–Sun × 2)
- 6:00–midnight, fits screen without scrolling
- Red line = current time, updates every 30 seconds
- Blue blocks = shifts (from OCR/sync), Green = classes (from school), Other colors = personal entries

### Adding entries
- Pick date, start/end time, label, color (7 colors — blue/green reserved for system)
- **Repeat weekly**: check the box, pick which days, set an end date → creates the whole series at once
- All entries in a series share a `groupId`

### Editing / Deleting
- Tap any block to edit
- "Apply changes to all in series" checkbox → changes affect the entire repeat series
- Delete with that checkbox checked → removes the whole series at once
- Delete without checkbox → removes just that one day

### Sync (bidirectional)
- Needs a GitHub Personal Access Token stored in ⚙️ settings
- Token: GitHub → Settings → Developer settings → Personal access tokens → Fine-grained → Contents: Read & Write for `my-schedule-app` repo
- Phone wins on conflict (phone edits are never overwritten by GitHub data)
- Auto-saves a backup snapshot before every sync (keeps last 10)

### Backups
- Saved automatically before each sync
- Restore from ⚙️ → Backups list
- Each context (installed app vs Safari browser) has its own separate backups

### Transfer data between Safari and installed app
Safari and the installed home screen app have completely separate storage.
- To transfer: ⚙️ → **Copy all data** (in source) → ⚙️ → **Paste & import** (in destination)
- This transfers entries, backups, and GitHub token all at once

---

## File Structure

```
shift-pwa/
├── index.html       — the entire app (HTML + CSS + JS, ~700 lines)
├── sw.js            — service worker for offline caching (bump version to force update)
├── manifest.json    — PWA metadata (name, icon, colors)
├── icon.png         — home screen icon (512x512)
├── shifts.json      — shift data synced from computer
├── school_shifts.json — school schedule synced from computer
└── sync.sh          — run on computer to push data to GitHub
```

---

## Common Issues

**App not updating after a code change:**
Bump the cache version in `sw.js` (`schedule-vN` → `schedule-vN+1`), commit and push. The new SW activates immediately (skipWaiting is set).

**Data missing on phone:**
Open from the **home screen icon**, not Safari browser — they have separate storage. If data is gone, tap 🔄 to pull from GitHub, or restore from ⚙️ backups.

**Red time line not visible:**
Only shows between 6:00–24:00. Outside that range (e.g. past midnight) it hides automatically.
