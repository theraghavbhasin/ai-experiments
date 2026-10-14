---
title: Blueprint — Backblaze Backup Monitor
status: complete; reproduces the dashboard exactly, with no data
last_updated: 2026-10-10
---

# Blueprint — Backblaze Backup Monitor

A small personal dashboard that tracks how much of your data is backed up, from snapshots you type in by hand.

**To the agent reading this:** build exactly what follows. Do not ask questions. Start with **no data**; the only sample data is the demo set in section 6, loaded by a button. If a detail is missing, pick the simplest option that matches the rest of this file.

*Not affiliated with Backblaze. Do not use their logo or brand colours.*

**Licence:** free to use for anyone, for any purpose, with no conditions and no attribution needed (CC0 1.0, public domain).

---

## 1. What it does

You read two numbers off the Backblaze app, **total** and **remaining** (in MB), and save them as a snapshot. The dashboard shows the latest state, a chart of progress over time, and the list of snapshots. You can add and remove snapshots, and export everything as a JSON backup.

## 2. Data

One record type, a **snapshot**. Everything else is derived.

| Field | Type | Meaning |
|---|---|---|
| `ts` | integer | Time saved, epoch milliseconds. Also the record's id (as text). |
| `total_mb` | integer | Total data to back up, in MB |
| `remaining_mb` | integer | Data still to upload, in MB |
| `demo` | boolean, optional | `true` only on demo snapshots (section 6) |

Stored in a collection named `entries`, one record per snapshot, id = `String(ts)`.

**Derived** (never stored):

- `uploaded_mb` = max(total − remaining, 0)
- `percent` = min(100, uploaded ÷ total × 100), rounded to 1 decimal
- TB = MB ÷ 1,048,576, shown to 2 decimals (1 TB = 1,048,576 MB)
- **Latest** = the snapshot with the highest `ts`

## 3. Page structure

One column, centred, max width 1100 px, 16 px gap between blocks. In this order:

1. **Header**: title `Backblaze Backup Monitor`, then one grey line (the takeaway).
2. **Overview** card
3. **Update backup status** card
4. **Backup progress over time** card
5. **Saved snapshots** card

### Header takeaway

`Last updated {when} ({d MMM yyyy})` where `{when}` is `today`, `1 day ago`, or `N days ago`, counted from the latest snapshot. Example: `Last updated 27 days ago (12 Sep 2026)`. With no snapshots: `No snapshots yet. Add one below, or load demo data.`

### Overview card

Title `Overview`. A progress ring, then three rows.

- **Ring**: 128 px, stroke 8 px, round ends, starts at the top. Centre text is `{percent}%` over the word `Complete`. Fill is the accent colour; when remaining is 0 it turns the success colour. The track is the accent at 14% opacity.
- **Rows** (label left, value right): `Total data`, `Backed up`, `Remaining`. Each value is `{TB} TB` (big number, small grey unit) with the MB figure underneath, e.g. `2,61,51,975 MB`. When remaining is 0, the Remaining value turns the success colour.
- With no data every value shows `—`.
- Wide screens: ring on the left, the three stats beside it in a row.

### Update backup status card

Note: `Type the numbers Backblaze shows, in MB. Each save adds a snapshot stamped with the current time; × removes one.`

- Fields: `Total backup (MB)` (placeholder `e.g. 26,151,975`) and `Remaining backup (MB)` (placeholder `e.g. 2,474,937`). Commas and spaces are accepted. Under each field, a live grey conversion such as `24.94 TB`.
- Buttons, side by side, identical small outlined style: `Save snapshot`, `Export backup`, and a demo button (section 6). A status message sits beside or below them.
- Only people allowed to edit see the fields, **Save snapshot** and the demo button. Everyone sees **Export backup**.

**Save rules**, with exact messages:

| Condition | Message |
|---|---|
| a field empty or not a number | `Enter both numbers, in MB.` |
| total ≤ 0 or remaining < 0 | `Total must be above 0 and remaining cannot be negative.` |
| remaining > total | `Remaining is more than total. Check the numbers.` |
| saved | `Saved.` (and clear both fields) |
| write refused | `Could not save. Only editors of this dashboard can add snapshots.` |

