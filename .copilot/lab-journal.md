## [Reviewer] 2026-04-23 01:11

### Reviewed
- `index.html`
- `.github/workflows/deploy.yml`

### Issues
- Severity: 🔴 must-fix
- File and line reference: `index.html:7-9`, `index.html:527`
- What the problem is: The page loads Google Fonts from external origins, but the footer says "Nothing leaves your browser."
- Why it matters: Visiting the page sends network requests and browser metadata to Google, so the privacy statement is false.
- Suggested fix direction: Self-host the fonts or remove the external font requests, and make the footer copy match the actual network behavior.

- Severity: 🟡 should-fix
- File and line reference: `index.html:618-621`, `index.html:632`, `index.html:662`, `index.html:667`, `index.html:683`, `index.html:691-693`, `index.html:703`
- What the problem is: `secureRandom(max)` uses `arr[0] % max`, which introduces modulo bias.
- Why it matters: This is a password/PIN generator, so values should be sampled uniformly. The current approach makes some outputs slightly more likely than others.
- Suggested fix direction: Replace the modulo-based helper with rejection sampling and keep all bounded random selection on that helper.

- Severity: 🟡 should-fix
- File and line reference: `index.html:844-863`
- What the problem is: `copyPassword()` always shows the copied success state after the fallback path, even if `document.execCommand('copy')` fails.
- Why it matters: Users can be told their password was copied when it was not, which is a real usability and security problem for generated credentials.
- Suggested fix direction: Check the fallback result and only show the success UI when copying actually succeeds; surface a failure message otherwise.

- Severity: 🟡 should-fix
- File and line reference: `.github/workflows/deploy.yml:11-12`
- What the problem is: The workflow uses actions by mutable version tags instead of pinned commit SHAs.
- Why it matters: This increases supply-chain risk in the deployment path if an upstream tag is changed or compromised.
- Suggested fix direction: Pin the referenced actions to reviewed full-length commit SHAs.

### Good
- Password generation happens client-side with `crypto.getRandomValues()`.
- Random passwords guarantee at least one character from each enabled character set.
- `renderPassword()` escapes characters before using `innerHTML`, which avoids introducing XSS there.

### Verdict
**don't ship**

## [Coder] 2026-04-23 01:16

### Task
Fix all four issues flagged by reviewer.

### Changes
- index.html: Removed Google Fonts `<link>` tags (lines 7-9). Replaced `'Inter'` with system font stack, `'JetBrains Mono'` with `ui-monospace` stack. Privacy claim in footer is now accurate.
- index.html: Rewrote `secureRandom(max)` to use rejection sampling — computes `limit = floor(2^32 / max) * max` and redraws if `arr[0] >= limit`, eliminating modulo bias.
- index.html: `copyPassword()` now shows success UI only on the happy path (clipboard API success) or when `execCommand('copy')` returns true. On fallback failure, shows "⚠ Failed to copy" via `showToast(message)`.
- .github/workflows/deploy.yml: Pinned `actions/checkout` to `34e114876b0b11c390a56381ad16ebd13914f8d5` (v4) and `Azure/static-web-apps-deploy` to `1a947af9992250f3bc2e68ad0754c0b0c11566c9` (v1).

### Tests
Single-file HTML app — no automated test suite. Verified changes via code review of diff.

### Notes
- `secureRandomFloat()` is unused after this change set but was not removed (out of scope).
- Font appearance will shift slightly to system defaults; no external requests are made now.

## [Reviewer] 2026-04-23 01:22

### Reviewed
- `index.html`
- `.github/workflows/deploy.yml`
- Prior review findings in `.copilot/lab-journal.md`

### Issues
- None.

### Good
- The privacy claim now matches the actual runtime behavior: the external font requests are gone and the UI uses local/system font stacks.
- `secureRandom(max)` now uses rejection sampling correctly, and all bounded random selection sites still flow through that helper.
- The copy fallback now gates success UI on the actual result of `document.execCommand('copy')`, which fixes the false-positive copy state from the first review.
- The deploy workflow pins both third-party actions to full commit SHAs, reducing supply-chain risk without changing deployment behavior.

### Verdict
**ship**

## [Coder] 2026-04-23 01:29

### Task
Make the app installable as a Progressive Web App (PWA) with iOS Safari support.

### Changes
- manifest.json: Created web app manifest with name, icons, theme_color (#0a0a1a), background_color, display: standalone.
- sw.js: Created service worker with cache-first strategy for offline support. Caches index.html, manifest, and icons.
- icon-192.svg, icon-512.svg: SVG icons with gradient background and lock emoji matching the app's color scheme.
- icon-192.png, icon-512.png: PNG versions generated from SVGs via ImageMagick for broader compatibility.
- index.html: Added `<link rel="manifest">`, `<meta name="theme-color">`, iOS meta tags (apple-mobile-web-app-capable, apple-mobile-web-app-status-bar-style), `<link rel="apple-touch-icon">`, and service worker registration script.

### Tests
- Validated manifest.json parses as valid JSON.
- Verified all meta tags and SW registration present in index.html via grep.
- No automated test suite exists for this project.

### Notes
- Icons use SVG with emoji which renders well on most platforms. PNG fallbacks generated for iOS compatibility.
- Service worker caches the core assets on install; uses cache-first, network-fallback fetch strategy.
- `apple-mobile-web-app-status-bar-style` set to `black-translucent` to match the dark theme.
