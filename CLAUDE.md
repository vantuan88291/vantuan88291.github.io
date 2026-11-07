# vantuan88291.github.io

Static GitHub Pages site that showcases the owner's Android apps. Plain HTML + inline CSS, no build step, no JS framework. Pushing to `main` deploys it.

## Structure

```
/
├── index.html        # Home: lists all apps (featured card per app)
├── policy.html       # Legacy redirect → subglance/policy.html (keep! linked from Google Play)
├── app-ads.txt       # AdMob verification — MUST stay at the root
├── CLAUDE.md
└── <app-slug>/       # One folder per app, e.g. subglance/
    ├── index.html    # App landing page  → https://vantuan88291.github.io/<app-slug>/
    ├── policy.html   # Privacy Policy & Terms → .../<app-slug>/policy.html
    └── icon.png      # 512×512 Play Store icon (ic_launcher-playstore.png renamed)
```

`subglance/` is the reference implementation. Copy it when adding an app.

## Adding a new app

1. **Get the facts from the owner first.** Don't make them up: app name, slug, one-line category, short description, key features, Play Store package id/URL, supported languages or other numbers, what data/SDKs the app uses (Firebase, AdMob, Crashlytics, permissions…), contact email, and the icon file.
2. **Create the folder** `/<app-slug>/` (lowercase, no spaces) and copy `subglance/index.html` and `subglance/policy.html` into it.
3. **Icon:** move the provided icon into the folder as `icon.png` with `git mv` (512×512 PNG).
4. **Landing page** (`<app-slug>/index.html`): update `<title>`, `meta description`, `og:title`, `og:image` (absolute URL `https://vantuan88291.github.io/<app-slug>/icon.png`), hero text, badges, feature cards, privacy bullets, the Play Store link, and the footer. Keep the "All apps" links (`../`).
5. **Privacy policy** (`<app-slug>/policy.html`): rewrite it for this app. It must match what the app really collects, which permissions it requests, and which third-party SDKs it uses. Update the effective date. Never copy SubGlance's claims without checking them.
6. **Home page** (`/index.html`):
   - Copy the `<article class="feature">…</article>` block inside `#apps` and fill it in for the new app. Paths are relative to the root (`<app-slug>/`, `<app-slug>/icon.png`).
   - Update the count in `.sec-head span` ("2 apps").
   - Add the app's policy link to the footer.
   - Check whether the hero and "How these apps are built" copy is still true for every app. Generalise it if not.
   - Once there are 3 or more apps, consider swapping the large feature cards for a grid of compact cards. You can keep the newest app featured.
7. **AdMob:** if the app uses AdMob under a different publisher, append its line to the root `app-ads.txt`. Never create per-app `app-ads.txt` files, because Google only reads the root one.
8. **Verify** (see below), then tell the owner which URL to set as the privacy policy in Play Console: `https://vantuan88291.github.io/<app-slug>/policy.html`.

## Rules

- **Never move or delete the root `policy.html` or `app-ads.txt`.** Live Play Store listings and AdMob depend on these URLs. If you rename or move any published page, leave a redirect at the old path. Use the same pattern as root `policy.html`: meta refresh + `location.replace` + canonical.
- **Don't invent claims.** Everything shown (offline, no account, language counts, "free", data handling) must be confirmed by the owner or the app's existing policy. Store and privacy pages are checked by Google.
- Keep every page self-contained: inline `<style>`, no build tools, no external JS. Only Google Fonts (Open Sans) is loaded externally.
- Keep the shared design tokens consistent across pages: the CSS variables in `:root` (`--bg`, `--surface`, `--text`, `--muted`, `--outline`, `--primary`, `--primary-container`…). Each page must support light and dark mode: `@media (prefers-color-scheme: dark)` under `:root:not([data-theme="light"])`, plus `:root[data-theme="dark"]`.
- An app may use its own accent colour by overriding `--primary` and `--primary-container` in its own pages.
- Must work at phone width (≥ 360px) without horizontal scrolling. Respect `prefers-reduced-motion`.
- Page text is in English.
- Use relative links inside the site (`../`, `policy.html`). Use absolute URLs only for `og:image`, canonical and redirects.

## Verifying changes

There is no build step. Open the files directly, or preview them locally:

```sh
python3 -m http.server 8000   # then open http://localhost:8000/ and /<app-slug>/
```

Check: home page card links, the "All apps" back links, the policy link, icons loading, dark mode, mobile width (DevTools ~390px), and that `/policy.html` still redirects.

Headless screenshot (desktop Chrome won't go below ~500px wide, so for mobile load the page in a 390px `<iframe>`):

```sh
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless=new --hide-scrollbars \
  --screenshot=out.png --window-size=1280,1900 "file://$PWD/index.html"
```

## Deploy

Commit and push to `main`. GitHub Pages publishes in about a minute. Only commit or push when the owner asks.
