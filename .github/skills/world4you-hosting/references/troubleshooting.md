# Troubleshooting — World4You

## ERR_CONNECTION_REFUSED

**Symptom:** Browser shows "This site can't be reached" / ERR_CONNECTION_REFUSED.

**Causes and fixes:**

| Cause | Fix |
|-------|-----|
| Files uploaded to FTP root `/` instead of `wwwroot/` | Move all files into `wwwroot/` |
| "Root-Verzeichnis" not set in control panel | Set it to `wwwroot` in World4You control panel → Webhosting |
| DNS not propagated yet | Wait up to 24 h after changing DNS or adding a domain |
| Corporate / VPN network blocking the domain | Test on mobile data (not WiFi) — if it works there, it's a local network issue |

**Diagnostic:** If the site works on mobile but not on your laptop, it's almost certainly a corporate network / DNS issue, not a server problem.

## CSS not loading / old styles showing

**Symptom:** Page loads but looks unstyled or shows the old design.

1. **Hard refresh:** Ctrl+Shift+R (Windows) / Cmd+Shift+R (Mac) — clears browser cache
2. **Check the path:** CSS must be at `wwwroot/css/style.css`, not `wwwroot/style.css`
3. **Check the HTML link tag:**
   ```html
   <link rel="stylesheet" href="css/style.css">
   ```
   Path is relative — works from `index.php` in `wwwroot/`.

## Posts not loading ("Loading posts…" stays forever)

**Most likely cause:** JavaScript crash before `loadLinkedInPosts()` is reached.

1. Open browser DevTools → **Console**
2. Look for a red error. Common one:
   ```
   TypeError: Cannot read properties of null (reading 'getContext')
   ```
   This means old particle-canvas code is referencing a `<canvas>` element that was removed from the HTML. Fix: remove the canvas code from `main.js`.
3. If no JS error: check the **Network** tab for a failed request to `data/posts.json` (404 means the file wasn't uploaded).

## Photo not showing / oval photo ring

**Photo not showing:**
- PHP detection checks for `portrait.png`, `portrait.jpg`, `portrait.jpeg`, `portrait.webp` in `img/` — filename must match exactly
- Upload the photo to `wwwroot/img/` (not just `wwwroot/`)

**Photo ring appears oval instead of circular:**
```css
.hero-photo-ring {
  aspect-ratio: 1 / 1;
  min-width: 380px;
  min-height: 380px;
  flex-shrink: 0;
}
.hero-photo-inner {
  border-radius: 50%;
  overflow: hidden;
  width: 100%;
  height: 100%;
}
.hero-photo-inner img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
```
The photo source should be a **square** image (1:1 ratio). A landscape banner image will show awkwardly even with `object-fit: cover`.

## LinkedIn iframe shows "Seite nicht gefunden" (Page not found)

The embedded post was deleted or the URL expired. In the admin UI, delete that post and re-embed it from LinkedIn.

## Admin UI not accessible

- URL: `your-domain.at/admin/` (trailing slash matters on some servers)
- If you get a 403: check that `admin/index.php` is uploaded to `wwwroot/admin/`
- PHP sessions require no special config on World4You

## `data/posts.json` was overwritten with placeholder

**Symptom:** All real posts are gone, only the example entry remains.

**Cause:** Local `data/posts.json` (containing only placeholders) was uploaded and overwrote the live server version.

**Prevention:** Add `data/posts.json` to `.gitignore` and never include it in upload batches. The file is managed exclusively via the admin UI on the server.

**Recovery:** The posts cannot be recovered. Re-add them via the admin UI.

## Fonts not loading (text looks bold/wrong weight)

Google Fonts are loaded via `<link>` in `<head>`. If they don't load:
- The page falls back to `sans-serif` (usually Arial/Helvetica) which renders `font-weight: 300` as regular weight
- Check for browser console errors about Content Security Policy blocking external resources
- World4You doesn't block Google Fonts — if it fails, it's usually a corporate proxy or ad-blocker

## `.htaccess` / server configuration

World4You supports `.htaccess` for Apache configuration. You generally don't need it for a simple PHP site. If you add one, ensure it doesn't accidentally block `data/posts.json` from being fetched by the browser.
