# Upkeep

A single-file web app for tracking vehicle and household maintenance. No build step, no account, no backend — just open `index.html` in a browser. Switch between the **🚗 Vehicles** and **🏠 Household** tabs at the top; each keeps its own data but shares the same local storage, export/import, and cloud sync.

## Vehicle tracker features

- **Multiple vehicles** — track as many cars, SUVs, trucks, or motorcycles as you own.
- **Maintenance schedule** by mileage and/or date (whichever comes first), with common presets (oil change, tire rotation, brake inspection, etc.) plus custom items. Motorcycles get their own preset list — the built-in defaults are taken directly from the Royal Enfield Hunter 350 owner's manual periodic maintenance chart. A 2010-era Honda CR-V (make "Honda", model containing "CR-V") also gets its own preset based on Honda's documented Maintenance Minder intervals for that generation. Edit intervals to match your specific vehicle/manual if different.
- **Schedule reflects real-world mileage** — when you add the default items, each one's assumed last-service point snaps to the nearest checkpoint at or below your vehicle's current mileage, so a used vehicle's schedule reflects where it actually sits in the cycle instead of resetting to "just serviced today."
- **Status at a glance** — each item is flagged OK, Due soon, or Overdue based on your vehicle's current mileage and today's date.
- **Warranty tracking** — record warranty coverage (provider, expiration date/mileage, notes) and optionally link it to a maintenance item. If that item comes due while the warranty is still active, you'll see a "may be covered under warranty" note.
- **Calendar reminders** — each due item has an "Add reminder" link that opens a pre-filled Google Calendar event (one click to save — no sign-in or API setup required).
- **Photo upload for receipts and odometer readings** — attach a photo of a service receipt to a maintenance log entry, or a photo of your odometer to a mileage update. The app scans the photo on-device (no internet, no account) and tries to auto-fill the date, cost, and mileage for you to review before saving.
- **Service and mileage history** — every logged service and every mileage update is kept (with its photo, if attached), viewable per maintenance item or per vehicle.
- **Local storage** — all data (and photos) stays on your device by default. Use the Export/Import buttons to back up or move your data, including photos.
- **Cloud sync across devices** — optionally sync your data (and photos) to a private GitHub repo you own, so the same data shows up on your phone and laptop. Explicit "Push" and "Pull" buttons, no automatic background syncing.

## Household tracker features

Built for a condo owner who needs to separate "my job" from "the HOA's job" at a glance.

- **Location + responsibility tagging** — every item is tagged `inside_unit` / `outside_unit` and `personal` / `association` / `limited_common_element` / `verify`. Color encodes responsibility (blue = personal, gray = association, teal = limited common element, amber = verify/unconfirmed), a small house/building icon encodes location, and safety-critical tasks get a red badge and outline that never blends into a routine item.
- **Comprehensive default catalog** — ~40 common condo items across HVAC, water heater, plumbing, kitchen appliances, laundry, structural/exterior, safety, electrical, and lawn/irrigation, each with suggested location, responsibility, and task intervals pulled straight from the spec. Picking a catalog item pre-fills everything as an editable suggestion; nothing is locked in. You can also add a fully custom item ("Other" category, blank form).
- **Two dashboard views** — **My To-Do** (personal-responsibility tasks only, the action list) and **Property Overview** (everything, including HOA/shared items, shown as awareness/tracking with a lighter-weight "Log" action instead of full one-tap controls). Both are sorted by urgency (overdue → due this month → due this quarter → later) with safety-critical overdue items surfaced above routine ones in the same bucket — never sorted by category.
- **One-tap Done, plus Skip and N/A** — click "✓ Done" straight from the list to log a completion with today's date and no form. "Detail" expands an optional cost/method/note before logging. "Skip" advances the schedule without a full log. "N/A" retires a task (e.g., you sold the item) without deleting its history — reactivate it later from the item page.
- **Browser notifications** — click "🔔 Enable reminders" to get local due-date notifications. Safety-critical tasks get a heads-up 14 days out plus a due/overdue notice; routine tasks get a single notice at the due date. This is a simple client-side check (fires while the tab is open/foregrounded), not a background push service.

## Usage — Vehicles

1. Serve `index.html` with a local static server (see note below on why this matters) and open it in a modern browser.
2. Add a vehicle with its current mileage.
3. Add maintenance items — either "Add common maintenance items" for a quick starting set, or add your own with custom intervals.
4. When you get service done, click "Log done". Optionally attach a photo of the receipt — the app will try to read the date, cost, and service details off it automatically; review and edit before saving. The next due date/mileage recalculates automatically.
5. To update your mileage, click "Update mileage" and optionally attach a photo of your odometer — the app will try to read the number and suggest it for you to confirm.
6. Add any warranties (bumper-to-bumper, powertrain, extended, etc.) and link them to the maintenance items they cover.
7. Click "History" on any maintenance item, or "Mileage history" on a vehicle, to see past entries and view attached photos.
8. Click "Export data" occasionally to save a JSON backup (this includes your photos), since everything lives in your browser's local storage/IndexedDB.

## Usage — Household

1. Click the "🏠 Household" tab.
2. Click "+ Add Item", pick a category, then either pick a catalog preset (pre-fills location/responsibility/tasks, all editable) or "-- Custom (blank) --" to enter everything yourself.
3. Review/edit the item's fields and save — you land on the item's detail page where its tasks (if any came from the catalog) are already listed. Add more tasks with "+ Add task".
4. Use "My To-Do" for what's yours to handle, "Property Overview" for everything including the HOA's/shared items.
5. Log work with "✓ Done" (instant, no form), "Detail" (adds cost/method/note), "Skip" (pushes the due date without a full log), or "N/A" (retires a task you no longer need to track).
6. Click "🔔 Enable reminders" once to allow browser notifications for upcoming/overdue tasks.

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

Once set up, syncing happens automatically while the app is open: it pulls the latest cloud data when you open or return to the tab, and pushes your changes a few seconds after you make them (debounced, so it's not on every keystroke). The manual "⬆ Push to cloud" / "⬇ Pull from cloud" buttons are still there for forcing a sync on demand. Auto-sync only runs while the app tab is open — a fully closed browser/phone won't sync in the background, since that would require a real server.

**On conflicts:** because the whole dataset is stored as one file (not merged field-by-field), if two devices both make changes before syncing with each other, the app can't silently combine them — one version has to win. Rather than guessing, it shows a **"Sync conflict"** dialog letting you choose "Keep my changes here" or "Use cloud version" whenever it detects the cloud changed since this device's last known sync point *and* this device also has unsynced edits. If only one side changed, it applies the update automatically with no prompt.

Because the token is a sensitive credential, it's stored only in that browser's local storage and isn't included in Export/Import backups; you'll need to paste it again on each new device.
