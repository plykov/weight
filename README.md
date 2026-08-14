# Weight Tracker — PWA

An offline-capable, installable web app for tracking weight loss with lean-mass protection, smart-scale body composition, blood pressure and adaptive calorie targets.

Built for: 52-year-old male, 178 cm, starting 105 kg (BMI 33.1), desk-based with regular activity, hypertension/dyslipidemia, joint-sparing training.

**Data never leaves your phone.** Everything is stored in the browser's local storage on your device. There is no account, no server, no analytics, no network calls of any kind after the first load.

---

## Deploying to `github.com/plykov/weight`

Everything below is doable from the GitHub website — no command line, no git install.

### 1. Upload the files

1. Go to <https://github.com/plykov/weight>
2. Click **Add file → Upload files**
3. Drag in all six files from this folder:
   - `index.html`
   - `manifest.webmanifest`
   - `sw.js`
   - `icon-192.png`
   - `icon-512.png`
   - `apple-touch-icon.png`
4. Scroll down, click **Commit changes**

> Upload the files themselves, not the folder containing them — they must sit at the repository root so that `index.html` is at `plykov/weight/index.html`.

### 2. Turn on GitHub Pages

1. In the repo, click **Settings** (top bar)
2. **Pages** in the left sidebar
3. Under **Build and deployment → Source**, choose **Deploy from a branch**
4. Branch: **main**, folder: **/ (root)** → **Save**
5. Wait 1–2 minutes, then refresh the page. GitHub will show your live URL:

```
https://plykov.github.io/weight/
```

> The repository must be **public** for GitHub Pages on a free account. The app itself sends nothing anywhere, so a public repo exposes the code — not your data. If you'd rather keep it private, GitHub Pages on private repos requires a paid plan; Netlify Drop (<https://app.netlify.com/drop>) is a free alternative that gives you an HTTPS URL from a drag-and-drop with no repo at all.

### 3. Install it on your phone

Open `https://plykov.github.io/weight/` in your phone's browser.

**iPhone (Safari — this must be Safari, not Chrome):**
Tap the **Share** button (square with an arrow) → scroll down → **Add to Home Screen** → **Add**.

**Android (Chrome):**
Either tap the **Install** banner the app shows you, or use the **⋮** menu → **Install app** / **Add to Home screen**.

It now opens full-screen with no browser chrome, works with no signal, and appears in your app switcher like any other app.

---

## Updating it later

Upload a changed `index.html` the same way. Bump the `VERSION` string at the top of `sw.js` (e.g. `wt-v1.0.0` → `wt-v1.0.1`) in the same commit — that's what tells installed copies to fetch the new version instead of serving the cached one.

The app then updates on its next launch. Your logged data is untouched by updates.

---

## Backing up your data

Local storage is genuinely local: it does not sync between devices, and it is erased if you delete the app, clear site data, or reset the phone.

**Log → Backup → Export JSON** every few weeks, and always before changing phones. Save the file to iCloud/Google Drive/email. **Import** restores it — merged by date, so importing an old backup won't wipe newer entries.

**Export CSV** gives you the same data with fat mass and lean mass computed, for a spreadsheet or to hand to a doctor or dietitian.

---

## What the app does with your numbers

- **7-day rolling average** is the primary weight figure everywhere. Daily readings vary ±1.5 kg on water alone and are shown only as faint dots.
- **Adaptive calorie target** recalculates as you get lighter, and once you've entered a body-fat reading it sets protein from your *measured* lean mass (2.0 g/kg) rather than a formula.
- **Fat share of loss** is the headline body-composition metric: of every kilogram gone, what proportion came from fat. Target 80%+; below 65% triggers a warning.
- **Automatic flags** fire for: losing too fast on too little training, lean-mass loss, plateaus, sleep debt, protein shortfall, alcohol load, and blood pressure dropping low enough to warrant a medication review.

### Smart-scale readings

Consumer impedance scales are poor at *absolute* body fat — even a validated multi-frequency device read men **4.2 percentage points low** versus DXA — but excellent at *tracking change* (test–retest ICC 0.987–0.995). Same time of day, after the toilet, before food or drink, bare dry feet, never after training or alcohol. Weekly, not daily.

Bone mass barely moves over months and is largely derived from the same impedance model. Log it if you like; don't let it drive decisions.

---

**Not medical advice.** Targets are model estimates and individual energy requirements vary ±10–15%. Blood pressure falls as weight falls — if you take antihypertensive medication, monitor at home and keep your doctor informed, since doses often need reducing. Discuss the training plan with a clinician before starting.
