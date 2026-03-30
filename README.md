# 🎲 Dice Job Scraper — Desktop App

> Automated Dice.com job scraper and one-click applicator with a full GUI, stealth Chrome, advanced filters, profile save/load, and Excel export.

---

## 🎬 Video Demo

> _Paste your video link below (Google Drive, Loom, YouTube, etc.)_

| | |
|---|---|
| **Demo Video** | 🎥 _[Add your video link here]_ |

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [Installation](#installation)
- [Setting Up Portable Chrome](#setting-up-portable-chrome)
- [Step-by-Step Usage Guide](#step-by-step-usage-guide)
- [Output Files](#output-files)
- [Stealth Mode Explained](#stealth-mode-explained)
- [Building to .exe (PyInstaller)](#building-to-exe-pyinstaller)

---

## Overview

**Dice Job Scraper** is a Windows desktop application that automates job searching and applying on [Dice.com](https://www.dice.com). You configure your search once — keyword, location, filters — and the tool handles everything: logging into your Dice account, paginating through results, collecting job links, and applying to each one. Easy Apply jobs are handled automatically; external jobs pause and wait for your manual action. All results are saved to a dated Excel file.

The scraper runs on top of a bundled Portable Chrome instance, injecting stealth JavaScript on every page to hide all automation fingerprints from Dice's anti-bot detection.

---

## ✨ Features

- **Full GUI** — Tkinter-based scrollable interface, no command line needed
- **Dice Login** — Logs into your Dice account automatically using your credentials
- **Advanced Filters** — Easy Apply, Posted Date, Work Settings (Remote/Hybrid/On-Site), Employment Type, Employer Type, Visa Sponsorship
- **Profile Save/Load** — Save your search configuration as a named JSON profile and reload it next time
- **Filter Confirmation Popup** — Review all applied filters in a summary dialog before scraping begins
- **Two-Phase Scraping:**
  - **Phase 1 — Collect:** Paginates through all result pages and collects job links
  - **Phase 2 — Apply:** Opens each job in a new tab, detects apply type, and acts accordingly
- **Easy Apply** — Automatically fills and submits Dice Easy Apply forms
- **External Apply Handling** — Pauses and shows a popup for manual action (Done / Skip / Stop All)
- **Already Applied Detection** — Skips jobs already applied to, logs the status
- **Tab Crash Guard** — Detects and recovers gracefully from closed or crashed job tabs
- **Browser Watcher** — Background thread detects if you manually close Chrome and auto-saves progress
- **Headless Mode** — Run without a visible browser window
- **Excel Export** — Results saved to `outputs/YYYY/Month/dice_jobs_DD_Month.xlsx`
- **Stealth Chrome** — Bundled Portable Chrome with CDP-level fingerprint spoofing
- **PyInstaller Compatible** — Packaged into a single `.exe` for easy distribution

---

## 🛠️ Tech Stack

| Component | Technology |
|---|---|
| GUI | Python `tkinter` + `ttk` |
| Browser Automation | Selenium WebDriver |
| Anti-Detection | CDP (`Page.addScriptToEvaluateOnNewDocument`), stealth JS injection |
| Chrome | Portable Chrome (bundled, no system install required) |
| Data Export | `pandas` + `openpyxl` (Excel `.xlsx`) |
| Threading | Python `threading` (scraper runs off the main GUI thread) |
| Packaging | PyInstaller (optional, for `.exe` distribution) |
| Config Storage | JSON profile files |

---

## 📁 Project Structure

```
dice-scraper/
│
├── dice_scraper.py          # Main app — GUI + scraper logic
│
├── module/
│   └── open_chrome.py       # Stealth Chrome WebDriver factory
│
├── PortableChrome/
│   ├── App/
│   │   └── Chrome-bin/
│   │       └── chrome.exe   # Portable Chrome binary
│   └── Driver/
│       └── chromedriver.exe # Matching ChromeDriver binary
│
├── profiles/                # Auto-created — saved search profiles (JSON)
├── dice_session/            # Auto-created — Chrome user data / session cache
│
└── outputs/                 # Auto-created — Excel output files
    └── 2025/
        └── January/
            └── dice_jobs_15_January.xlsx
```

---

## ⚙️ Requirements

- **Windows** (Portable Chrome is `.exe` based)
- **Python 3.8+**
- **Portable Chrome** matching your ChromeDriver version (see setup below)

### Python Dependencies

```bash
pip install selenium pandas openpyxl pyautogui
```

---

## 🚀 Installation

### 1. Clone or Download

```bash
git clone https://github.com/YOUR_USERNAME/dice-scraper.git
cd dice-scraper
```

### 2. Install Python Dependencies

```bash
pip install selenium pandas openpyxl pyautogui
```

### 3. Set Up Portable Chrome

See the [Setting Up Portable Chrome](#setting-up-portable-chrome) section below.

### 4. Run the App

```bash
python dice_scraper.py
```

---

## 🌐 Setting Up Portable Chrome

The app does **not** use your system Chrome. It requires a bundled Portable Chrome so the session, cookies, and fingerprint stay isolated.

### Step 1 — Download Portable Chrome

1. Go to [portableapps.com/apps/internet/google_chrome_portable](https://portableapps.com/apps/internet/google_chrome_portable)
2. Download and install it to a local folder.
3. Place the result so the `chrome.exe` is at:
   ```
   YourProject\PortableChrome\App\Chrome-bin\chrome.exe
   ```

### Step 2 — Download Matching ChromeDriver

1. Check the version of your Portable Chrome by opening it and going to `chrome://version`.
2. Go to [googlechromelabs.github.io/chrome-for-testing](https://googlechromelabs.github.io/chrome-for-testing/) and download the matching `chromedriver` for Windows.
3. Place it at:
   ```
   YourProject\PortableChrome\Driver\chromedriver.exe
   ```

> ⚠️ **The Chrome and ChromeDriver versions must match exactly** (same major version). Mismatches will cause the driver to fail at startup.

---

## 📖 Step-by-Step Usage Guide

### Step 1 — Launch the App

```bash
python dice_scraper.py
```

A GUI window opens. It is scrollable — scroll down to see all sections.

### Step 2 — Enter Dice Credentials

At the top of the GUI, enter your **Dice.com email and password**. Click **Show** to reveal the password while typing.

> 🔒 Credentials are used only for this session and are never written to disk.

### Step 3 — (Optional) Load a Saved Profile

If you've used the app before and saved a profile:
1. Type the profile name in the **Profile** field (e.g. `python_remote`).
2. Click **📂 Load Profile** to restore all your previous settings.

### Step 4 — Set Search Parameters

| Field | Description |
|---|---|
| **Job Keyword** | e.g. `Python Developer`, `Data Engineer`, `React` |
| **Location** | e.g. `New York, NY` or `Remote` |
| **Max Pages** | Number of result pages to paginate. Leave `0` or blank for all pages |

### Step 5 — Apply Filters

Configure any combination of filters:

| Filter | Options |
|---|---|
| **Easy Apply** | Only collect Easy Apply jobs |
| **Posted Date** | No preference / Today / Last 3 days / Last 7 days |
| **Work Settings** | Remote, Hybrid, On-Site (multi-select) |
| **Employment Type** | Full time, Part time, Contract, Third Party, Internship |
| **Employer Type** | Direct hire, Recruiter, Other |
| **Work Authorization** | Willing to sponsor |

### Step 6 — Set Output File

The output path is auto-generated as:
```
outputs/2025/January/dice_jobs_15_January.xlsx
```
Click **Browse** to choose a custom save location.

### Step 7 — (Optional) Save Your Profile

Type a name in the **Profile** field (e.g. `remote_python`) and click **💾 Save Profile** to save all current settings for future sessions.

### Step 8 — Start Scraping

Click **Start Scraping**. The progress bar activates and the status bar shows live updates.

**What happens internally:**

1. Browser launches in stealth mode
2. Logs into your Dice account
3. Applies your filters and searches for jobs
4. A **Filter Confirmation Popup** appears — review all settings and click **✅ Continue Scraping** (or cancel)
5. Phase 1: Paginates through all result pages and collects job links
6. Phase 2: Opens each job in a new tab and processes it:
   - **Easy Apply** → fills and submits the form automatically
   - **External Apply** → a popup asks: Done / Skip / Stop All
   - **Already Applied** → logged as skipped, tab closes immediately
   - **Tab Closed** → detected gracefully, skipped without crashing

### Step 9 — Stop at Any Time

Click **Stop** to halt scraping after the current job finishes. A confirmation dialog appears. All data collected up to that point is saved automatically.

### Step 10 — Review Results

When scraping completes, a summary popup shows:
- ✅ Applied
- 🖐 Manual (external apply)
- ⏭ Skipped

The Excel file is saved to your configured output path.

---

## 📊 Output Files

Each row in the Excel output represents one job. Columns included:

| Column | Description |
|---|---|
| Job Title | Job title from the listing |
| Company | Company name |
| Company Link | Link to the company profile on Dice |
| Location | Job location |
| Salary | Listed salary (if available) |
| Employment Type | Full time, Contract, etc. |
| Work Setting | Remote, Hybrid, On-Site |
| Skills | Skills listed in the job post |
| Description | Full job description text |
| Apply Status | `Applied`, `Manual`, `Skipped`, `Easy Apply Failed`, etc. |
| Apply Type | `Easy Apply`, `External`, `Unknown` |
| Job Link | Direct URL to the job posting |
| Applied Date | Date the application was processed |

Files are organized automatically by year and month:
```
outputs/
└── 2025/
    ├── January/
    │   └── dice_jobs_15_January.xlsx
    └── February/
        └── dice_jobs_03_February.xlsx
```

---

## 🥷 Stealth Mode Explained

The app uses two layers of anti-detection to avoid being flagged as a bot by Dice:

### Layer 1 — Chrome Options (launch flags)
- `--disable-blink-features=AutomationControlled` — removes the `AutomationControlled` flag
- `excludeSwitches: ["enable-automation"]` — hides the "Chrome is being controlled" banner
- `useAutomationExtension: False` — disables the automation extension
- Custom realistic User-Agent string

### Layer 2 — CDP Stealth JS (injected before every page loads)
Via `Page.addScriptToEvaluateOnNewDocument`, the following are patched on every page:
- `navigator.webdriver` → `undefined`
- `navigator.plugins` → a realistic array
- `navigator.languages` → `['en-US', 'en']`
- `window.chrome` → a realistic object with `runtime`, `loadTimes`, etc.
- `window.navigator.permissions.query` → patched for notification handling
- Selenium CDC properties (`cdc_adoQpoasnfa76pfcZLmcfl_*`) → deleted

Additionally, `Network.setUserAgentOverride` via CDP sets the `User-Agent`, `acceptLanguage`, and `platform` at the network level (not just the JS layer).

---

## 📦 Building to .exe (PyInstaller)

To distribute the app as a standalone `.exe`:

```bash
pip install pyinstaller
```

```bash
pyinstaller --onefile --windowed ^
  --add-data "PortableChrome;PortableChrome" ^
  --add-data "module;module" ^
  dice_scraper.py
```

> The `--onefile` flag bundles everything into one `.exe`. At runtime, the app detects `sys.frozen` and resolves the `PortableChrome` path from `sys._MEIPASS` (the temp extraction dir), while keeping session and output folders next to the `.exe` (writable location).

The final `dist/dice_scraper.exe` can be distributed and run on any Windows machine — no Python installation required.

---

## ⚠️ Notes & Limitations

- **Windows only** — Portable Chrome and ChromeDriver are Windows binaries.
- **Dice account required** — You must have an active Dice.com account.
- **Easy Apply forms vary** — Some multi-step or unusual Easy Apply forms may not complete successfully. These are logged as `Easy Apply Failed`.
- **External jobs require manual action** — The app pauses and waits; it does not auto-fill third-party application forms.
- **Rate limiting** — The scraper includes random delays between actions. Do not set Max Pages excessively high in a single run.
- **ChromeDriver must match Chrome version** — Update both together when upgrading.

---

## 📄 License

MIT — free to use, modify, and distribute.

---

*Built for US IT recruiting automation*
