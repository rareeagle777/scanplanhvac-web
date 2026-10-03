# ScanPlanHVAC web portal (phase 1)

A static site for contractors: sign in with the Cloud Sync account, see projects, download every
PDF exported from the app, and reset a forgotten password. Projects are created and edited on the
iPhone; this site only reads. There is no build step — three files and Supabase's browser library
loaded from a CDN.

## Files

- `index.html` — the page (sign in, new password, project list, project detail).
- `app.js` — all behaviour. Talks to Supabase directly: `projects` table, `reports` and `weather`
  buckets, same row-level-security rules as the app.
- `styles.css` — styling.
- `config.js` — Supabase URL + publishable key (same public values as `SupabaseConfig.swift`).

## Hosting

Any static host works. Two options that fit what's already in use:

1. **GitHub Pages** (like the `scanplanhvac-terms` repo): push the `web/` folder to a repo, enable
   Pages on the main branch. The site will be at `https://<user>.github.io/<repo>/`.
2. **Render static site** (next to the backend): new Static Site, publish directory `web`, no build
   command.

Open the page over `https://` — Supabase sessions are stored in the browser and should not travel
over plain HTTP.

## Supabase settings to update once hosted

In the Supabase dashboard → Authentication → URL Configuration:

- **Site URL**: the hosted page URL. Password-reset emails land here; the page detects the
  recovery token and shows the "Set a new password" form.
- **Redirect URLs**: add the hosted page URL as well (the app's `scanplanhvac://auth-callback` stays
  in the list so reset links tapped on the phone keep opening the app).

Run `docs/CloudSync/supabase_schema.sql` again after pulling this change — it adds the `reports`
and `weather` buckets the page reads.

## Running locally

Serve the folder over HTTP (modules don't load from `file://`):

```sh
cd web && python3 -m http.server 8000
```

then open <http://localhost:8000>. Add `http://localhost:8000` to the Supabase redirect list if you
want password-reset links to come back to the local copy.

## What's deliberately not here yet

- Editing (phase 4). Sync replaces whole projects, newest wins, so a web editor needs a
  "changed elsewhere" check before it can save safely.
- Calculations and web-generated PDFs (phases 2–3). Those run the Swift engines on the server.
- Sign in with Apple: not enabled in Supabase yet; needs to be turned on for the app and the web
  at the same time so one person doesn't end up with two accounts.
