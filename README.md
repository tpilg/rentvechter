# RentVechter

A static, single-file web app that calculates a Dutch rental property's WWS points, its estimated legal maximum rent, and (if overpaying) generates a draft letter to the landlord or Huurcommissie. See `rentvechter-spec.md` for the full PRD and sourcing.

No build step, no dependencies, no backend — `index.html` is the entire app (HTML + CSS + vanilla JS inline).

## Run it locally

Just open `index.html` directly in a browser, or serve it so relative paths behave like production:

```bash
npx serve .
```

## Deploy to Vercel

This is a zero-config static site, so the Vercel CLI can deploy it straight from this folder — no `vercel.json` needed.

```bash
# one-time setup, if you don't have the CLI yet
npm i -g vercel

# from inside this project folder
vercel login      # opens a browser to authenticate, if not already logged in
vercel --prod      # deploys and prints the live URL
```

When prompted:
- "Set up and deploy?" → yes
- Framework preset → **Other** (it's plain static HTML, no framework)
- Build command / output directory → leave blank / default (nothing to build)

### If you're using Claude Code

Open a terminal in this folder, run `claude`, and paste:

> Deploy this static site to Vercel. It's a single `index.html` with no build step or framework — run `vercel --prod` from this directory, accept the zero-config defaults (no build command, no output directory override), and give me back the production URL when it's done.

## Project files

- `index.html` — the app (calculator + letter generator)
- `rentvechter-spec.md` — PRD with sourced legal/regulatory facts

## Next steps (from the PRD roadmap)

- v1.2: shareable calculation links (no login)
- v2: accounts, live Huurcommissie case tracking, address-based auto-lookup of energy label/WOZ value
- v3: landlord/building reputation layer

