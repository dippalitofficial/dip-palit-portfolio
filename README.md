# dippalit.com

The personal site of **Dip Palit** — a B2B cold caller and SDR specialising in merchant cash advance qualification and FMCSA/DOT compliance outreach for US motor carriers.

A hand-written static site. No framework, no build step, no package manager, no dependencies to install. Clone it, open it, ship it.

**Live:** [dippalit.com](https://dippalit.com)

---

## Contents

- [At a glance](#at-a-glance)
- [Why static](#why-static)
- [Repository layout](#repository-layout)
- [Running it locally](#running-it-locally)
- [Deployment](#deployment)
- [Configuration files](#configuration-files)
- [Structured data](#structured-data)
- [Common edits](#common-edits)
- [Design system](#design-system)
- [Accessibility](#accessibility)
- [Browser support](#browser-support)
- [Third-party dependencies](#third-party-dependencies)
- [Changelog](#changelog)
- [Licence](#licence)

---

## At a glance

| | |
|---|---|
| Pages | 28 + a 404 |
| Words of content | ~44,800 |
| Structured data | 74 JSON-LD blocks across 14 schema types |
| Stylesheet | One file, 61 KB, ~450 rules |
| JavaScript | One file, 5 KB, no libraries |
| Total repo size | ~825 KB across 40 files |
| Build step | None |
| Runtime dependencies | None |

Content breaks down as 6 service pages, 2 deep specialist pages (MCA and FMCSA/DOT), 8 long-form blog guides, 2 case studies, 2 printable checklists, plus pricing, about, tools, FAQ and contact.

---

## Why static

The site is three kinds of thing at once: a portfolio, a lead-capture page, and a body of reference content that needs to rank. Static HTML serves all three well.

- **Fast.** No hydration, no render-blocking framework. One stylesheet, one small script.
- **Durable.** Nothing to patch, no dependency tree to rot, no build that breaks in eighteen months.
- **Portable.** Runs identically on Netlify, Vercel, Cloudflare Pages, or any Apache/cPanel host.
- **Crawlable.** Everything is in the initial HTML response, which matters for both search engines and AI crawlers.

The pages were generated once from a set of Python templates and then committed as flat HTML. The generator is not part of this repository — the HTML is the source of truth. Edit the HTML directly.

---

## Repository layout

```
.
├── index.html                      Homepage
├── 404.html                        Error page
│
├── about/                          One folder per page, each with index.html
├── contact/                        so URLs resolve as /about/, /contact/, …
├── faq/
├── pricing/
├── tools/
│
├── services/
│   ├── index.html                  Services hub
│   ├── cold-calling/
│   ├── appointment-setting/
│   ├── lead-generation/
│   ├── crm-management/
│   ├── mca-specialist/             Specialist page — merchant cash advance
│   └── dot-compliance-specialist/  Specialist page — FMCSA/DOT compliance
│
├── case-studies/
│   ├── index.html
│   ├── mca-funding-campaign/
│   └── dot-compliance-outreach/
│
├── resources/
│   ├── index.html
│   ├── mca-qualification-checklist/   Printable, print-stylesheet aware
│   └── dot-compliance-checklist/      Printable, print-stylesheet aware
│
├── blog/
│   ├── index.html                  Blog hub
│   └── …8 long-form guides
│
├── assets/
│   ├── css/style.css               Entire design system
│   └── js/main.js                  Nav, FAQ accordions, form handling
│
├── sitemap.xml                     28 URLs
├── robots.txt                      Explicitly allows AI crawlers
├── llms.txt                        Structured site summary for LLM crawlers
├── _headers                        Netlify / Cloudflare Pages
├── _redirects                      Netlify / Cloudflare Pages — legacy URL 301s
├── .htaccess                       Apache / cPanel equivalent
│
├── preview-local-WINDOWS.bat       Local preview launcher
├── preview-local-MAC.command       Local preview launcher
├── README-DEPLOY.txt               Non-technical deployment guide
└── README.md                       This file
```

URLs use the folder/`index.html` pattern so they resolve as clean paths — `/services/mca-specialist/` rather than `/services/mca-specialist.html`. Every canonical tag and every sitemap entry uses that clean form.

---

## Running it locally

> **Do not open `index.html` by double-clicking it.**
>
> A `file://` URL cannot resolve a folder to its `index.html`, so every internal link lands on a directory listing and the site looks broken when it isn't. `file://` also sends no HTTP referrer, which makes the YouTube embed fail with error 153.

Use a local server instead. Any of these work:

```bash
# Python 3 — no install needed on macOS or most Linux
python3 -m http.server 8080

# Node
npx serve .

# PHP
php -S localhost:8080
```

Then open <http://localhost:8080>.

For convenience there are two double-clickable launchers that do the same thing and open your browser automatically:

| Platform | File |
|---|---|
| Windows | `preview-local-WINDOWS.bat` |
| macOS | `preview-local-MAC.command` *(first run: right-click → Open)* |

Both are ignored by `robots.txt` and are inert on a web server. Delete them if you'd rather not ship them.

**One caveat:** `localhost` reproduces layout, styling and navigation faithfully, but not everything. The YouTube embed may still refuse to play because YouTube does not accept `localhost` as a valid embedding referrer. Verify video playback on the deployed domain, not locally.

---

## Deployment

No build command. No output directory. Deploy the repository root as-is.

### Netlify / Cloudflare Pages

Connect the repo, or drag the folder into the deploy zone. Leave the build command empty and the publish directory as `/`. Both platforms read `_headers` and `_redirects` automatically.

### Vercel

Import the repo as a static project. No framework preset, no build command.

### Apache / cPanel shared hosting

Upload the **contents** of the repository to `public_html` — `index.html` must sit at the web root, not inside a subfolder. Confirm `.htaccess` uploaded; it begins with a dot and most file managers hide it by default. Then run AutoSSL.

### GitHub Pages — read this first

GitHub Pages will serve the site, but it **ignores `_headers`, `_redirects` and `.htaccess`**. That means no security headers, no cache-control policy, and none of the legacy-URL 301 redirects. Clean URLs still work, since Pages resolves directory indexes.

If you deploy to Pages, either accept those losses or move the redirects into HTML `<meta http-equiv="refresh">` stubs. For a site whose purpose is ranking, a host that honours the redirect and header configuration is the better choice.

### After deploying

1. Verify HTTPS, and that `http://` and the `www.` variant both redirect to the canonical host.
2. Submit `sitemap.xml` in Google Search Console.
3. Request indexing for the homepage and the two specialist service pages.
4. Import the property into Bing Webmaster Tools — Bing feeds several AI search products.

---

## Configuration files

| File | Read by | Purpose |
|---|---|---|
| `robots.txt` | All crawlers | Allows everything except the preview launchers. Explicitly allows `GPTBot`, `ClaudeBot`, `PerplexityBot`, `Google-Extended`, `Applebot-Extended` and others that many sites block by accident. Points to the sitemap. |
| `sitemap.xml` | Search engines | All 28 canonical URLs with `lastmod`, `changefreq` and `priority`. No hash anchors, no duplicates. |
| `llms.txt` | LLM crawlers | A plain-text structured summary of who the site belongs to, what each page covers, and explicit notes that compliance content is educational rather than legal advice. |
| `_headers` | Netlify, Cloudflare Pages | HSTS, `X-Frame-Options`, `X-Content-Type-Options`, `Referrer-Policy`, `Permissions-Policy`; one-year immutable caching on `/assets/*`, revalidate on HTML. |
| `_redirects` | Netlify, Cloudflare Pages | 301s from the previous site's URLs (`services.html`, `tools.html`, `/solutions/*`, old hash anchors) to the current structure. |
| `.htaccess` | Apache, cPanel | The same redirects and headers, plus `DirectoryIndex`, forced HTTPS, non-`www` canonicalisation, `index.html` stripping, gzip and expires rules. |

---

## Structured data

74 JSON-LD blocks across 14 types. Every page carries at least a `BreadcrumbList`, and most carry a page-type entity plus `FAQPage`.

| Type | Count | Where |
|---|---|---|
| `BreadcrumbList` | 27 | Every page except the homepage |
| `FAQPage` | 19 | Homepage, service pages, blog posts, FAQ |
| `BlogPosting` | 8 | All blog guides |
| `Service` | 7 | Service and pricing pages |
| `Person` | 2 | Homepage and about — the `@id` anchor other entities reference |
| `Article` | 2 | Case studies |
| `HowTo` | 2 | The two printable checklists |
| `AboutPage`, `Blog`, `ContactPage`, `ItemList`, `ProfessionalService`, `VideoObject`, `WebSite` | 1 each | — |

Notes on choices:

- **`BlogPosting`, not `Article`, on blog posts.** `BlogPosting` is a subtype of `Article`, so it inherits everything `Article` provides. Adding both would declare two competing entities for one page.
- **A single `Person` node.** Defined once at `https://dippalit.com/#person` and referenced by `@id` everywhere else, rather than redeclared per page. One entity, many references.
- **`FAQPage` density is deliberate.** FAQ and HowTo markup are the formats most readily lifted into AI-generated answers and featured snippets. That is the main reason this site is structured the way it is.
- **`wordCount` is computed**, not estimated.

Validate changes with the [Schema Markup Validator](https://validator.schema.org/) and the [Rich Results Test](https://search.google.com/test/rich-results).

---

## Common edits

### Change a colour, font or spacing value

Everything is a custom property at the top of `assets/css/style.css`:

```css
:root {
  --navy: #0F172A;
  --blue: #2563EB;
  --sky:  #60A5FA;
  --font-display: 'Raleway', …;
  --font-body: 'Nunito', …;
  --r: 14px;
  --container: 1180px;
}
```

Tints are derived with `color-mix()` from those base values, so changing `--blue` updates every related shade. Change it once; the whole site follows.

### Swap the intro video

The YouTube ID appears four times in `index.html` — once in the iframe `src`, three times in the `VideoObject` schema (`contentUrl`, `embedUrl`, `thumbnailUrl`). Find and replace:

```
HJg56OIPxYg  →  your-new-video-id
```

Then update `uploadDate`, `name` and `description` in the schema. Adding a `duration` in ISO 8601 form (`"PT1M12S"`) is optional but preferred by Google.

### Update contact details

Find and replace across all files:

| Current value | Appears in |
|---|---|
| `dippalitofficial@gmail.com` | contact page, footer, schema |
| `https://www.upwork.com/freelancers/~017494125bf2bd8aa8` | CTAs, footer, schema `sameAs` |
| `https://www.linkedin.com/in/dippalit01` | footer, schema `sameAs` |
| `https://wa.link/pbduj5` | CTAs |
| `https://formspree.io/f/meedlzvg` | contact form action |

### Add a blog post

1. Copy any folder inside `blog/` and rename it to the new slug.
2. Edit the content, `<title>`, meta description and canonical URL.
3. Update the `BlogPosting` and `BreadcrumbList` schema — `headline`, `description`, `datePublished`, `wordCount`, `mainEntityOfPage`.
4. Add a card to `blog/index.html`.
5. Add the URL to `sitemap.xml`.
6. Add a line to `llms.txt`.

### Replace the portrait

The photo is hotlinked from `https://i.imgur.com/zqJgmgn.png`. Consider committing it to `assets/img/` and switching to a relative path — one less third-party dependency, and faster. It is referenced in the hero, the blog author boxes, and the Open Graph and schema image fields.

---

## Design system

Navy `#0F172A` and blue `#2563EB`, Raleway for display and Nunito for body text, on a token-driven stylesheet.

- **Fluid typography.** Sizes interpolate with `clamp()` rather than stepping at breakpoints.
- **Intrinsic grids.** Card layouts use `repeat(auto-fit, minmax(…, 1fr))`, which removed most media queries.
- **Figures are the emphasis.** The site's credibility rests on verifiable numbers, so statistics use tabular lining figures, tightened tracking, and a rule tying each number to its label.
- **Restrained motion.** A single 6px entrance transition. Hover lift is reserved for cards that are genuinely clickable.
- **Dark mode.** Full `prefers-color-scheme: dark` support in section 21 of the stylesheet. Self-contained — delete that block to ship light-only.
- **Print styles.** The two checklists are designed to be printed; navigation, CTAs and the video are suppressed, FAQ answers expand, and page breaks avoid splitting list items.

The stylesheet is organised into 22 numbered sections with a table of contents at the top.

---

## Accessibility

- Skip-to-content link on every page.
- Keyboard-operable FAQ accordions with `aria-expanded` state.
- Visible `:focus-visible` outlines throughout, suppressed for mouse users.
- 44px minimum tap targets on icon-only controls.
- Alt text on every image.
- `prefers-reduced-motion` honoured — all animation and smooth scrolling disabled.
- Semantic landmarks: `<nav>`, `<main>`, `<article>`, `<aside>`, `<footer>`.
- Tables use `<th scope="col">` and wrap in a horizontally scrollable container rather than overflowing the page.

---

## Browser support

Modern evergreen browsers. The stylesheet uses `color-mix()`, `:has()`, `clamp()`, `aspect-ratio`, logical properties and container-relative units — all Baseline as of 2024.

Two enhancements degrade gracefully behind `@supports`: scroll-driven animation on the navigation border, and `backdrop-filter` on the navigation background. Neither affects legibility or layout where unsupported.

No Internet Explorer support, and none intended.

---

## Third-party dependencies

The site has no build-time dependencies. At runtime it requests four external resources:

| Resource | Host | Failure mode |
|---|---|---|
| Raleway, Nunito | `fonts.googleapis.com`, `fonts.gstatic.com` | Falls back to `system-ui` |
| Font Awesome 6.5.0 | `cdnjs.cloudflare.com` | Icons disappear; layout holds |
| Portrait photo | `i.imgur.com` | Empty circle in the hero |
| Intro video | `youtube.com` | Player fails; a "Watch on YouTube" fallback link remains |

Form submissions post to Formspree over AJAX, with a non-JS fallback to a standard form POST.

Reducing this list — self-hosting the fonts, subsetting the icons, committing the photo — is the clearest available performance win.

---

## Changelog

| Version | Changes |
|---|---|
| **1.3.1** | Moved the video embed off `youtube-nocookie.com` and removed the restrictive `referrerpolicy`; both can trigger YouTube error 153. Added a persistent "Watch on YouTube" fallback link. |
| **1.3** | Intro video with `VideoObject` schema. Eight contextual blog → service internal links. Computed `wordCount` on all `BlogPosting` schema. |
| **1.2.3** | Local preview launchers and documentation for the `file://` directory-index problem. |
| **1.2.2** | Restored the circular portrait, the floating hero cards' animation, and centred footer text. |
| **1.2.1** | Nine styling regressions fixed after browser-based review — hero grid on the wrong element, card headings rendering as raw hyperlinks, breadcrumbs overlapping the navbar, permanently visible form success message, duplicate checkbox icons, and others. |
| **1.2** | Stylesheet rebuilt on a design-token system: fluid type, intrinsic grids, dark mode, layered shadows, modern selectors. |
| **1.0** | Initial build — 28 pages, full schema coverage, redirects from the previous Netlify site. |

---

## Licence

**Code:** the HTML structure, CSS and JavaScript may be reused freely.

**Content:** all written content, case study data, metrics and the likeness of Dip Palit are © Dip Palit and are **not** licensed for reuse. The compliance and merchant cash advance guides in `blog/` and `resources/` represent substantial original work.

If you want the design without the content, take `assets/` and replace everything else.

---

## A note on the compliance content

The FMCSA and DOT material on this site is **educational and is not legal advice**. It reflects published federal requirements as understood by a compliance support specialist, not an attorney. Regulations change, and requirements differ for passenger carriers, hazmat carriers and intrastate operations.

Anyone editing these pages should preserve the disclaimers and the links to [fmcsa.dot.gov](https://www.fmcsa.dot.gov) and the [current eCFR Title 49](https://www.ecfr.gov/current/title-49). Do not add specific regulatory claims — deadlines, thresholds, dollar figures — without verifying them against the current regulation first. Inaccurate compliance guidance is worse than none.
