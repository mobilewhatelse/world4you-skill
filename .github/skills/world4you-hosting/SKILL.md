---
name: world4you-hosting
description: Build and deploy PHP/HTML homepages on World4You shared hosting — file structure, WebFTP upload, PHP patterns, LinkedIn post embeds, and a ready-to-use single-page portfolio template.
---

# World4You Hosting — Homepage Skill

Field-tested patterns for building and deploying personal homepages on **World4You** shared hosting (Austrian provider). Covers the file structure, PHP setup, WebFTP upload workflow, and a complete single-page portfolio template with an embedded LinkedIn posts section.

## What is this for?

**Audience:** Developers building personal or small-business homepages on World4You shared hosting.  
**What you get:** A repeatable recipe — from local development to live deployment — including a dark, modern single-page design with PHP photo detection, LinkedIn iframe embeds managed via a simple admin UI, and a contact section.  
**What it is not:** A framework or CMS. This is intentionally simple: pure HTML/CSS/JS/PHP, no build tools, no database required.

## When to load this skill

- Setting up a new homepage project on World4You shared hosting
- Uploading files via WebFTP and troubleshooting path / directory issues
- Building a PHP-based single-page site with a LinkedIn posts section
- Diagnosing "connection refused", "directory not found", or CSS-not-loading issues on World4You

## References

Load only the reference relevant to the current task:

| Reference | When to load |
|-----------|-------------|
| [file-structure.md](references/file-structure.md) | Setting up the project folder layout or uploading for the first time |
| [php-setup.md](references/php-setup.md) | Writing PHP (photo detection, date output, admin auth) |
| [linkedin-posts.md](references/linkedin-posts.md) | Adding or troubleshooting the LinkedIn embedded-posts section |
| [secrets-and-api.md](references/secrets-and-api.md) | Storing passwords/API keys, deploying via FTP command line, PHP proxy for external APIs, firewall 403 on POST |
| [troubleshooting.md](references/troubleshooting.md) | ERR_CONNECTION_REFUSED, CSS not loading, posts not showing, photo ring issues |

## Key facts about World4You

| Topic | Detail |
|-------|--------|
| **Web root** | `wwwroot/` subfolder — NOT the FTP root `/`. All files go here. |
| **Control panel** | Set "Root-Verzeichnis" to `wwwroot` in the World4You control panel. |
| **PHP** | PHP available; MySQL available but not needed for a static portfolio. |
| **No SSH** | No shell access. File management via WebFTP only. |
| **No Composer** | No package manager. Pure PHP only. |
| **Domains** | Primary domain and subdomains each map to their own `wwwroot/` within that subdomain's folder. |

## Recommended project structure

```
wwwroot/
├── index.php          ← main page (PHP for photo detection + year)
├── css/
│   └── style.css
├── js/
│   └── main.js        ← loads data/posts.json, scroll animations
├── img/
│   └── portrait.png   ← square portrait photo (512 × 512 or larger)
├── data/
│   └── posts.json     ← LinkedIn iframe embeds (managed by admin UI)
├── admin/
│   └── index.php      ← password-protected admin for adding/removing posts
└── api/
    └── posts.php      ← (optional) serves posts.json as JSON API
```

## Upload checklist

When deploying changes, upload only the files that changed. **Never overwrite `data/posts.json`** from your local copy — it is managed live on the server by the admin UI.

| Situation | Files to upload |
|-----------|----------------|
| First deploy | Everything except `data/posts.json` |
| Design change | `css/style.css` |
| Layout change | `index.php` |
| JS fix | `js/main.js` |
| New portrait photo | `img/portrait.png` (or whichever extension) |
| Admin UI change | `admin/index.php` |

## Quick-start workflow

1. Build locally with `npx serve` (or any static server — `index.html` mirrors `index.php` for local preview)
2. Test on desktop and mobile
3. Upload changed files via WebFTP to `wwwroot/`
4. Hard-refresh in browser (Ctrl+Shift+R) to bypass cache
5. Add LinkedIn posts via `your-domain.at/admin/`
