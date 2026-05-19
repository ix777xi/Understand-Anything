# Deploying to Railway

This monorepo contains two deployable apps:

- `homepage/` — Astro marketing site
- `understand-anything-plugin/packages/dashboard/` — React + Vite interactive dashboard (static)

Each has its own `railway.json` and `nixpacks.toml`. The trick on Railway is to point each **service's Root Directory** at the right subfolder and let the configs handle the workspace-aware install/build.

## Steps

1. Go to <https://railway.app/new> and choose **Deploy from GitHub repo**.
2. Pick `ix777xi/Understand-Anything`.
3. Railway will create a project. Inside that project, create **two services** (one per app):

### Service A — homepage

- **Settings → Source → Root Directory:** `homepage`
- **Settings → Networking:** Generate a public domain.
- Railway picks up `homepage/railway.json` automatically. Astro is built with pnpm and served via `astro preview` on `$PORT`.

### Service B — dashboard

- Add a second service to the same project: **+ New → GitHub Repo → same repo**.
- **Settings → Source → Root Directory:** `understand-anything-plugin/packages/dashboard`
- **Settings → Networking:** Generate a public domain.
- Railway picks up that folder's `railway.json`. The build runs `pnpm` from the repo root so the workspace dependency `@understand-anything/core` resolves, then serves the static `dist/` with `serve`.

## Environment variables

Neither app requires secrets to boot. Set these only if you customize:

- `NODE_VERSION` — pinned to 22 in nixpacks.toml; override here to change.
- For Astro, you can set `HOST=0.0.0.0` if you replace `astro preview` with a custom server.

## Notes

- `pnpm install --frozen-lockfile=false` is used because the upstream lockfile is occasionally out of sync with workspace versions; switch back to `--frozen-lockfile` once you confirm stability.
- The dashboard build depends on `@understand-anything/core` — the nixpacks config builds core first.
- If Railway's Nixpacks autodetect tries to run a default build command, the `railway.json` `buildCommand`/`startCommand` will override it.
