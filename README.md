# Password Generator

A sleek, client-side password generator inspired by 1Password. Generates strong passwords, memorable passphrases, and PINs — entirely in your browser.

**[Live Demo →](https://red-flower-08bc4e403.7.azurestaticapps.net)**

## Features

- **Random passwords** — configurable length with uppercase, lowercase, numbers, and symbols
- **Memorable passphrases** — word-based passwords that are easy to remember
- **PIN codes** — numeric PINs of any length
- **Strength meter** — real-time entropy-based password strength indicator
- **Copy to clipboard** — one-click copy with visual feedback
- **Installable PWA** — works offline, add to home screen on any device

## Privacy

Everything runs client-side. No external requests, no analytics, no tracking. Randomness comes from `crypto.getRandomValues()` — your passwords never leave your browser.

## Install as PWA

1. Open the [live demo](https://red-flower-08bc4e403.7.azurestaticapps.net) in your browser
2. **Chrome/Edge**: Click the install icon in the address bar, or use ⋮ → "Install app"
3. **Safari (iOS)**: Tap Share → "Add to Home Screen"
4. The app works fully offline once installed

## Tech Stack

- Vanilla HTML / CSS / JavaScript — single-file app, no build step
- `crypto.getRandomValues()` with rejection sampling for unbiased randomness
- Service worker for offline caching
- Web App Manifest for PWA install support

## Project Structure

```
index.html       — entire app (markup, styles, and logic)
manifest.json    — PWA manifest (name, icons, theme)
sw.js            — service worker (cache-first offline strategy)
icon-*.svg/png   — app icons in 192px and 512px variants
```

## License

MIT
