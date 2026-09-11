# TMLAK Property — Production Deployment Package

This is the existing TMLAK Property single-file website, prepared for
static deployment on Vercel. No redesign, no rebuild — the original
HTML/CSS/JS is untouched except for one bug fix (see below).

## What's in this folder
- `index.html` — the site (renamed from the uploaded file so Vercel serves it as the homepage)
- `vercel.json` — minimal config (cache headers only; no rewrites needed — see note below)
- `robots.txt` / `sitemap.xml` — placeholders, **replace `REPLACE-WITH-YOUR-DOMAIN.com` with your real domain**
- `.gitignore` — standard ignores for OS/editor files and the local `.vercel` folder

## Why no framework conversion
This is a static, self-contained HTML file using CSS variables and vanilla JS
with hash-based client-side routing (e.g. `#projects`, `#about`). Vercel
serves static HTML natively with zero build step — converting this to
Next.js/React would add complexity for no benefit and risk changing the
site's behavior. Treat this as "Other" / static in Vercel's framework
picker.

## Deploying

### Push to GitHub
```bash
git init
git add .
git commit -m "Production-ready TMLAK Property site"
git branch -M main
git remote add origin https://github.com/<your-username>/<repo-name>.git
git push -u origin main
```

### Connect to Vercel
1. In the Vercel dashboard: **Add New → Project → Import** the GitHub repo above.
2. Framework Preset: **Other**
3. Build Command: *(leave empty)*
4. Output Directory: *(leave empty / root)*
5. Deploy.

Because routing is hash-based (`#page`), there is nothing after the `#`
that ever reaches the server, so no rewrite rules are required — Vercel
just needs to serve `index.html` at `/`, which it does automatically.

## Bug fixed during this pass
Every project card is built dynamically in JS (`projectCardHTML()`).
It previously linked to `href="project-burj-binghatti-jacob-co.html"`
— a physical file that doesn't exist anywhere in the project — instead
of using the same `href="#project-burj-binghatti-jacob-co"` hash pattern
every other link on the page uses. In normal left-click use, the page's
own click handler intercepts the click and calls `showPage()`, so it
"worked" — but a middle-click / "open in new tab", a disabled-JS
browser, or a search-engine crawler following that link would 404. Now
fixed to match the rest of the site.

## Known issues that need your decision (not changed — flagging only)
1. **Contact forms don't send anywhere.** Both forms on the page
   (`Book a Consultation` and the full contact form) just show an
   on-page "Thanks" message via `onsubmit="event.preventDefault()..."`.
   No data is emailed or stored anywhere. If you want actual leads to
   reach an inbox, this needs a form backend (e.g. Formspree, like the
   ibrahimali-dubai.com site uses) — happy to wire this up once you
   confirm the destination email.
2. **All "Projects" grid cards link to the same detail page**
   (Burj Binghatti Jacob & Co) by design — a comment in the code says
   it's the only fully built-out template page in this prototype.
   That's expected prototype behavior, not a bug, but worth confirming
   before launch.
3. **Property images are hotlinked from Unsplash** (`images.unsplash.com`),
   not hosted assets. This works fine functionally, but (a) they're
   generic stock photos, not real TMLAK listings, and (b) hotlinking to
   a third party means you don't control if/when those specific images
   change or disappear. Fine to launch with, but worth revisiting.
4. **SEO is limited by the single-page hash-routing architecture.**
   Search engines see one URL (`/`) with one `<title>`/meta description;
   the per-"page" titles only update client-side after JS runs. This is
   inherent to how the site was built (not something introduced by this
   deployment pass) — a real fix would mean separate routes/pages, which
   is a design decision, not a deployment one, so I left it alone.
5. **`robots.txt` / `sitemap.xml` are new** (the original file didn't
   include them) — fill in your real production domain before going live.
