# Vehicle Maintenance Tracker

A single-file web app for tracking vehicle maintenance schedules and warranty coverage. No build step, no account, no backend — just open `index.html` in a browser.

## Features

- **Multiple vehicles** — track as many cars as you own.
- **Maintenance schedule** by mileage and/or date (whichever comes first), with common presets (oil change, tire rotation, brake inspection, etc.) plus custom items.
- **Status at a glance** — each item is flagged OK, Due soon, or Overdue based on your vehicle's current mileage and today's date.
- **Warranty tracking** — record warranty coverage (provider, expiration date/mileage, notes) and optionally link it to a maintenance item. If that item comes due while the warranty is still active, you'll see a "may be covered under warranty" note.
- **Calendar reminders** — each due item has an "Add reminder" link that opens a pre-filled Google Calendar event (one click to save — no sign-in or API setup required).
- **Local storage** — all data stays in your browser. Use the Export/Import buttons to back up or move your data.

## Usage

1. Open `index.html` in any modern browser (double-click it, or serve it with any static file server).
2. Add a vehicle with its current mileage.
3. Add maintenance items — either "Add common maintenance items" for a quick starting set, or add your own with custom intervals.
4. When you get service done, click "Log done" and enter the date/mileage — the next due date/mileage recalculates automatically.
5. Add any warranties (bumper-to-bumper, powertrain, extended, etc.) and link them to the maintenance items they cover.
6. Click "Export data" occasionally to save a JSON backup, since everything lives in your browser's local storage.

## Notes on Google Calendar integration

This app doesn't do full automatic calendar sync — that requires a hosted OAuth setup with Google, which isn't practical for a plain local HTML file. Instead, each due/upcoming maintenance item gets a one-click "Add reminder" link that opens Google Calendar with the event pre-filled; you just hit save. If you'd like true automatic sync (events created for you without a click) or hosted access from multiple devices, that would need a small backend — let me know if you want that built out.
