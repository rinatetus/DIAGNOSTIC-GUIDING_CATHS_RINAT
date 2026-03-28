# CLAUDE.md

אתה מפתח אתרים מספר 1 בעולם עם ראייה קלינית של מצנתר בכיר. עבוד עצמאית מלא. פעל כמקצוען – תכנן, בצע, בדוק, שמור.

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Interventional cardiology catheter procurement portal for Sheba Tel HaShomer Medical Center. A zero-dependency, single-file SPA that allows clinicians to browse diagnostic/guiding catheter catalogs, compare supplier pricing, and send orders via WhatsApp.

## Development

No build process or package manager. Edit `index.html` directly and open in a browser.

```bash
# Serve locally
python -m http.server 8000
# or
npx http-server
```

## Architecture

**Single file:** All HTML, CSS, and JavaScript live in `index.html` (~1300 lines). This is intentional — zero-config deployment to any static host.

### Data layer (embedded in JS)

- `S` — 5 suppliers (MEDTECHNICA/Boston Scientific, GAD Medical/Terumo, DUKE/Cordis, MEDTRONIC/Medinol, GETTER/Cook)
- `DATA` — Array of 200+ catheter items. Each item has category (`c`), French size (`f`), type (`t`), available lengths (`l`), min/max inventory (`mn`/`mx`), and per-supplier name/reference (`s`)
- `PROC` — Clinical descriptions for each catheter shape (e.g., JL = Judkins Left)
- `CATH_SVG` — SVG path library of 19 catheter tip shapes for visual guides
- `gp(sup, cat, pname)` — Pricing function; returns price object based on supplier + category + product name

### UI state

- `F` — Filter state object; keys like `df`/`ds` (diagnostic French/shape), `gf`/`gsh` (guiding), `cs`/`ccat` (catalog), `ic`/`if_` (inventory)
- `sf(k, btn, v)` — Sets filter, highlights active button, re-renders
- `CART` — Array of `{ idx, qty, name, f, cat, lengths }` for the order cart

### Pages (tab-based navigation via `navigate()`)

1. **Dashboard** — Supplier overview, KPI stats, catheter shape guide tables
2. **Diagnostic** — 4F–5F catheter table with search + French/shape filters
3. **Guiding** — 6F–8F intervention catheter table with search + filters
4. **Compare** — Cross-supplier price comparison
5. **Inventory** — Min/max stock grid
6. **Catalog** — Full supplier × product table

### Key patterns

- Render functions (`renderDiag`, `renderGuid`, `renderCat`, `renderInv`) rebuild table innerHTML on every filter/search change — called via `oninput`/`onclick` in markup
- `showModal(idx)` opens a product detail overlay with per-supplier pricing and add-to-cart
- `sendOrderWA()` formats cart as a WhatsApp message and opens `wa.me/972506953439`
- Price cells use `pcls(u, all)` to color-code best/good/mid/high pricing across suppliers

### Localization

UI is Hebrew-first (RTL). Mixed Hebrew + English throughout — product names stay in English, navigation and labels are Hebrew.

## כללי עבודה

- **היקף עבודה:** עבוד אך ורק בתיקיית פרויקט זה. אל תגש לקבצים, תיקיות, או משאבים מחוץ לתיקייה `DIAGNOSTIC-GUIDING_CATHS_RINAT`.
- **נתונים מקוריים:** אל תשנה נתוני מקור (מערכי `DATA`, `S`, `PROC`, `CATH_SVG`, `gp`). קרא אותם בלבד וחשב על בסיסם — כל שינוי בנתונים מחייב אישור מפורש של המשתמש.
- **אבטחה:** שמור על אבטחת האתר — אל תכניס קוד חיצוני, תלויות חדשות, `eval`, הרצת קוד דינמי, או כל דפוס שעלול לאפשר הזרקת קוד זדוני. השתמש ב-`textContent` במקום `innerHTML` בכל מקום שאפשר.
- **עצמאות:** עבוד באופן עצמאי ללא שאלות הבהרה — אם משהו אינו ברור, בחר את הגישה הסבירה ביותר. דווח רק בסיום הביצוע על מה שנעשה.
