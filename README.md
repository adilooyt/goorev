# goorev

A lightweight, static NFC/QR redirect system for Google Reviews — hosted on GitHub Pages with zero backend and zero running cost.

When a customer taps an NFC card or scans a QR code, they see a branded 5-second countdown page that smoothly redirects them to the correct Google Reviews form. Anonymous analytics are fired in the background via a Google Apps Script webhook.

**Live demo:**
```
https://adilooyt.github.io/goorev/?id=SP1
```

---

## How it works

```
NFC tag / QR code
       │
       ▼
flash-redirect-analytics.html?id=SP1
       │
       ├─ Resolves shop name + review URL from SHOP_LOOKUP
       ├─ Shows branded countdown UI (5 seconds)
       ├─ Fires anonymous analytics event
       │
       ▼
Google Reviews page (specific to that shop)
```

---

## File structure

```
goorev/
├── index.html       # Main redirect page (the whole app lives here)
├── admin.html       # Local-only shop manager (gitignored)
├── .gitignore
└── README.md
```

> `admin.html` is excluded from the repo via `.gitignore`. It is a local tool only and should never be deployed.

---

## Shop management

Shops are defined in the `SHOP_LOOKUP` object inside `flash-redirect-analytics.html`:

```js
const SHOP_LOOKUP = {
  "SP1": { shop: "Aligarh Muslim University", link: "https://search.google.com/local/writereview?placeid=ChIJUUu8vOWkdDkRmOnQMzdx2to" },
  "SP2": { shop: "Shop Name",                 link: "https://search.google.com/local/writereview?placeid=YOUR_PLACE_ID" },
  // add more entries as needed
};
```

Each entry maps a short **Shop ID** (e.g. `SP1`) to a shop name and its Google Review URL.

### Adding or editing shops

Use `admin.html` locally:
1. Open `admin.html` in your browser
2. Enter your GitHub token, owner, repo, and branch
3. Click **Load shops from GitHub**
4. Add, edit, or delete entries using the form
5. Click **Publish changes to GitHub** — the file is committed automatically

Alternatively, edit `flash-redirect-analytics.html` directly on GitHub mobile.

---

## URL parameters

| Parameter | Purpose | Security |
|-----------|---------|----------|
| `?id=SP1` | Recommended. Resolves shop from the controlled `SHOP_LOOKUP` map | Safe — server-controlled |
| `?shop=Name` | Overrides the display name only. Does not affect redirect destination | Safe |
| `?link=https://...` | Overrides redirect URL. Only honored for allowlisted Google domains | Validated |

### Allowlisted domains for `?link=`

- `search.google.com/local/writereview?placeid=...`
- `www.google.com/maps/...`
- `google.com/maps/...`
- `maps.google.com/...`

Non-HTTPS links and any non-Google domains are silently blocked — the page falls back to the default destination.

---

## NFC tag URL format

Program your NFC tag or QR code with:

```
https://adilooyt.github.io/goorev/?id=SP1
```

Replace `SP1` with the correct Shop ID for each location.

---

## Analytics

Analytics events are sent via `navigator.sendBeacon` (with a `fetch` fallback) to a Google Apps Script endpoint. No client secrets are used. The payload is:

```json
{
  "timestamp": "2026-07-10T00:00:00.000Z",
  "event": "shop_redirect",
  "shopId": "SP1",
  "resolution": "id_lookup",
  "source": "auto",
  "targetHost": "search.google.com"
}
```

To disable analytics, set `ANALYTICS_WEBHOOK = ""` in the config section of `flash-redirect-analytics.html`.

---

## GitHub Pages setup

1. Go to **Repository Settings → Pages**
2. Source: **Deploy from a branch**
3. Branch: `main`, folder: `/ (root)`
4. Save — the site goes live within 1–3 minutes

---

## Security notes

- The `?link=` parameter is validated against a strict Google domain allowlist before use
- Non-HTTPS URLs are always rejected
- The CSP header blocks all external scripts, frames, and form actions
- `admin.html` is local-only and never pushed to the repository
- The GitHub token used by `admin.html` is stored in `localStorage` of your local browser only
