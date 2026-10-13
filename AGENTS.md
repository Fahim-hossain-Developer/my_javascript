# AGENTS.md

## What this repo is

A plain static site: hand-written HTML and JavaScript practice exercises.
There is **no backend, no database, and no external service**, so it needs no
credentials and no migrations. `index.html` at the repo root is the entry page;
the exercises live in `HTML 5 FULL/` and `Javascript/` (folder names contain
spaces, so their URLs must be percent-encoded, e.g.
`/Javascript/Class-03%20Variable/index.html`).

## Running it

```
docker compose -f docker-compose.base44.yml up -d --build
```

`package.json` / `package-lock.json` exist only to run the dev server (Vite) —
the site itself has no build step and no dependencies of its own. `node_modules`
lives in the `web_node_modules` named volume, so it is never written into the
working tree (and is gitignored as a safety net).

The `web` service bind-mounts the checkout at `/app` and runs `npm install`
followed by the Vite dev server on port 3000. Because the source is mounted,
file edits are served straight away without rebuilding the image.

## Verifying it works

```
curl -s http://localhost:3000/ | grep -q '@vite/client' && echo dev-server-ok
```

That marker only appears when the running server is Vite's dev server reading
the mounted source. If it is missing, port 3000 is being served by a prebuilt or
production bundle and the edit loop is broken.

## Sandbox notes

- `BASE44_PREVIEW_MODE` is passed through to the service bare; nothing in the
  app reads it, and no sandbox-only code changes were needed.
- `__VITE_ADDITIONAL_SERVER_ALLOWED_HOSTS` is passed through bare so Vite
  (>= 6.1) accepts the preview's external hostname. Do not hardcode any
  `BASE44_*` host value into a file — pass the variable instead.
- The preview proxy does not carry Vite's HMR websocket, so an edit appears on
  the next page load rather than instantly. After editing, call
  `reload_preview` (or refresh) to see the change.
