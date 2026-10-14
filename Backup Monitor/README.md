# Backup Progress Monitor

> A lightweight, privacy-focused, zero-backend web dashboard to track offsite backup progress over time.

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)
![Zero Dependencies](https://img.shields.io/badge/Dependencies-Zero%20(Vendored)-emerald.svg)
![Privacy: 100% Offline](https://img.shields.io/badge/Privacy-100%25%20Offline-success.svg)

---

## Overview

**Backup Progress Monitor** is a standalone, single-page web application designed to track and visualize data backup progression. By logging periodic snapshots of your total dataset size and remaining bytes to upload, it generates visual progress rings, completion percentages, capacity metrics, and multi-view interactive charts.

Designed from the ground up for **absolute privacy and zero maintenance**:
* **100% Offline & Private:** Zero external network calls, zero tracking, zero CDNs, and zero third-party APIs. All chart and gesture libraries are vendored locally.
* **Zero Build Step:** Pure vanilla HTML, CSS, and modern JavaScript. No Node.js, npm, webpack, or compiler required. Double-click `app/index.html` in Safari, Chrome, Firefox, or Edge to run.
* **Tool-Agnostic:** Works with any backup software, cloud sync agent, or replication tool that reports total and remaining size:
  * [Backblaze](https://www.backblaze.com) Personal Computer Backup
  * [restic](https://restic.net)
  * [rclone](https://rclone.org)
  * macOS Time Machine
  * [Duplicati](https://www.duplicati.com)
  * [BorgBackup](https://www.borgbackup.org)
  * Storage pool / RAID resilvering jobs

---

## Features

* **Visual Progress Overview:** Radial completion gauge, dynamic percentage calculator, and auto-converting metrics in MB, GB, and TB.
* **Dual Chart View Modes:**
  * **Timeline View:** Plots snapshots on a continuous real-time calendar axis to visualize speed changes and time gaps.
  * **Absolute View:** Plots snapshots with equal-width slots, ideal for sequential review regardless of logging intervals.
* **Pan & Zoom:** Interactive touch and scroll zoom powered by Chart.js and Hammer.js.
* **Portable JSON Import & Export:** One-click backup and restore of your history. Your data never leaves your machine.
* **Preloaded Demo Dataset:** Test and preview all dashboard features immediately with a realistic 22-snapshot historical dataset (simulating backup growth up to 8 TB).
* **Adaptive Dark & Light Theme:** Automatic system-preference detection with a manual toggle.
* **Dynamic Digit Grouping:** Clean number formatting with live conversion tags in MB, GB, and TB.

---

## Quick Start

### 1. Run Locally (Desktop)
1. Clone or download this repository:
   ```bash
   git clone https://github.com/your-username/backup-progress-monitor.git
   ```
2. Open `app/index.html` in any web browser:
   ```bash
   open app/index.html
   ```
3. Click **Import Backup** and select `demo-data.json` to explore sample charts, or enter your own figures and click **Save Snapshot**.

### 2. Deploy to GitHub Pages
Because the repository requires no build pipeline, you can host your own private or public instance on GitHub Pages in seconds:
1. Fork or push this repository to GitHub.
2. Go to **Settings ➔ Pages**.
3. Under **Build and deployment**, select `Deploy from a branch` and set the folder to `/app` (or `/` with a redirect).
4. Save, and your monitor will be live at `https://your-username.github.io/backup-progress-monitor/app/`.

---

## Data Model & JSON Schema

All snapshot records are stored in browser `localStorage` under the key `b2calc`.

Exported backups follow a simple, human-readable JSON schema:

```json
{
  "version": 2,
  "totalBackup": 8388608,
  "remainingBackup": 0,
  "logs": [
    {
      "ts": 1740000000000,
      "totalBackup": 5452595,
      "remainingBackup": 4301987
    },
    {
      "ts": 1740100000000,
      "totalBackup": 5452595,
      "remainingBackup": 0
    }
  ]
}
```

* `version`: Integer schema version (`2`).
* `totalBackup`: Latest snapshot total size in MB.
* `remainingBackup`: Latest snapshot remaining size in MB.
* `logs`: Chronological array of snapshots:
  * `ts`: Unix epoch timestamp in milliseconds.
  * `totalBackup`: Total dataset size in MB.
  * `remainingBackup`: Remaining data to upload in MB.

---

## Project Structure

```
backup-progress-monitor/
├── README.md               # Project documentation & guide (this file)
├── LICENSE                 # Open-source MIT License
├── .gitignore              # Git ignore rules for OS & temporary exports
├── BLUEPRINT.md            # Architectural blueprint specification
├── SKILLS.md               # AI agent prompt instructions to recreate artifact
├── demo-data.json          # Standalone 22-snapshot sample dataset (up to 8 TB)
└── app/                    # Self-contained application directory
    ├── index.html          # Standalone responsive web dashboard
    ├── chart.umd.min.js    # Chart.js v4.5.1 (vendored)
    ├── chartjs-adapter-date-fns.bundle.min.js  # Date adapter (vendored)
    ├── chartjs-plugin-zoom.min.js              # Zoom/pan plugin (vendored)
    └── hammer.min.js       # Touch gesture support (vendored)
```

---

## Contributing

Contributions, bug reports, and suggestions are welcome! Feel free to open an issue or submit a pull request.

---

## License & Credits

Created by [Raghav Bhasin](https://theraghavbhasin.com) / [Brewing Studio](https://brewing.studio) for personal use, open sourced for everyone.

This project is licensed under the [MIT License](LICENSE) — free to use, modify, and distribute for personal and commercial purposes.

*Disclaimer: This project is an independent open-source utility and is not affiliated with, endorsed by, or sponsored by Backblaze, Apple, or any other trademark owner.*
