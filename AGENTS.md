# Base44 Dev Environment

## Overview
This is the GitHub Docs (docs.github.com) Next.js 16 app with a custom Express server.
The app uses `next({ dev: true })` in development mode with HMR through the Express server.

## Setup
- **Runtime**: Node 24 (engines: `^24 || ^26`)
- **Package manager**: npm (package-lock.json present)
- **Dev server**: `npm start` → `cross-env NODE_ENV=development ENABLED_LANGUAGES=en nodemon src/frame/server.ts`
- **Default port**: 4000, overridden to 3000 via `PORT=3000` in compose
- **Healthcheck**: `GET /healthcheck` returns 200

## Running
```bash
docker compose -f docker-compose.base44.yml up -d --build
```

## Key details
- `npm ci --ignore-scripts` is used to skip the husky `prepare` hook; `patch-package` runs manually after install to apply the one patch in `patches/`.
- No external credentials are required to boot. Optional env vars (ELASTICSEARCH_URL, HYDRO_ENDPOINT, CSE_COPILOT_SECRET) are for search, analytics, and AI search features — the app works without them.
- StatsD is auto-mocked when `MODA_PROD_SERVICE_ENV !== 'true'` (always true in dev).
- `ENABLED_LANGUAGES=en` limits the app to English content only (no translations directory needed).
- `CHOKIDAR_USEPOLLING=true` enables file-watch polling for nodemon/Next.js HMR in the bind mount.
- `allowedDevOrigins` in `next.config.ts` is conditionally extended for the Base44 preview origin when `BASE44_PREVIEW_MODE === '1'`.

## Verification
- `curl -f http://localhost:3000/healthcheck` → 200
- `curl -s http://localhost:3000/ | head -20` → GitHub Docs homepage HTML
