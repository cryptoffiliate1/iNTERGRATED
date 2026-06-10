# Cryptoffiliate — Supabase Integration

## What changed

Your original design and color scheme are **100% preserved**. Only the data layer was replaced.

| File | Change |
|------|--------|
| `src/lib/supabase.server.ts` | **NEW** — Supabase client + TypeScript types |
| `src/lib/api/listings.functions.ts` | **NEW** — All server functions (replaces mock-data calls) |
| `src/routes/directory.index.tsx` | Updated — loader fetches from `v_listings_full` view |
| `src/routes/directory.$programId.tsx` | Updated — loads listing + attrs + affiliate program + reviews |
| `src/routes/exchanges.tsx` | Updated — loads exchanges vertical + saved comparisons |
| `src/routes/compare.$slug.tsx` | **NEW** — Dynamic comparison pages at `/compare/x-vs-y` |
| `src/components/site-header.tsx` | Updated — added Compare to nav |
| `package.json` | Added `@supabase/supabase-js` |
| `.env.example` | **NEW** — env var template |

## Setup

### 1. Run the migrations
Apply the 7 SQL migration files (from `cryptoffiliate-migrations.zip`) to your Supabase project in order, using the SQL editor or Supabase CLI.

### 2. Add env vars
```bash
cp .env.example .env
# Then fill in your Supabase URL and anon key from supabase.com → Settings → API
```

### 3. Install and run
```bash
bun install   # adds @supabase/supabase-js
bun dev
```

## How the data layer works

All data fetching uses `createServerFn` from TanStack Start — these run server-side only, so your Supabase keys are never exposed to the browser.

```
Route loader
  └── createServerFn (server-only)
        └── Supabase client (reads VITE_SUPABASE_URL from process.env)
              └── query v_listings_full view
```

## Comparison pages

Comparison pages work via URL: `/compare/ledger-nano-x-vs-trezor-model-t`

The URL is split on `-vs-` to get the slugs, then `getComparison()` fetches attributes for both listings from `listing_attributes WHERE is_comparable = true`.

To add a new comparison, just navigate to `/compare/[slug-a]-vs-[slug-b]` — it works for any two listings in the database.

## Adding more listings

Use the Supabase dashboard or a script — no code changes needed. The directory, comparison engine, and filter pages all derive from database rows automatically.
