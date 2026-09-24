# Meta-Product Manager (no-build version)

Same app as before — meta-products, features, options, constraints,
white/black list uploads, and Option/Compatibility `.xlsx` export — but with
**zero build tooling**. No `npm install`, no Vite, no bundler, ever.

**How it works:**
- The frontend is plain HTML/JS. React is loaded from a CDN as a `<script>`
  tag (like jQuery used to be). JSX in the `.js` files is transformed
  in-browser by Babel Standalone (also from a CDN) — there's no separate
  compile step, you just edit a file and refresh the page.
- Excel reading/writing happens in the browser via SheetJS (also CDN).
- The Netlify Functions (`netlify/functions/*.js`) use only Node's built-in
  `fetch` to call Supabase's REST API directly — no `@supabase/supabase-js`,
  no `xlsx` package, no `package.json`, no `node_modules` at all.

You can open `public/index.html` in spirit like any static site: copy the
files, push them to GitHub, and Netlify serves them as-is.

## 1. Set up Supabase

1. Create a project at [supabase.com](https://supabase.com).
2. Open the SQL editor and run everything in `supabase/schema.sql`.
3. Go to **Project Settings → API** and copy:
   - `Project URL` → you'll set this as `SUPABASE_URL`
   - `service_role` secret key → you'll set this as `SUPABASE_SERVICE_KEY`

## 2. Push to GitHub

```bash
cd meta-product-manager-simple
git init
git add .
git commit -m "Initial commit: meta-product manager (no-build)"
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repo>.git
git push -u origin main
```

No `npm install` at any point — there's nothing to install.

## 3. Deploy to Netlify

1. In Netlify: **Add new site → Import an existing project** → pick your GitHub repo.
2. Build settings: leave the **build command blank** (there isn't one).
   Publish directory: `public` (already set in `netlify.toml`).
3. In **Site settings → Environment variables**, add:
   - `SUPABASE_URL`
   - `SUPABASE_SERVICE_KEY`
4. Deploy. Netlify serves the static files as-is and runs the functions —
   no build step happens at all.

## Trying it locally (optional)

You don't need this to deploy, but if you want to preview it on your own
machine first, install the Netlify CLI as a one-time global tool (this is
not a project dependency, so it doesn't count as "npm install for the
project"):

```bash
npm install -g netlify-cli   # one-time, global — not part of this project
cp .env.example .env         # fill in SUPABASE_URL and SUPABASE_SERVICE_KEY
netlify dev                  # serves public/ and the functions together
```

## Screens

| Story | File |
|---|---|
| Lookup Meta-Products | `public/app/pages/MetaProductLookup.js` |
| Create a new Meta-Product | Modal inside `MetaProductLookup.js` |
| Add features to a Meta-Product | `public/app/pages/FeatureManagement.js` |
| Add feature details / edit | `public/app/components/FeatureDetailModal.js` |
| Add constraint from a feature | `public/app/components/AddConstraintModal.js` |
| View constraints to/from/including a feature | `public/app/pages/ConstraintsView.js` |
| Manage/upload white or black list files | `public/app/pages/ListFileManager.js` |
| Map features to options/choices (semi-auto populate) | `public/app/pages/OptionMapping.js` |
| Review output + download Option/Compatibility files | `public/app/pages/MetaProductOutput.js` |

## Notes

- Routing is a tiny custom hash router (`public/app/router.js`) — URLs look
  like `yoursite.com/#/meta-products/<id>/features`.
- No authentication is included — anyone with the URL can use the app.
- The CDN scripts (React, Babel, SheetJS) require an internet connection to
  load; if you need a fully offline version, download those three files and
  reference them locally instead of via `unpkg.com`.
- Auto-suggest rules for Option Mapping live in `public/app/utils/suggestions.js`.
