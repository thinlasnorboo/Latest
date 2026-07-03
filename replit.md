# LA RC Cafe

A restaurant + RC (remote control car) track venue website — customers browse the menu, book track time/services, shop RC gear, and view the gallery; admins manage content via an admin panel.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080)
- `pnpm --filter @workspace/rc-cafe run dev` — run the frontend (port 23330)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite (`artifacts/rc-cafe`)
- API: Express 5 (`artifacts/api-server`)
- DB: PostgreSQL + Drizzle ORM (`lib/db`)
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec in `lib/api-spec`)
- Object storage: Google Cloud Storage via `lib/object-storage-web`
- Build: esbuild (CJS bundle)

## Where things live

- `artifacts/rc-cafe/src/pages` — Home, Menu, Shop, Services, Gallery, Book, Contact, Cart, Checkout, Admin, AdminLogin
- `artifacts/rc-cafe/src/context/CartContext.tsx` — shopping cart state
- `artifacts/api-server/src/routes` — bookings, contact, gallery, menu, products, services, settings, slides, stats, storage, adminAuth
- `lib/api-spec/openapi.yaml` — source-of-truth API contract
- `lib/db/src/schema` — bookings, menu, contact, products, slides, bankDetails

## Product

- Public site: browse menu (coffee/snacks/pizza/RC track charges/rentals/combos), shop RC cars/parts/accessories/apparel, view services, book track time, contact form, gallery.
- Admin panel (`/admin`, login at `/admin/login`): manage slides, bank details, menu, products, bookings.

## User preferences

- User communicates in Roman Urdu/Hindi — reply in that register.

## Gotchas

- Frontend calls the API via relative `/api/...` paths (not full URLs) — the shared Replit proxy routes `/api` to the api-server artifact automatically. Don't hardcode ports or use `setBaseUrl`.
- Menu and Products routes auto-seed the DB with starter data on first `GET` if the table is empty — no separate seed script needed.

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
