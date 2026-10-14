---
name: backup-progress-monitor-oss
description: Build, customize, or package the open-source Backup Progress Calculator dashboard artifact. Use when an AI agent needs to generate or update the neutral-branded offline backup telemetry dashboard with Slate and Indigo styling, MIT licensing, and creator credits.
---

# Backup Progress Monitor (Open Source)

Instructions for an AI agent to build, customize, or package the open-source Backup Progress Calculator dashboard as a self-contained web artifact or standalone repository tool.

## When to Use

- Generating or recreating the open-source Backup Progress Calculator artifact in an AI sandbox or workspace.
- Adding features, updating visualization controls, or packaging open-source distributions.
- Verifying neutral brand styling, zero-PII sanitization, offline compliance, and creator credit links.

## Core Architectural Specifications

### 1. Visual Design and Brand Identity
- **App Title**: `Backup Progress Calculator`.
- **Subtitle**: `Track how much of your data is backed up offsite, over time`.
- **Palette**: Neutral Slate and Indigo theme.
  - Light mode: `--bg: #f7f5f6; --bg-grad: radial-gradient(circle at 15% -10%, #eef2ff 0%, #f8fafc 45%); --text: #0f172a; --muted: #64748b; --accent: #4f46e5; --accent2: #6366f1; --accent-light: #eef2ff; --border: #e8e4e5;`.
  - Dark mode: `--bg: #0a0a12; --bg-grad: radial-gradient(circle at 20% -10%, #1e1b4b 0%, #0a0a12 45%), radial-gradient(circle at 90% 10%, #0a0a24 0%, transparent 40%); --text: #f3eef0; --muted: #9c959d; --accent: #818cf8; --accent2: #a5b4fc; --accent-light: rgba(129, 140, 248, 0.15); --border: #2b2730;`.
  - Semantic colors: Success `--success: #16a34a` (light) / `#4ade80` (dark); Danger `--danger: #ef4444` (light) / `#f87171` (dark).
- **Typography**: System font stack (`-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif`). Zero external Google Fonts.
- **Required Footer Credit**:
  ```html
  <footer class="app-footer" style="text-align: center; font-size: 0.78rem; color: var(--muted); margin-top: 32px; padding-top: 16px; border-top: 1px solid var(--border);">
      <p style="margin: 0; font-weight: 500;">Created by <a href="https://theraghavbhasin.com" target="_blank" rel="noopener noreferrer" style="color: var(--accent); text-decoration: none;">Raghav Bhasin</a> / <a href="https://brewing.studio" target="_blank" rel="noopener noreferrer" style="color: var(--accent); text-decoration: none;">Brewing Studio</a> for personal use, open sourced for everyone.</p>
  </footer>
  ```

### 2. Layout Structure
- **Container**: Centered, max-width 1100px with 16px block spacing.
- **Header**: Centered title, generic subtitle, and `#themeToggle` button in the top right.
- **Top Grid (Two-Column Responsive)**:
  - **Overview Hero Card**: Radial SVG progress ring (radius 54px, circumference 339.29px) displaying percentage in center, flanked by three metric blocks: Total Data, Backed Up, and Remaining.
  - **Status Input Card**: Inputs for `Total Backup (MB)` and `Remaining Backup (MB)` with live conversion tags (`GB / TB`), action buttons (`Save Snapshot`, `Export Backup`, `Import Backup`), and hidden file input.
- **Interactive Chart Card**: Full-width canvas (`#progressChart`) with view mode segmented toggle (`Timeline` vs `Absolute`), reset zoom button, and descriptive hint text.
- **Saved Snapshots Card**: Collapsible drawer header (`Saved Snapshots` with animated chevron) wrapping a scrollable table with inline row editing and deletion.

### 3. Data Model and Privacy Boundaries
- **Storage Model**: Persists snapshots to browser `localStorage.getItem('b2calc')`:
  ```json
  {
    "totalBackup": 8388608,
    "remainingBackup": 0,
    "logs": [
      {
        "ts": 1740000000000,
        "totalBackup": 5452595,
        "remainingBackup": 4301987
      }
    ]
  }
  ```
- **Zero PII Requirement**: The open-source repository distribution must never bundle personal backup timestamps, drive paths, or private snapshot files.
- **Demo Data Support**: Allow importing clean sample data (`demo-data.json`) to demonstrate chart behavior on fresh installs.
- **JSON Portability**: Supports standardized JSON export and import for user backups.

## Step-by-Step Implementation Workflow

### Step 1: Initialize Offline State and DOM Elements
1. On `DOMContentLoaded`, detect theme from `localStorage.getItem('b2calc-theme')` or system color scheme.
2. Initialize dashboard state via `loadState()`. Populate latest numbers into input fields and trigger live conversions.
3. Attach input listeners: `input` updates live estimates, `blur` triggers `formatNumberInput`, and `keydown` on Enter triggers `saveSnapshot`.

### Step 2: Render Overview Ring and Hero Stats
1. Calculate derived values: `uploaded = max(total - remaining, 0)` and `percent = (uploaded / total) * 100`.
2. Compute SVG circle stroke offset: `offset = RING_CIRCUMFERENCE * (1 - percent / 100)`. Apply pulse animation when progress changes.
3. Populate metric blocks: format TB with 2 decimal places and MB underneath.

### Step 3: Configure Multi-View Chart.js Logic
1. Load vendored libraries from relative paths: `chart.umd.min.js`, `chartjs-adapter-date-fns.bundle.min.js`, `hammer.min.js`, and `chartjs-plugin-zoom.min.js`.
2. Implement two x-axis modes:
   - **Absolute Mode (Default)**: Equal-width category slots per snapshot. Shows sequential progression regardless of elapsed time.
   - **Timeline Mode**: Continuous time adapter axis (`type: 'time'`) reflecting real temporal gaps between backups.
3. Dataset styling:
   - `Total`: Neutral dashed line (`borderDash: [5, 5]`), color `#78716c` (light) / `#a8a29e` (dark), zero fill.
   - `Uploaded`: Solid accent line, point radius 3px (hover 6px), soft vertical gradient fill.
4. Interaction: Configure tooltip `mode: 'index'`, `intersect: false`. Enable wheel/pinch zoom and pan with a Reset Zoom control.

### Step 4: Build Snapshot Table with Cluster Shading
1. Sort snapshots in descending order (newest first).
2. Calculate delta against chronologically preceding snapshot:
   - Remaining decreased: display green pill (`-X GB` / `-X TB`).
   - Remaining increased: display red pill (`+X GB`).
   - No change: display muted pill (`0 MB`).
3. Cluster shading: Apply alternating row group class (`row-group-b`) when the time gap between snapshots exceeds a rolling 48-hour threshold.
4. Inline editing: Allow clicking Edit to convert Total and Remaining cells into input fields with Save and Cancel buttons.
5. Deletion: Provide two-step confirmation before removing a snapshot from state.

## Gotchas and Guidelines

- **Exact Credit Links**: Always include the exact footer credit linking `https://theraghavbhasin.com` and `https://brewing.studio`.
- **Feature Parity**: Layout, table controls, chart behavior, and inline editing must remain 100% identical to the personal edition.
- **Tool-Agnostic Phrasing**: Keep documentation and tooltips generic so users can track Backblaze, restic, rclone, or Time Machine backups.
- **Zero Network Calls**: Never add external scripts or CDN fonts. Ensure the dashboard functions completely offline.
