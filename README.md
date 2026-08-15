# Weight Tracker — PWA

An offline-capable, installable web app for tracking weight loss with lean-mass protection, smart-scale body composition, blood pressure and adaptive calorie targets.

Built for: 52-year-old male, 178 cm, starting 105 kg (BMI 33.1), desk-based with regular activity, hypertension/dyslipidemia, joint-sparing training.

**Your data never leaves your phone.** Everything you log is stored in the browser's local storage on your device. There is no account, no server, no analytics, and nothing is ever uploaded.

The one network call the app makes is the barcode lookup: when you scan a product, it sends **only that barcode number** to [Open Food Facts](https://world.openfoodfacts.org) (a non-commercial open database) and gets the nutrition values back. No weight, no food log, no identifiers. The result is cached locally, so each product is looked up once. Skip scanning and the app never touches the network at all.

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

**Android (Chrome) — recommended:**
Tap the **Install** banner the app shows, or **⋮** menu → **Install app**. It lands in your app drawer with its own icon, runs full-screen, and scanning works.

**iPhone (Safari):**
Share → **Add to Home Screen**. Everything works except barcode scanning — use search or manual entry there.

It now opens full-screen with no browser chrome, works with no signal, and appears in your app switcher like any other app.

---

## Updating it later

Upload a changed `index.html` the same way. Bump the `VERSION` string at the top of `sw.js` (currently `wt-v1.2.0` → `wt-v1.2.1`) in the same commit — that's what tells installed copies to fetch the new version instead of serving the cached one.

The app then updates on its next launch. Your logged data is untouched by updates.

---

## Backing up your data

Local storage is genuinely local: it does not sync between devices, and it is erased if you delete the app, clear site data, or reset the phone.

**Log → Backup → Export JSON** every few weeks, and always before changing phones. Save the file to iCloud/Google Drive/email. **Import** restores it — merged by date, so importing an old backup won't wipe newer entries.

**Export CSV** gives you the same data with fat mass and lean mass computed, for a spreadsheet or to hand to a doctor or dietitian.

---

## Food tracking and barcode scanning

**Built for Android.** Chrome on Android supports the native `BarcodeDetector` API, so scanning needs no third-party library — nothing to download, and it works offline. (iOS Safari still has this API disabled by default even in version 26.4, which is why this build targets Android.)

**Three ways to add food:**

1. **Scan a barcode** — camera opens, detects EAN-13/EAN-8/UPC/Code-128, looks the product up in [Open Food Facts](https://world.openfoodfacts.org), and asks only for the portion size. Torch button appears if your camera supports it.
2. **Search the library** — ~65 seeded staples with per-100 g values, searchable in English or Russian (`творог`, `гречка`, `сельдь`). Common portions are one tap (1 egg = 50 g, 1 tbsp oil = 14 g, 1 slice rye = 30 g).
3. **Enter manually** — type the values off the packet. It's saved to your list for next time, and warns you if the macros don't add up to the stated calories.

**Every scanned product is cached on your phone** the first time you look it up. Scan the same tvorog next week with no signal and it resolves instantly from local storage — verified in testing that a repeat scan makes zero network calls.

If a barcode isn't in Open Food Facts, the manual form opens with the barcode pre-filled, so entering it once fixes it permanently for you.

**The daily dashboard** shows calories left as the headline, then protein, fat and carbs as consumed / target / remaining, with overflow shown in red. Fibre is tracked against 38 g. Below that, calories split by meal — breakfast through dinner in an ordered blue ramp.

**It feeds the rest of the app.** Daily totals write into the Today tab's calorie and protein fields, so the protein-shortfall flag and the lean-mass-risk logic run on what you actually ate rather than a manual estimate.

### On accuracy

The seeded library holds **reference values per 100 g** — real brands vary, and every item is editable. Two things matter more than the database:

- **Cooking changes weight enormously.** 100 g of dry buckwheat becomes roughly 300 g cooked. Log food in the state you weighed it, and pick the matching library entry (both dry and cooked are included for the common ones).
- **Open Food Facts is crowd-sourced.** Most entries are accurate; some aren't. If a scanned product's numbers look wrong, edit them — your version is what gets saved.

---

## The €100/week meal plan, built in

The **Food tab** opens with today's plan — the four recipes scheduled for that weekday, each with its calories and macros. Tap **Log** on one, or **Log the whole day** for all four. The `i` button shows the full ingredient list with gram weights before you commit.

Logging a recipe adds **each ingredient as its own entry**, not one lump, so if you ate 200 g of potato instead of 320 g you can fix that one line without redoing the meal.

The **Plan tab** carries the shopping list — 30 items grouped by shop with running subtotals, €87.31 total. Tap to tick items off; it remembers between visits. ✓ marks a price verified against a live product page; the rest are estimates and move with weekly *aanbiedingen*.

The library gained the Dutch staples the plan needs (magere kwark, kipdijfilet, volkorenbrood, zilvervliesrijst, broccoli, spinazie, canned pulses) and Dutch search aliases on the existing ones — `kwark`, `kipfilet`, `havermout`, `aardappel` all resolve now, alongside the Russian names.

> **Magere kwark is not tvorog.** Dutch kwark is ~9 g protein per 100 g; Russian tvorog is ~18 g because it's drained much further. Both are in the library as separate entries — pick the one you actually bought, or the protein maths will be out by a factor of two.

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