Values are rounded to whole MB. The snapshot's `ts` is the current time.

**Export** downloads `B2-Calculator-Backup-YYYY-MM-DD-HH-mm-ss.json` (local time) in this exact shape, which the original standalone tracker can import:

```json
{
  "version": 2,
  "totalBackup": 0,
  "remainingBackup": 0,
  "logs": [{ "ts": 0, "totalBackup": 0, "remainingBackup": 0 }]
}
```

`totalBackup` and `remainingBackup` at the top are the latest snapshot's values. `logs` is every snapshot, oldest first. If downloads are unavailable: `Export is not available here.`

### Chart card

Title `Backup progress over time`. Note: `Each snapshot gets an equal-width slot, whatever the real gap between them. Total data and data backed up, in TB.`

- **Legend** above the chart: a dashed line swatch + `Total data`, a dot swatch + `Backed up`.
- **X axis**: one equal-width slot per snapshot (not real time). 2 to 7 date labels, about one per 90 px, format `Jun '24`, first left-aligned, last right-aligned.
- **Y axis**: TB, starts at 0, top is the largest total × 1.05, about 5 ticks labelled `5 TB`, `10 TB`, and so on. Left margin is measured from the widest label.
- **Series**: *Total data* is a 2 px dashed line (`5 4`), grey, no dots. *Backed up* is a 2 px solid accent line with round joins, a 2.5 px dot at every snapshot (1.5 px panel-coloured ring), and a soft fade underneath: a vertical gradient of the accent at opacity 0.22, 0.14, 0.07, 0.025, 0 at 0, 25, 50, 75, 100%.
- **Gridlines**: 1 px, very light. **Axis line**: 1 px. Tick labels 11 px, muted.
- **Height** 300 px (plus legend), width 100% of the card, redrawn on resize.
- **Hover** (mouse) or **drag/tap** (touch): a thin vertical line at the nearest snapshot, a 5 px dot on each series, and a small popover with the date and time (`12 Sep 2026, 14:30`), then `Total data: 24.94 TB` and `Backed up: 24.94 TB`. The popover stays inside the card. On touch, vertical swipes still scroll the page, and the popover stays until the next touch.
- With no data the chart area is empty.

### Saved snapshots card

Title `Saved snapshots`. Note `Newest first. Sizes in MB.`

- Columns: `Date` (`12 Sep 2026, 14:30`), `Total`, `Remaining`, `Backed up`, and a last, unlabelled column with `×`.
- Numbers use Indian digit grouping (`2,61,51,975`), right-aligned, tabular figures. Date is left-aligned.
- Rows where remaining is 0 get a small green dot before the date. Other rows keep the same space, empty.
- Sticky header; the list scrolls inside the card (max height 360 px).
- **Delete**: click `×` once, it changes to `Remove?` for 3 seconds; click again to delete for good. A failure shows `Could not remove that snapshot.` Only people allowed to edit can delete.

## 4. Look

### Fonts (system fonts only; no web fonts)

- Text: `system-ui, -apple-system, "SF Pro Text", "Segoe UI", Roboto, "Helvetica Neue", sans-serif`
- Numbers: `ui-rounded, "SF Pro Rounded", system-ui, -apple-system, "Segoe UI", Roboto, sans-serif`, weight 600, letter-spacing −0.02em.
- Title: text font, weight 650, letter-spacing −0.02em, 28 px. Big stat values 28 px. Ring percent 1.25 rem. Body 14 px, notes and table 12 px.

### Colours

Use the host's own text, page and card colours. Add these three, chosen by whether the card background is dark (average brightness below half) or light:

| Role | Light | Dark |
|---|---|---|
| Accent (Backed up, ring) | `#4338ca` | `#a5b4fc` |
| Total data (grey) | `#78716c` | `#a8a29e` |
| Success (complete) | `#15803d` | `#4ade80` |

Re-check the theme on every redraw and when the system theme changes.

### Cards and controls

- Card: card background, 1 px border, 12 px radius, 20 px padding.
- Inputs: 16 px text (stops iPhone zoom), 1 px border, 12 px radius, 11 rem wide.
- Buttons: 12 px text, 1 px border, transparent background, 12 px radius, padding 5 px 12 px, minimum height 34 px.

