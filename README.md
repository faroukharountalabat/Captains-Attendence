[README.md](https://github.com/user-attachments/files/27569082/README.md)
# Talabat Attendance System — Deployment Guide

## What's included

| File | Purpose |
|------|---------|
| `app/index.html` | The full PWA app (coordinator + rider) |
| `app/manifest.json` | PWA install manifest |
| `app/sw.js` | Service worker (offline support) |
| `gas/Code.gs` | Google Apps Script backend |

---

## Step 1 — Set up Google Sheets

1. Go to https://sheets.google.com and create a new spreadsheet.
2. Copy the **Sheet ID** from the URL:
   `https://docs.google.com/spreadsheets/d/**SHEET_ID**/edit`

---

## Step 2 — Deploy Google Apps Script

1. In your Google Sheet, go to **Extensions → Apps Script**
2. Delete all existing code
3. Paste the full contents of `gas/Code.gs`
4. Replace `YOUR_GOOGLE_SHEET_ID_HERE` with your actual Sheet ID
5. Click **Save** (Ctrl+S)
6. In the function dropdown, select `setupSheets` and click **Run**
   - Accept permissions when prompted
   - This creates all sheet tabs with correct headers
7. Click **Deploy → New deployment**
   - Type: **Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
8. Click **Deploy** and copy the **Web App URL**
   (looks like: `https://script.google.com/macros/s/AKfycb.../exec`)

---

## Step 3 — Host the app

### Option A — GitHub Pages (free, recommended)
1. Create a free GitHub account at github.com
2. Create a new repository called `talabat-attendance`
3. Upload all files from the `app/` folder to the repository root
4. Go to **Settings → Pages → Source: main branch**
5. Your app URL will be: `https://yourusername.github.io/talabat-attendance`

### Option B — Netlify (free, drag & drop)
1. Go to https://netlify.com and sign up free
2. Drag the `app/` folder onto the Netlify dashboard
3. Your app gets a URL like `https://random-name.netlify.app`

### Option C — Any web server
Upload all files from `app/` to your server's public directory.
The app only needs standard HTML/JS/CSS hosting — no backend required.

---

## Step 4 — Configure the app

1. Open the app URL in any browser or Android phone
2. Log in as **Coordinator** with any PIN (default: `1234`)
3. Go to the **Setup** tab
4. Paste your **Apps Script Web App URL**
5. Set your coordinator **PIN**
6. Tap **Connect & save**

From this point, all attendance and schedule changes sync to Google Sheets in real time.

---

## Step 5 — Install on Android phones

### For riders:
1. Open the app URL in **Chrome** on Android
2. Chrome shows a banner: **"Add to Home Screen"**
3. Tap it — the app installs like a native app with the Talabat icon
4. Riders open it from their home screen and log in as "Rider"

### For coordinators:
Same process — log in as Coordinator with the PIN.

---

## Google Sheet structure

After running `setupSheets()`, your spreadsheet will have 4 tabs:

| Sheet | Contents |
|-------|---------|
| **Riders** | All rider names, zones, base shifts |
| **Zones** | Zone GPS coordinates and radius |
| **Schedule** | Weekly shift assignments (editable from app) |
| **Attendance** | Daily check-ins with GPS, time, status |

You can edit rider names and zones directly in the **Riders** sheet.
You can adjust zone GPS center and radius in the **Zones** sheet.

---

## Shift rules

| Shift | Start time | Notes |
|-------|-----------|-------|
| Morning | 9:00 AM | Most zones |
| Morning early | 8:00 AM | Zone 4 (Nasr City) |
| Night | 4:00 PM | |
| Cover | 12:00 PM | When partner is on day off |
| Day off | — | 1 day per week per rider |

**Grace period:** 30 minutes — arriving within 30 min of shift start = On Time.

---

## Anti-fake GPS

The rider app detects mock/spoofed GPS by checking location accuracy.
Real GPS has accuracy of 5–50 meters. Mock GPS apps typically report
accuracy of 0–2 meters (suspiciously perfect). If detected, check-in is blocked.

For stronger enforcement, consider enabling Android's **Developer Options → 
Mock location app** restriction via MDM policy on company phones.

---

## Troubleshooting

**"Saved locally only" message:**
→ The Apps Script URL is not configured. Go to Setup tab and paste the URL.

**"Permission denied" when deploying Apps Script:**
→ Make sure you selected "Execute as: Me" and clicked Accept on the permissions dialog.

**Riders can't find their shift:**
→ The name lookup is by first name. Make sure the rider enters their name exactly as it appears in the Riders sheet.

**GPS keeps failing:**
→ The rider must allow location permissions in their browser/Chrome settings.
→ On Android: Settings → Apps → Chrome → Permissions → Location → Allow.
