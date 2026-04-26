# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

@AGENTS.md

## Commands

```bash
npm run dev      # start dev server (Turbopack, outputs to .next/dev)
npm run build    # production build (Turbopack by default)
npm run start    # start production server
npm run lint     # run ESLint directly (not via next lint — that command is removed in v16)
```

No test framework is configured yet.

## Architecture

This is a **Next.js 16** App Router project with TypeScript, Tailwind CSS v4, and React 19.2. Source lives under `src/app/` using the file-system router.

- `src/app/layout.tsx` — root layout; wraps every page with Geist fonts and Tailwind base styles
- `src/app/page.tsx` — home route (`/`)
- `src/app/globals.css` — global styles using Tailwind v4's `@import "tailwindcss"` and `@theme inline` syntax
- `public/` — static assets served from `/`
- Path alias: `@/*` → `src/*`

## Next.js 16 breaking changes to keep in mind

**Async Request APIs are fully async** — `cookies()`, `headers()`, `draftMode()`, `params`, and `searchParams` must be `await`-ed. Synchronous access was removed.

**`middleware` renamed to `proxy`** — the file must be `proxy.ts` and export a function named `proxy`. The `edge` runtime is not supported in `proxy`; use `middleware` if you need edge.

**`next lint` is gone** — use `eslint` directly (already wired in `package.json`). `next build` no longer auto-runs linting.

**Caching API changes:**
- `revalidateTag` now requires a second `cacheLife` profile argument: `revalidateTag('tag', 'max')`
- `cacheLife` and `cacheTag` are stable — drop any `unstable_` prefix
- PPR is now `cacheComponents: true` in `next.config.ts` (not `experimental.ppr`)

**`serverRuntimeConfig` / `publicRuntimeConfig` removed** — use `process.env` / `NEXT_PUBLIC_` env vars instead.

**Turbopack is default** — `next dev` and `next build` both use Turbopack. Pass `--webpack` to opt out. Turbopack config moves from `experimental.turbopack` to top-level `turbopack` in `next.config.ts`.

**Parallel routes require `default.js`** — every `@slot` must have an explicit `default.js` or builds fail.

**`next/image` defaults changed** — `minimumCacheTTL` is 4 hours (was 60 s), `qualities` defaults to `[75]`, `16` removed from `imageSizes`, max 3 redirects, local IP optimization blocked by default, `images.domains` is deprecated (use `remotePatterns`).

**ESLint flat config** — `eslint.config.mjs` is the expected format (already in place). Legacy `.eslintrc` is being phased out.
