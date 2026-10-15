# LinkedIn Posts — Embed Pattern

## Why not the LinkedIn API?

LinkedIn's API for reading personal post feeds requires **Company Page** ownership and an approved partner application. It is not available for personal profiles. The workaround: use LinkedIn's built-in "Embed this post" feature, paste the iframe into a simple admin UI, and serve it from a JSON file.

## Architecture

```
LinkedIn "Embed this post" → copy <iframe>
        ↓
  your-domain.at/admin/   ← paste iframe, save
        ↓
  data/posts.json          ← stored on server
        ↓
  js/main.js fetch()       ← renders iframes on homepage
```

## How to embed a LinkedIn post

1. Open the post on LinkedIn
2. Click **···** (three dots) in the top-right corner of the post
3. Choose **"Embed this post"**
4. Copy the `<iframe ...>` code
5. Paste into the Admin UI at `your-domain.at/admin/`
6. Optionally add a short description
7. Click **"Post hinzufügen"**

## `data/posts.json` format

```json
[
  {
    "embed": "<iframe src=\"https://www.linkedin.com/embed/feed/update/...\" height=\"399\" width=\"504\" frameborder=\"0\" allowfullscreen=\"\" title=\"Embedded post\"></iframe>",
    "date": "2026-10-01",
    "preview": "Short description shown in admin UI"
  }
]
```

- Array is ordered newest-first (admin prepends new entries)
- Maximum 10 posts kept (older ones are dropped automatically)
- **Never overwrite this file from your local copy** — it is the live source of truth on the server

## JavaScript loader (`js/main.js`)

```js
async function loadLinkedInPosts() {
  const container = document.getElementById('li-posts-container');
  if (!container) return;

  try {
    const res   = await fetch('data/posts.json');
    const posts = await res.json();

    if (!posts || posts.length === 0) {
      container.innerHTML = '<p class="posts-empty">No posts yet.</p>';
      return;
    }

    container.innerHTML = posts.map(post => `
      <div class="li-embed-wrap">
        <div class="li-embed-date">${post.date || ''}</div>
        ${post.embed}
      </div>
    `).join('');

  } catch {
    container.innerHTML = '<p class="posts-empty">Posts could not be loaded.</p>';
  }
}

loadLinkedInPosts();
```

Key points:
- Fetch goes to `data/posts.json` directly — works on both static server and PHP host
- No canvas or other DOM elements should be touched before this function — a JS crash earlier in the file will silently block it
- Wrap everything in try/catch — a 404 on the JSON file should show a graceful message, not a broken page

## Common issue: "Loading posts…" stays forever

Cause: A JavaScript error earlier in `main.js` stops execution before `loadLinkedInPosts()` is called.

Fix: Open browser DevTools → Console tab. Look for the error. Common culprits:
- `TypeError: Cannot read properties of null (reading 'getContext')` — old canvas code referencing a `<canvas>` that no longer exists in the HTML
- Network error on `data/posts.json` — file not uploaded or wrong path

## Common issue: LinkedIn iframe shows "Page not found"

The embedded post URL is stale (post was deleted or the URL changed). Delete the post in the admin UI and re-embed it.
