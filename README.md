# goorev

Static GitHub Pages flash redirect page for NFC/QR to Google Reviews.

## Main file
- `flash-redirect-analytics.html`

## Test URLs
- https://adilooyt.github.io/goorev/flash-redirect-analytics.html?id=SP1
- https://adilooyt.github.io/goorev/flash-redirect-analytics.html?shop=Shop%201&link=https%3A%2F%2Fsearch.google.com%2Flocal%2Fwritereview%3Fplaceid%3DPLACE_ID_SP1

## Secure URL usage
- Recommended: `?id=SP1` (uses controlled `SHOP_LOOKUP` mapping in the page).
- Optional `?link=` is only honored when it is an **allowlisted HTTPS Google review URL**.
- Non-HTTPS links and non-allowlisted hosts are ignored and the page safely falls back.
- `?shop=` only changes display text and does not bypass redirect restrictions.

Allowed `?link=` examples:
- `https://search.google.com/local/writereview?placeid=...`
- `https://www.google.com/maps/...`
- `https://google.com/maps/...`
- `https://maps.google.com/...`

Blocked examples:
- `http://search.google.com/local/writereview?placeid=...` (non-HTTPS)
- `https://example.com/phish` (non-allowlisted host)

## GitHub Pages setup
1. Repository Settings → Pages
2. Source: Deploy from a branch
3. Branch: `main`, folder `/ (root)`
4. Save and wait 1–3 minutes
