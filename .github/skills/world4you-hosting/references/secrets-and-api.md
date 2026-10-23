# Secrets, FTP deploy and server-side API calls

## Where secrets go

The FTP root has `wwwroot/` (public web root) and space next to it that the web server never serves. Put every secret there:

```
/                    <- FTP root, not reachable over HTTP
├── config/
│   ├── anthropic.php   <?php return '<api-key>';
│   ├── admin.php       <?php return '<admin-password>';
│   └── service.php     <?php return ['url' => '<url>', 'token' => '<token>'];
└── wwwroot/            <- public
    └── api/chat.php    $key = require __DIR__ . '/../../config/anthropic.php';
```

- A PHP file that only `return`s a value prints nothing when called directly, so even a copy inside `wwwroot/` does not leak the value. Outside `wwwroot/` is still the default.
- Add `config/*.php` to `.gitignore`. Never hard-code passwords in `admin/index.php`; load them like above and compare with `hash_equals()`.
- Generate the config file locally in a temp folder, upload it, delete the local copy. Never paste the secret into chat, commits or logs.

## Deploy with FTP from the command line

WebFTP works, but `curl` makes uploads scriptable:

```bash
curl -s --ftp-create-dirs -T index.php --user "<ftp-user>:<ftp-password>" "ftp://<ftp-host>/wwwroot/index.php"
```

- Keep the credentials in a local `.env` file outside the repo. If it was saved by a Windows editor it may start with a UTF-8 BOM; strip it before sourcing (`sed '1s/^\xEF\xBB\xBF//'`).
- Delete a file: `curl --user "<user>:<pass>" -Q "DELE wwwroot/path/file" "ftp://<ftp-host>/"`.
- Never upload files that are managed live on the server (for example `data/posts.json`).

## Calling an external API (for example an AI chat)

The browser must never see an API key. Use a small PHP proxy:

1. Browser `fetch('api/chat.php', {method: 'POST', body: JSON})`.
2. `chat.php` validates input (length limit), applies a simple per-IP rate limit (hash of `REMOTE_ADDR`, timestamps in a file under `sys_get_temp_dir()`), loads the key from `config/`, calls the API with `curl`, returns only the answer text.
3. Set a monthly spend limit on the API side as a second safety net.

If the proxy gives the model tools that read data from another system (for example a smart-home instance), use a dedicated non-admin account, hard-code an allow-list of readable values in PHP and offer no write actions.

## Web application firewall: non-ASCII request bodies are blocked

World4You's firewall answers **403 Forbidden** to POST requests whose JSON body contains non-ASCII characters (umlauts, accents), even when they are `\u` escaped. ASCII-only requests pass.

Fix: Base64-encode the text in the browser and decode it in PHP.

```js
body: JSON.stringify({ m64: btoa(String.fromCharCode(...new TextEncoder().encode(text))) })
```

```php
$msg = (string)base64_decode((string)($in['m64'] ?? ''), true);
if (!mb_check_encoding($msg, 'UTF-8')) $msg = '';
```

Responses may contain any characters; only the request body is filtered.

## PHP version notes

World4You runs a current PHP 8.x. `curl_close()` is deprecated since 8.5 and prints a warning into the response, which breaks JSON output. Leave it out.
