# HimRaag — Legal & Support Website

The official legal and support website for **HimRaag**, an Android app for Pahadi folk and
Himalayan regional music.

> *"Preserving the Soul of Himalayan Music"*

**Live site:** <https://gosling-dude.github.io/himraag-legal/>

---

## Purpose

Google Play requires every published app to provide a publicly accessible **privacy policy URL**
and, for apps with accounts, a **data deletion URL**. This repository hosts those documents — plus
terms of service and a support contact — as a real website rather than raw Markdown, so the links
in the Play Console listing and inside the app resolve to something readable on any device.

These pages describe the app **as actually built**. Every data flow named in the privacy policy
(Firebase Authentication, Cloud Firestore, Cloudflare R2, Crashlytics, Analytics) corresponds to
something the shipped application really does.

| Page | URL | Used for |
|---|---|---|
| Home | [`/`](https://gosling-dude.github.io/himraag-legal/) | Landing page, app overview |
| Privacy Policy | [`/privacy.html`](https://gosling-dude.github.io/himraag-legal/privacy.html) | **Play Console → Privacy policy URL** |
| Terms of Service | [`/terms.html`](https://gosling-dude.github.io/himraag-legal/terms.html) | Store listing, in-app link |
| Delete Account | [`/delete-account.html`](https://gosling-dude.github.io/himraag-legal/delete-account.html) | **Play Console → Data deletion URL** |
| Contact | [`/contact.html`](https://gosling-dude.github.io/himraag-legal/contact.html) | Support, bug reports, DMCA |
| 404 | `/404.html` | Served automatically for unknown paths |

---

## Folder structure

```
himraag-legal/
├── index.html              # Homepage — hero, about, features, legal hub
├── privacy.html            # Privacy Policy  (Play requirement)
├── terms.html              # Terms of Service
├── delete-account.html     # Account & data deletion  (Play requirement)
├── contact.html            # Support, bug reports, DMCA
├── 404.html                # Not-found page (root-absolute asset paths)
├── README.md               # This file
├── LICENSE                 # MIT for the code, with a carve-out for legal text
├── .nojekyll               # Serve files as-is, skip Jekyll processing
└── assets/
    ├── css/
    │   └── style.css       # The entire stylesheet — no frameworks
    └── images/
        └── logo.png        # App icon, also used as favicon
```

---

## Deployment (GitHub Pages)

The site is served straight from the default branch — there is no build step, no bundler and no
CI pipeline. Push to `main` and the change is live within about a minute.

**Settings used:**

| Setting | Value |
|---|---|
| Source | Deploy from a branch |
| Branch | `main` |
| Folder | `/` (root) |

To configure it from scratch: **Settings → Pages → Source: Deploy from a branch → `main` / `root`
→ Save.**

`.nojekyll` is present so GitHub serves the files verbatim instead of running them through Jekyll.
Nothing here depends on Jekyll, and the file prevents surprises with any future directory whose
name begins with an underscore.

### Local preview

No tooling required — open `index.html` in a browser. To exercise the real relative paths, serve
the folder over HTTP:

```bash
python -m http.server 8000
# then visit http://localhost:8000
```

Note that `404.html` uses root-absolute paths (`/himraag-legal/...`) because GitHub Pages serves it
for missing URLs at *any* depth. Under `python -m http.server` those paths will not resolve — that
page only looks correct on the deployed site (or behind a server mounted at `/himraag-legal/`).

---

## How to edit the legal pages

Each page is a single self-contained HTML file. There are no templates or includes, so editing is
direct — but a few things must stay in sync by hand.

### Editing text

1. Open the relevant `.html` file.
2. Find the section — every `<h2>` carries an `id` (e.g. `<h2 id="retention">`).
3. Edit the prose inside the `<p>`, `<ul>` or `<table>` elements.
4. **Update the "Last updated" date** in the `.page-header` block at the top of the page.
5. Commit and push.

### When you change something, check these

| If you change… | Also update |
|---|---|
| A section heading | Its `id`, and the matching link in that page's `.toc` list |
| The contact email | **Every** page — it appears in the body, the footer and `mailto:` links |
| Navigation links | The `<nav>` block **and** the footer in all six HTML files |
| The "Last updated" date | Only the page you actually changed |
| Anything in the privacy policy | The Play Console **Data safety** form, if the change affects collected data |

> **The contact email appears in all six files.** Search-and-replace across the whole repo rather
> than editing page by page:
> ```bash
> grep -rl "old@example.com" . --include="*.html" | xargs sed -i "s/old@example.com/new@example.com/g"
> ```

### Adding a LinkedIn link

`contact.html` contains a commented-out `.contact-card` block ready for it. Uncomment it, paste the
profile URL, and move it inside the `.contact-grid`.

### Styling

All styling lives in `assets/css/style.css`, organised in numbered sections with the design tokens
at the top. The colour palette is taken verbatim from the Flutter app
(`lib/core/theme/app_colors.dart`) so the site and the product match:

| Token | Value | Role |
|---|---|---|
| `--purple` | `#6B3FA0` | Deep mountain purple — primary brand |
| `--gold` | `#E8A020` | Saffron / turmeric — accent |
| `--bg` | `#0D0D14` | Page background |
| `--card` | `#232338` | Card surface |

Change a token and every page follows.

---

## Design notes

- **No frameworks.** No Bootstrap, no Tailwind, no external CSS or fonts — one hand-written
  stylesheet.
- **No JavaScript.** The mobile menu is a pure-CSS checkbox toggle, so there is nothing to break
  and no console errors.
- **No external requests.** Fonts are the system stack and the only image is local, so the site
  loads fast and leaks nothing to third parties.
- **No cookies, no analytics, no trackers** on this site — which is what the privacy policy claims,
  so it had better be true.
- **Accessible.** Semantic landmarks, a skip link, visible focus states, `aria-current` on the
  active nav item, labelled tables, and support for `prefers-reduced-motion` and
  `prefers-contrast`.
- **Printable.** A dedicated print stylesheet renders the legal pages cleanly on paper or as PDF —
  people do save these.

---

## License

The **site code** (HTML structure and CSS) is released under the [MIT License](LICENSE) — reuse it
freely.

The **legal text** — the privacy policy, terms of service and deletion policy — is specific to
HimRaag and is **not** covered by the MIT grant. Please do not copy it for your own app: it
describes HimRaag's actual data flows, and a policy that misdescribes your app is worse than no
policy at all. Write one that matches what your app really does.

The HimRaag name and logo are the property of the developer.

---

## Roadmap

- [ ] Add the LinkedIn profile link to the contact page
- [ ] Publish an in-app "Delete account" flow, then update `delete-account.html` to document it
- [ ] Add a custom domain (e.g. `himraag.app`) and update every canonical URL, the Play Console
      links, and `AppConstants` in the Flutter app
- [ ] Publish a proper cookie notice if a web player ever ships
- [ ] Hindi / Pahadi translations of the legal pages
- [ ] Add `sitemap.xml` and `robots.txt` once the domain is final
- [ ] Store-listing press kit: screenshots, feature graphic, app description

---

## Maintainer

**Sumit Chauhan** — [@Gosling-dude](https://github.com/Gosling-dude)
Contact: [sc5271027@gmail.com](mailto:sc5271027@gmail.com)

Made with ❤️ in India.
