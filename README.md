# World4You Hosting Skill

## What is this for?

**Purpose:** A Claude Code skill for building and deploying personal homepages on [World4You](https://www.world4you.com) shared hosting (popular Austrian provider).

**Audience:** Developers who want to build a modern personal/portfolio page on World4You without fighting the platform's quirks from scratch.

**What you get:**
- A repeatable recipe for the full workflow: local development → WebFTP upload → live site
- PHP patterns for photo detection, session-based admin auth, JSON file management
- A LinkedIn posts section using iframe embeds (no API required) with a password-protected admin UI
- Troubleshooting guides for the most common World4You issues (wrong web root, CSS cache, JS crashes)

**What it is not:** A CMS, a framework, or a generator. The output is intentionally simple: plain PHP/HTML/CSS/JS files that work on any basic shared hosting.

---

## Skills

| Skill | Description |
|-------|-------------|
| `world4you-hosting` | Main skill — file structure, upload workflow, PHP setup, LinkedIn posts, troubleshooting |

### References (loaded on demand)

| File | Content |
|------|---------|
| `references/file-structure.md` | `wwwroot/` layout, upload checklist, local preview |
| `references/php-setup.md` | Photo detection, admin auth, JSON read/write, what's available on World4You |
| `references/linkedin-posts.md` | Iframe embed flow, JS loader, `posts.json` format, common issues |
| `references/secrets-and-api.md` | Secrets outside `wwwroot/`, FTP deploy with curl, API proxy, firewall workaround |
| `references/troubleshooting.md` | ERR_CONNECTION_REFUSED, CSS cache, JS crashes, oval photo ring, lost posts |

---

## Installation

### As a Claude Code plugin

```bash
/plugin install https://github.com/mobilewhatelse/world4you-skill
```

### Manual (project-level)

Copy `.github/skills/world4you-hosting/` into your project's `.github/skills/` folder. Claude Code auto-discovers skills in that location.

---

## License

MIT