### Narrow screens (the page area is 560 px wide or less)

Judge by the page's own width, not the device, so it also works in a side panel.

- Card padding 12 px.
- Overview: ring centred, the three stats stacked as rows (label left, value right at 1.5 rem, MB figure right-aligned beneath at 11 px), thin dividers between rows.
- Update form: fields stacked, full width. Status message on its own line.
- Table: hide the time of day and the `Backed up` column. Cell padding 10 px 6 px, text 13 px, a larger `×` tap area, max height 420 px.
- No sideways scrolling at 360 px wide.

## 5. Not included (deliberately)

A timeline (real-time) chart mode, zoom and pan, an upload-speed or ETA estimate, a percent-complete column in the table, per-snapshot notes, Backblaze branding.

## 6. Demo data

So a new copy isn't empty and blank, editors get one extra button, `Load demo data`, that fills in a made-up history topping out at 8 TB.

**Button rules**

- Shown to editors only, and only when there are **no snapshots** or **demo snapshots are present**.
- With no snapshots it reads `Load demo data`. Pressing it adds the 22 snapshots below and shows `Demo data loaded. Clear it any time.`
- Once demo data is present it reads `Clear demo data`. Pressing it deletes **only** snapshots with `demo: true` (never real ones) and shows `Demo data cleared.` Failure: `Could not change demo data.`
- Real snapshots can be added while demo data is present; they stay when the demo is cleared. Once real snapshots exist and no demo data does, the button disappears.

**The demo snapshots.** Each has `ts` = now − *days ago* × 86,400,000 ms, `total_mb` = round(TB × 1,048,576), `remaining_mb` = round(remaining × 1,048,576), `demo: true`.

Each line is `[days ago, total TB, remaining TB]`, 22 in all:

```
[300, 5.20, 4.10]  [299, 5.20, 3.20]  [297, 5.20, 1.90]  [296, 5.20, 0.80]  [295, 5.20, 0.00]
[240, 5.45, 0.35]  [239, 5.45, 0.00]
[170, 5.90, 0.60]  [169, 5.90, 0.12]  [168, 5.90, 0.00]
[120, 6.80, 0.95]  [119, 6.80, 0.40]  [118, 6.80, 0.00]
[ 75, 7.35, 0.55]  [ 74, 7.35, 0.10]  [ 73, 7.35, 0.00]
[ 40, 7.95, 1.20]  [ 39, 7.95, 0.70]  [ 38, 7.95, 0.15]  [ 37, 7.95, 0.00]
[ 12, 8.00, 0.05]  [ 11, 8.00, 0.00]
```

The story: a first full backup at 5.2 TB, then new data added in steps, each one followed by a catch-up that ends at 0 remaining, up to 8 TB.

## 7. Building it

**In Claude, as a dashboard artifact (preferred):**

- Create it from the Dashboard type, titled `Backblaze Backup Monitor`. There are no datasets: all data lives in the artifact's own database, collection `entries`. Give the page the database, user and downloads capabilities, and let people who can edit write to `entries`.
- Read `entries` live, and redraw on every change and on resize. Draw the chart with the built-in `d3`.
- The page is plain HTML, CSS and JS: no network, no web fonts, no browser storage, no React.
- Start the database empty, show the empty states above, and add the demo button.

**Anywhere else:** build one self-contained `index.html` that opens offline. Store snapshots in `localStorage`, provide Import as well as Export (same JSON shape), draw the chart with plain SVG or any small library vendored into the file, and follow everything else in this file.

## 8. Done when

- With no data: every value shows `—`, the takeaway says `No snapshots yet…`, nothing errors, and `Load demo data` is offered.
- Loading the demo shows 22 snapshots up to 8 TB; clearing it returns to empty and leaves any real snapshots alone.
- Save a snapshot and the ring, stats, takeaway, chart and table all update.
- A snapshot with remaining 0 turns the ring and Remaining value green and adds a dot in the table.
- Remaining greater than total is refused with the exact message.
- `×` needs two clicks; Export produces the JSON shape above.
- Light and dark both read clearly; at 360 px wide nothing overlaps or scrolls sideways.
