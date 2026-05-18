# my-schedule-app

A personal schedule PWA (Progressive Web App) installable on an iPhone home screen. Displays a 2-week calendar with work shifts and school classes. Works offline and syncs with GitHub.

**Live:** https://HengVichet123.github.io/my-schedule-app/

---

## Features

- 2-week calendar view (Mon–Sun × 2), 6:00–midnight
- Red line shows current time, updates every 30 seconds
- Blue blocks = shifts, Green = school classes, other colors = personal entries
- Add entries manually or via shift screenshot (OCR)
- Repeat weekly entries — create a whole series at once
- Bidirectional sync with GitHub (pull from computer, push phone edits back)
- Auto-backup before every sync (keeps last 10 snapshots)
- Fully offline via service worker

---

## Controls

| Button | Action |
|--------|--------|
| `＋` | Add entry manually |
| `📷` | Upload shift screenshot — OCR reads dates and times |
| `🎓` | Pull school schedule from GitHub |
| `🔄` | Sync — pulls from GitHub, pushes phone edits back |
| `⚙️` | Settings: GitHub token, export/import, restore backups |

---

## Sync Setup

Sync requires a GitHub Personal Access Token:

1. GitHub → Settings → Developer settings → Personal access tokens → Fine-grained
2. Grant **Contents: Read & Write** for this repo
3. Paste the token in `⚙️` → Settings inside the app

---

## Data Flow

```
Computer (OCR / school scraper) → sync.sh → GitHub → phone pulls
Phone edits → phone pushes → GitHub
```

Run `sync.sh` on the computer after adding new shifts or updating the school schedule.

---

## File Structure

```
my-schedule-app/
├── index.html          # Entire app — HTML + CSS + JS
├── sw.js               # Service worker for offline caching
├── manifest.json       # PWA metadata
├── icon.png            # Home screen icon
├── shifts.json         # Shift data synced from computer
├── school_shifts.json  # School schedule synced from computer
└── sync.sh             # Run on computer to push data to GitHub
```

---

## Updating the App

After editing `index.html`, bump the cache version in `sw.js` (`schedule-vN` → `schedule-vN+1`), then commit and push. The new service worker activates immediately.

---

## Transfer Data Between Safari and Installed App

Safari and the home screen app have separate storage. To transfer:
- Source: `⚙️` → **Copy all data**
- Destination: `⚙️` → **Paste & import**

This transfers entries, backups, and the GitHub token all at once.
