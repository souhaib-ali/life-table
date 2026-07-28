# Mon Tableau de Vie (Life Dashboard)

A personal, single-page life-tracking dashboard. Everything — markup, styles,
and logic — lives in one file: **`index.html`**. There is no build step, no
package manager, and no server code: you open the file (or its hosted URL)
and it just runs in the browser.

## What it does

The dashboard is organized into sections (left sidebar), each tracking a
different part of daily/weekly life:

- **Vue d'ensemble** — overview of all sections at a glance
- **Bilan express** — a one-page daily checklist across every section
- **Santé** — health habits (sleep, water, meals, exercise, screen time...)
  plus health metrics (weight, BMI, blood pressure, heart rate)
- **Travail** — work checklist (punctuality, meetings, reporting, deep work...)
- **Cabinet** — law-firm case tracking (litigation status, quarterly reports)
- **Études** — study tracking
- **Projet écriture** / **Projet formation** — long-running personal projects
- **Religion** — daily prayers and religious habits
- **Budget** — monthly savings/spending tracker
- **Lexique** — a personal vocabulary list (English/Français/العربية)
- **Calendrier** — meetings and tasks with reminders
- **Interprétariat** — interpretation missions and payments
- **Bilan général** — evolution charts (day/week/month/quarter) via Chart.js

Each section has weekly checklists, progress gauges, and (for several
sections) a "generate report" button that produces a standalone printable
HTML report.

### How data is stored

- Data is kept in the browser's `localStorage` (key `ldv7`, with a mirrored
  backup key) so the app works fully offline.
- If configured, it also syncs to a **Supabase** project (see the
  `SUPABASE_URL` / `SUPABASE_KEY` constants near the top of the `<script>`
  block) so the same data is available across devices, behind a simple
  login gate.
- You can **Export**/**Télécharger** a backup (JSON or self-contained HTML)
  and **Importer** it back at any time from the top bar — do this before any
  risky change.

### Tech stack

- Plain HTML/CSS/JS (no framework, no bundler)
- [Chart.js](https://www.chartjs.org/) for graphs (loaded from CDN)
- [Supabase JS client](https://supabase.com/) for auth + cross-device sync (CDN)
- [Font Awesome](https://fontawesome.com/) for icons (CDN)
- Google Fonts (Inter, Space Grotesk, Sora)

## Running it locally

No install needed:

```bash
# just open it in a browser
open index.html        # macOS
xdg-open index.html     # Linux
```

Or serve it (recommended if you hit any CORS/localStorage quirks with
`file://` URLs):

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Deploying

Since it's a single static file, the simplest option is **GitHub Pages**:
Settings → Pages → deploy from the `main` branch (root). Any push to `main`
that updates `index.html` will update the live site.

---

## Workflow: updating the dashboard from a normal Claude chat

Most day-to-day tweaks to this dashboard (add a habit, change a color, add a
section, fix a bug) are made by asking Claude in a **normal chat** (claude.ai
or the app) to regenerate `index.html`, rather than through a coding session
like this one. Because the whole app is one file, here's the process to do
that safely:

1. **Back up your data first.** Open the live dashboard and click
   **Export**/**Télécharger** to save a current backup (JSON or HTML) of
   your data to your computer. This is independent of the app's code, but
   it protects you in case something goes wrong during the update.

2. **Give Claude the current file as context.** Paste the current
   `index.html` (or attach it) and describe the change you want. Since this
   is one large file, being specific helps a lot — e.g. "in the `Santé`
   section, add a new checklist item called 'Méditation 10 min'" rather than
   "improve the health section."

3. **Ask for the full updated file, not a snippet.** Because there's no
   build process to merge partial patches, always ask Claude to return the
   *entire* `index.html` with your change applied, so you can replace the
   file wholesale. If the response gets cut off, ask Claude to continue
   from where it stopped.

4. **Save the generated HTML.** Copy the code Claude gives you (or download
   it if the chat offers a file/artifact) and save it as `index.html`,
   replacing the old one on your machine.

5. **Sanity-check before publishing:**
   - Open the new `index.html` locally in a browser.
   - Confirm the `SUPABASE_URL` and `SUPABASE_KEY` constants near the top of
     the `<script>` block are unchanged (unless you meant to change them).
   - Log in and confirm your existing data still loads (it's read from
     `localStorage`/Supabase, not from the file, so this should be
     unaffected — but verify).
   - Click through the section(s) you changed and a couple of others to
     check nothing else broke.

6. **Commit and push the change** to this repository:
   ```bash
   git add index.html
   git commit -m "Describe what changed"
   git push
   ```
   (Or use GitHub's "Upload files" button in the web UI, which is how this
   file has been updated before.)

7. **If something breaks**, restore the previous version with
   `git checkout <previous-commit> -- index.html`, or re-import the backup
   you exported in step 1 from within the app.

### Tips for better results in normal chat

- Mention which **section id** you're changing (e.g. `sante`, `travail`,
  `budget`, `lexique`) — the sections are defined in the `SC` array near the
  top of the script, and checklist items for habit-tracking sections are
  defined in the `OB` object right after it.
- If a change is purely visual, mention whether it should apply to both the
  dark theme (default) and the light theme (`body.light` CSS overrides).
- Keep changes scoped — asking for several unrelated changes in one prompt
  makes it harder to verify the resulting file and increases the chance of
  the response being truncated.
