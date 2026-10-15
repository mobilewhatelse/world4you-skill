# File Structure & Upload — World4You

## Critical: web root is `wwwroot/`, not `/`

World4You's FTP root contains several system folders. The publicly served folder is `wwwroot/`. **All project files must go inside `wwwroot/`.**

Common mistake: uploading to FTP root `/` → site shows "403 Forbidden" or "directory listing not allowed".

### Control panel setting

In the World4You control panel, under "Webhosting" → "Root-Verzeichnis", set the value to `wwwroot`. Without this, even files in `wwwroot/` may not be served.

## Project layout

```
wwwroot/
├── index.php          ← entry point; PHP handles photo detection and current year
├── index.html         ← optional static mirror for local preview (no PHP)
├── css/
│   └── style.css      ← all styles; single file, no preprocessor
├── js/
│   └── main.js        ← scroll animations + loads data/posts.json for LinkedIn posts
├── img/
│   └── portrait.png   ← portrait photo; PHP checks for .png .jpg .jpeg .webp in order
├── data/
│   └── posts.json     ← LinkedIn iframe embed codes; managed by admin UI, never overwrite
├── admin/
│   └── index.php      ← password-protected post manager
└── api/
    └── posts.php      ← serves posts.json with correct Content-Type (optional)
```

## `.gitignore` recommendations

```
data/posts.json        # live on server, not in git
config/secrets.php     # if you add a secrets file
*.log
```

## Local preview

`index.html` is a static copy of `index.php` with PHP replaced by static equivalents (hardcoded photo path, static year). Run locally with:

```bash
npx serve .
```

JS fetches `data/posts.json` directly — works on both static server and PHP host.

## WebFTP upload notes

- WebFTP at `webftp.world4you.com`
- Drag-and-drop files into the correct folder
- Create folders (`css/`, `js/`, `img/`, `data/`, `admin/`) before uploading their contents
- After upload, always do **Ctrl+Shift+R** (hard refresh) in the browser — World4You CDN can cache CSS/JS aggressively

## Subdomain vs. primary domain

Each subdomain has its own folder in the FTP root, each with its own `wwwroot/`. For example:
- `your-domain.at` → `/wwwroot/`
- `sub.your-domain.at` → `/sub/wwwroot/`

Set the "Root-Verzeichnis" separately for each subdomain in the control panel.
