# Vehicle Maintenance Tracker

A single-file web app for tracking vehicle maintenance schedules and warranty coverage. No build step, no account, no backend — just open `index.html` in a browser.

## Features

- **Multiple vehicles** — track as many cars, SUVs, trucks, or motorcycles as you own.
- **Maintenance schedule** by mileage and/or date (whichever comes first), with common presets (oil change, tire rotation, brake inspection, etc.) plus custom items. Motorcycles get their own preset list (minor/major service, chain care, valve clearance, etc.) based on Royal Enfield's published service structure — a reasonable starting point for most single-cylinder bikes, but edit the intervals to match your exact model's manual.
- **Status at a glance** — each item is flagged OK, Due soon, or Overdue based on your vehicle's current mileage and today's date.
- **Warranty tracking** — record warranty coverage (provider, expiration date/mileage, notes) and optionally link it to a maintenance item. If that item comes due while the warranty is still active, you'll see a "may be covered under warranty" note.
- **Calendar reminders** — each due item has an "Add reminder" link that opens a pre-filled Google Calendar event (one click to save — no sign-in or API setup required).
- **Photo upload for receipts and odometer readings** — attach a photo of a service receipt to a maintenance log entry, or a photo of your odometer to a mileage update. The app scans the photo on-device (no internet, no account) and tries to auto-fill the date, cost, and mileage for you to review before saving.
- **Service and mileage history** — every logged service and every mileage update is kept (with its photo, if attached), viewable per maintenance item or per vehicle.
- **Local storage** — all data (and photos) stays on your device by default. Use the Export/Import buttons to back up or move your data, including photos.
- **Cloud sync across devices** — optionally sync your data (and photos) to a private GitHub repo you own, so the same data shows up on your phone and laptop. Explicit "Push" and "Pull" buttons, no automatic background syncing.

## Usage

1. Serve `index.html` with a local static server (see note below on why this matters) and open it in a modern browser.
2. Add a vehicle with its current mileage.
3. Add maintenance items — either "Add common maintenance items" for a quick starting set, or add your own with custom intervals.
4. When you get service done, click "Log done". Optionally attach a photo of the receipt — the app will try to read the date, cost, and service details off it automatically; review and edit before saving. The next due date/mileage recalculates automatically.
5. To update your mileage, click "Update mileage" and optionally attach a photo of your odometer — the app will try to read the number and suggest it for you to confirm.
6. Add any warranties (bumper-to-bumper, powertrain, extended, etc.) and link them to the maintenance items they cover.
7. Click "History" on any maintenance item, or "Mileage history" on a vehicle, to see past entries and view attached photos.
8. Click "Export data" occasionally to save a JSON backup (this includes your photos), since everything lives in your browser's local storage/IndexedDB.

## Notes on Google Calendar integration

This app doesn't do full automatic calendar sync — that requires a hosted OAuth setup with Google, which isn't practical for a plain local HTML file. Instead, each due/upcoming maintenance item gets a one-click "Add reminder" link that opens Google Calendar with the event pre-filled; you just hit save. If you'd like true automatic sync (events created for you without a click) or hosted access from multiple devices, that would need a small backend — let me know if you want that built out.

## Notes on photo scanning (OCR)

Text recognition runs fully on-device using a vendored copy of [Tesseract.js](https://github.com/naptha/tesseract.js) (see `vendor/tesseract/`) — no photo or data ever leaves your browser, and no internet connection is needed once the page is loaded.

**Important:** the OCR feature (and only that feature) requires the app to be served over `http://` or `https://` rather than opened directly as a `file://` path, because browsers block the background Web Worker it needs when running from a raw file. If you just double-click `index.html`, everything else works fine — you just won't get auto-fill from photos, and you'll see a message saying so; you can still attach the photo and type the details in yourself.

To serve it locally, run one of these from the project folder and open the printed URL:
```
python3 -m http.server 8080
# or
npx serve
```

Scanning accuracy depends on photo clarity — always double-check the auto-filled fields before saving.

## Notes on cloud sync

Click "☁ Cloud sync" to set this up. It stores your data as a single JSON file (photos included, as embedded base64) in a **private GitHub repo you create and own** — the app never talks to any server except GitHub's own API, directly from your browser.

Setup (one time, per device):
1. Create a private GitHub repo to hold your data (separate from any app-code repo, so your personal data never ends up in a public repo).
2. Generate a [fine-grained personal access token](https://github.com/settings/personal-access-tokens/new) scoped to **only that repo**, with "Contents: Read and write" permission — nothing broader.
3. Paste the token, repo owner, and repo name into the Cloud sync dialog and click "Save settings".

Then, on any device: click "⬆ Push to cloud" after making changes, and "⬇ Pull from cloud" on another device to catch up. Sync is manual and explicit in both directions — nothing happens automatically, and pulling always asks you to confirm before overwriting local data. Because the token is a sensitive credential, it's stored only in that browser's local storage and isn't included in Export/Import backups; you'll need to paste it again on each new device.
