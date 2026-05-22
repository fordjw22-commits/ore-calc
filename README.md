[README (1).md](https://github.com/user-attachments/files/28154206/README.1.md)
# Ore & Concentrate Lot Calculator

A browser-based P&L calculator for non-ferrous and precious metal ore lots. Built for internal use by an international ore trader operating out of Zambia.

No server, no install, no dependencies. One HTML file.

---

## What it does

- Pulls **live LME settlement prices** for Cu, Pb, Zn automatically on page load (no key needed)
- Pulls **live Au and Ag spot prices** via metals.dev (free API key required)
- Pulls **live ZMW/USD mid-market rate** automatically (no key needed)
- Displays **bank buy and sell rates** derived from the mid-rate using an adjustable spread
- Calculates full lot P&L: wet/dry weight conversion, assay, payability, CIF value, all-in costs, Lusaka/London split
- Handles **mixed-metal concentrates** — primary metal plus byproduct credits (e.g. Cu lot with Ag and Au credits)
- Payability uses the **D24 sliding scale formula** from the original Excel model: `(base + (assay − threshold)) / 100`
- All ZMW-denominated costs convert to USD using the live mid-rate
- Inputs persist in the browser between sessions

---

## Metals covered

| Metal | Symbol | Assay unit | Price source |
|---|---|---|---|
| Copper | Cu | % | Westmetall (LME cash settlement) |
| Lead | Pb | % | Westmetall (LME cash settlement) |
| Zinc | Zn | % | Westmetall (LME cash settlement) |
| Gold | Au | g/t | metals.dev (spot) |
| Silver | Ag | g/t | metals.dev (spot) |

---

## Price sources

| Data | Source | Key required | Cost |
|---|---|---|---|
| Cu, Pb, Zn LME prices | [westmetall.com](https://www.westmetall.com/en/markdaten.php) | No | Free |
| Au, Ag spot prices | [metals.dev](https://metals.dev) | Yes (free) | Free (100 req/mo) |
| ZMW/USD FX rate | [ExchangeRate-API](https://www.exchangerate-api.com) | No | Free |
| BoZ official rate (reference) | [boz.zm](https://www.boz.zm/markets-securities/boz-exchange-rates) | No | Free |

Cu, Pb, and Zn work immediately with zero setup. Au and Ag require a free metals.dev key.

---

## Setup & deployment

### Step 1 — Get a metals.dev API key (for Au / Ag)

1. Go to [metals.dev](https://metals.dev)
2. Click **Get Free API Key** — no credit card required
3. Copy your key from the dashboard

> Skip if only trading Cu, Pb, or Zn.

### Step 2 — Upload to this GitHub repository

1. On the repo main page click **Add file** → **Upload files**
2. Drag in `index.html`
3. Click **Commit changes**

> Important: the file must be named exactly `index.html` (lowercase, no spaces, no numbers).
> If your browser saves it as `index (1).html`, rename it before uploading.

### Step 3 — Enable GitHub Pages

1. Go to **Settings** → **Pages**
2. Source: **Deploy from a branch**
3. Branch: `main`, folder: `/ (root)`
4. Click **Save**

Live at:
```
https://YOUR-GITHUB-USERNAME.github.io/ore-calc/
```

GitHub shows the exact URL in Settings → Pages after ~1 minute.

### Step 4 — First use

1. Open the URL
2. Paste your metals.dev API key into the key field → click **connect**
3. Key is saved in the browser — only needs to be entered once per device

---

## Updating the app

When a new version of `index.html` is available:

1. Go to the repo → click `index.html` → click the pencil (edit) icon
2. Select all, paste in the new file contents
3. Click **Commit changes**

Live within ~30 seconds.

---

## Calculation logic

### Dry weight
```
dmt = wet_weight × (1 − moisture%)
```

### Payability (D24 sliding scale)
```
payability = (base + (assay − threshold)) / 100
```
Default for Cu: base = 70%, threshold = 8%
Example: 10.67% assay → (70 + (10.67 − 8)) / 100 = 72.67%

Base and threshold are editable in the app for different contract terms.

### Primary metal value per dmt
```
value = LME_price × (assay / 100) × payability          [base metals, Cu/Pb/Zn]
value = spot_price × (assay_g_t / 31.1035) × payability  [precious metals, Au/Ag]
```

### All-in cost per dmt
All ZMW costs converted to USD using live mid-rate:
```
total_cost = (ore_price + mine_ops + inland_freight + port_handling +
              ocean_freight + export_clearance + SGS) × (1 / ZMW_per_USD)
```

### P&L
```
P/L per dmt = CIF value − all-in cost
P/L for lot = P/L per dmt × dmt
```

### P&L split
Lusaka: 33% · London/Beijing: 67% · Annualised: × 8 lots/year
(Edit these constants in the JS if terms change)

### ZMW bank rates
```
buy rate  = mid × (1 − spread/2)   [bank buys USD from you]
sell rate = mid × (1 + spread/2)   [bank sells USD to you]
```
Default spread: 2% — adjust to match your bank's actual rate.

---

## Mine ops cost breakdown (ZMW/t)

The default mine ops figure of 230.90 ZMW/t is the sum of:

| Line item | ZMW/t |
|---|---|
| Mine crush site TPT | 93.00 |
| Operators | 35.50 |
| Loading | 20.00 |
| Washing | 16.80 |
| Crushing | 18.20 |
| Operators (2nd) | 27.00 |
| Manual labor | 7.40 |
| Water pump | 13.00 |
| **Total** | **230.90** |

Update as costs change — all fields are editable in the app.

---

## Default lot values

Defaults are pre-loaded from the April 2026 Zambia Cu milled ore lot:

| Input | Default | Source |
|---|---|---|
| Wet weight | 300 wmt | Original lot |
| Moisture | 2% | Original lot |
| Cu assay | 10.67% | Original lot |
| Ore purchase price | 9,530 ZMW/t | Original lot |
| Petauke → Beira freight | 1,425 ZMW/t | Original lot |
| Beira port handling | 693.5 ZMW/t | Original lot |
| Beira → PRC CIF | 855 ZMW/t | Original lot |
| ZRA export clearance | 200 ZMW/t | Original lot |
| SGS / assay | 373 ZMW/t | Original lot |
