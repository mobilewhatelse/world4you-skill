# PHP Patterns — World4You

World4You shared hosting runs PHP. No SSH, no Composer, no shell. Keep PHP usage minimal and self-contained.

## Photo detection

Detect whichever portrait format was uploaded, in priority order:

```php
<?php
$photo = null;
foreach (['portrait.png', 'portrait.jpg', 'portrait.jpeg', 'portrait.webp'] as $f) {
    if (file_exists(__DIR__ . '/img/' . $f)) { $photo = $f; break; }
}
?>
```

Then in the template:

```php
<?php if ($photo): ?>
  <img src="img/<?= $photo ?>" alt="Portrait">
<?php else: ?>
  <div class="photo-placeholder">👤</div>
<?php endif; ?>
```

## Current year in footer

```php
© <?php echo date('Y'); ?> Your Name
```

## Admin UI — password protection

Simple session-based auth. Store the password as a `define` constant at the top of `admin/index.php`.  
Use a strong password — this is the only protection for the post manager.

```php
<?php
define('ADMIN_PASSWORD', 'your-strong-password-here');
session_start();

if ($_SERVER['REQUEST_METHOD'] === 'POST' && isset($_POST['password'])) {
    if ($_POST['password'] === ADMIN_PASSWORD) {
        $_SESSION['admin'] = true;
    } else {
        $error = 'Wrong password.';
    }
}
if (isset($_POST['logout'])) {
    session_destroy();
    header('Location: index.php');
    exit;
}
```

## Reading / writing `data/posts.json`

```php
$postsFile = __DIR__ . '/../data/posts.json';
$posts = json_decode(file_get_contents($postsFile), true) ?: [];

// Add new entry at top, keep max 10
array_unshift($posts, ['embed' => $embed, 'date' => date('Y-m-d'), 'preview' => $preview]);
$posts = array_slice($posts, 0, 10);
file_put_contents($postsFile, json_encode($posts, JSON_PRETTY_PRINT | JSON_UNESCAPED_UNICODE));
```

## Sanitising LinkedIn iframe embed input

Strip the full HTML to just the `src` URL, then rebuild a clean iframe:

```php
if (strpos($embed, '<iframe') !== false) {
    preg_match('/src="([^"]+)"/', $embed, $m);
    $src = $m[1] ?? '';
    $embed = '<iframe src="' . htmlspecialchars($src, ENT_QUOTES)
           . '" height="399" width="504" frameborder="0" allowfullscreen="" title="Embedded post"></iframe>';
}
```

## What is NOT available

| Feature | Status |
|---------|--------|
| Composer / package manager | ✗ Not available |
| SSH / shell | ✗ Not available |
| `exec()` / `shell_exec()` | ✗ Disabled |
| Cron jobs | ✓ Available via control panel UI |
| MySQL | ✓ Available (not needed for a static portfolio) |
| `file_get_contents()` for local files | ✓ Works |
| `file_put_contents()` | ✓ Works (check folder permissions) |
