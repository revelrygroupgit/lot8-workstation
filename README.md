# Lot 8 Workstation

Internal operating hub for the Lot 8 live-commerce startup. Single-file HTML deployed to Vercel, with a Supabase real-time backend for the shared taskboard and waitlist capture.

**Live URL:** https://lot8-workstation.vercel.app  
**GitHub:** https://github.com/revelrygroupgit/lot8-workstation  
**Vercel project:** `lot8-workstation` (team: `team-3461s-projects`)

---

## What it is

A self-contained workstation that replaces a scattered mix of Notion/Slack/docs with one URL the whole team can open. It has three top-level sections:

| Section | Tabs | Colour |
|---|---|---|
| **Action Center** | Notes, Action Plan, Taskboard | Green |
| **Lot 8 Strategy** | Vision, Platform, Value Props, AI Core, Business Model, Financials, Go-to-Market, Roadmap, Competitive, Raise | Orange |
| **Landing Page** | Draft marketing page with live waitlist form | Amber |

---

## Tech stack

| Layer | What | Why |
|---|---|---|
| Frontend | Single `index.html` — no build step, no framework | Zero ops overhead |
| Hosting | Vercel static | CI-free push-to-deploy |
| Database | Supabase (Postgres + Realtime) | Shared taskboard state + waitlist |
| Fonts | Bebas Neue (display), DM Sans (body) via Google Fonts CDN | Matches brand |
| Supabase JS | `@supabase/supabase-js@2` via jsDelivr CDN | No npm required |

---

## Prerequisites

- [Vercel CLI](https://vercel.com/docs/cli): `npm i -g vercel`
- Vercel account with access to team `team-3461s-projects`
- GitHub access to `revelrygroupgit/lot8-workstation`
- Supabase project credentials (see [docs/SUPABASE.md](docs/SUPABASE.md))

---

## Local development

```bash
# Clone
git clone https://github.com/revelrygroupgit/lot8-workstation.git
cd lot8-workstation

# Open directly in browser — no server needed
open index.html
```

> The taskboard and waitlist form require internet to reach Supabase. Everything else (all tabs, meeting notes, action plan, pitch deck) works fully offline.

---

## Deploy to Vercel

```bash
# Deploy to production
vercel --yes --scope team-3461s-projects --prod
```

The Vercel config (`vercel.json`) serves `index.html` for all routes. No build step runs.

---

## Making changes

All content lives in `index.html`. It is a single file with three zones:

1. **CSS** — design system variables and component styles (top of `<style>`)
2. **Data** — `MEETINGS` array, `SEED_TASKS` array (top of `<script>`)
3. **Render functions** — one function per tab, returning an HTML string

After editing, commit and deploy:

```bash
git add index.html
git commit -m "your message"
git push origin main
vercel --yes --scope team-3461s-projects --prod
```

### Adding a meeting (from Granola)

See [docs/CONTENT.md](docs/CONTENT.md) for the full format spec. The short version: prepend a new entry to the `MEETINGS` array at the top of the script block. The first item is always the default meeting shown on the Notes tab.

### Adding or editing tasks

See [docs/CONTENT.md](docs/CONTENT.md). Tasks are seeded from `SEED_TASKS` and stored in Supabase. New seed entries are merged non-destructively on first load.

---

## Supabase

Two tables: `tasks` (kanban) and `waitlist` (landing page signups). Full schema, RLS policies, and setup SQL are in [docs/SUPABASE.md](docs/SUPABASE.md).

The Supabase credentials are embedded directly in `index.html` (anon key only — read + insert, no admin access). This is intentional for a public-facing static site; the anon key is safe to expose.

---

## Repository structure

```
lot8-workstation/
├── index.html          # The entire workstation — edit this
├── vercel.json         # Vercel static routing config
├── .gitignore
├── docs/
│   ├── SUPABASE.md     # Database schema + setup SQL
│   ├── CONTENT.md      # How to add meetings and tasks
│   └── BRAND.md        # Design system reference
└── README.md           # This file
```

---

## Automated Granola sync

A scheduled cloud agent (Claude Code Routines) runs daily at 11am UTC (9pm AEST) to:
1. Pull new Lot 8 meetings from Granola
2. Prepend them to the `MEETINGS` array in `index.html`
3. Push to GitHub and redeploy to Vercel

See [docs/CONTENT.md](docs/CONTENT.md) for the meeting format the agent uses.

---

## Handover checklist

If someone new takes over:

- [ ] Add them to the Vercel team (`team-3461s-projects`) at vercel.com
- [ ] Add them as a collaborator on `revelrygroupgit/lot8-workstation`
- [ ] Share Supabase project access (`keywwvhzlgryfbqhbpim`) at supabase.com
- [ ] Share this README and the `/docs` folder
- [ ] Run `vercel whoami` to confirm Vercel CLI auth works
- [ ] Test a deploy with `vercel --yes --scope team-3461s-projects --prod`
