# Base44 Dev Environment

## What this project is
A single static HTML page (`deepseek_html_20260930_b6fa45.html`) — the PinS
(Portugueses na Ciência / Portuguese in Science, Singapore) landing page.
Self-contained: embedded CSS and JS, no backend, no build step, no dependencies.

## Running it
```
docker compose -f docker-compose.base44.yml up -d
```
- Served by `nginx:stable` on host port 3000 (container port 80).
- The repo is bind-mounted read-only at `/usr/share/nginx/html`, so edits to the
  HTML file are reflected immediately (no rebuild needed for content changes).
- `nginx.base44.conf` sets the oddly-named HTML file as the directory index.
- `Dockerfile.base44` installs `curl` (for the healthcheck) and forces nginx
  workers to run as `root` — required because the sandbox bind-mount directory
  has `0700` perms that the default non-root `nginx` worker cannot read.
- Rebuild only when `Dockerfile.base44` or `nginx.base44.conf` change:
  `docker compose -f docker-compose.base44.yml up -d --build`.

## Page logic
The HTML file's `<script>` block implements all client-side behaviour: PT/EN
i18n toggle (`setLang`), auth modal (`openAuth`/`closeAuth`/`showLogin`/
`showRegister`), localStorage-backed login & registration (`login`/`register`/
`logout`), member directory rendering, events with admin add-form, and
initiatives. Demo admin: `admin@ptassociationsg.com` / `admin123`. All state
persists in `localStorage` (keys prefixed `pins_`). No backend.

## Verification
- `curl -s -o /dev/null -w "%{http_code}" http://localhost:3000/` → `200`
- `docker compose -f docker-compose.base44.yml ps` → `web` is `healthy`
