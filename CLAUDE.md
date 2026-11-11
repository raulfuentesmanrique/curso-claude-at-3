# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Resttek: restaurant management platform. npm-workspaces monorepo (Node 22+, npm 10+) in `packages/`: `api` (Express 5 + TypeScript + SQLite), three Angular 21 apps (`web-admin` :4200, `web-empleados` :4201, `web-clientes` :4202) and `web-shared` (shared Angular library: auth, interceptors, common components). The docs in `docs/` (Spanish) cover architecture, data model and glossary: `docs/arquitectura/`, `docs/dominio/`.

## Commands (run from the repo root)

```bash
npm install                  # installs every workspace
npm run seed                 # populate SQLite with test data (idempotent, INSERT OR IGNORE)
npm run dev:api              # API on :3000 (tsx watch); check with GET /health
npm run dev:admin            # also dev:empleados, dev:clientes (ng serve with /api proxy to :3000)
npm test                     # API tests only (vitest run)
```

Single API test (from `packages/api`): `npx vitest run src/services/order.service.test.ts` or `npx vitest run -t "test name"`. Watch mode: `npm run test:watch -w @resttek/api`. The frontends have no test script wired at the root and no lint config; `ng build` is available per package (`npm run build -w @resttek/web-admin`).

Seed credentials: the password of every user equals their email (e.g. `admin@resttek.com`/`admin@resttek.com`, `gerente1@resttek.com`, `cocinero1@resttek.com`, `camarero1@resttek.com`, `cliente1@resttek.com`).

## Apps

Start the API first (frontends proxy `/api` to :3000). Run `npm run seed` once beforehand. Stop any app with `Ctrl+C`, or if left running in background `fuser -k <port>/tcp` (no dedicated stop commands exist).

| App | Package | Location | Purpose | Start | Port | URL |
| --- | --- | --- | --- | --- | --- | --- |
| API | `@resttek/api` | `packages/api/` | REST backend: auth, restaurants, dishes, ingredients, employees, orders | `npm run dev:api` | 3000 | http://localhost:3000 (`/health`) |
| web-admin | `@resttek/web-admin` | `packages/web-admin/` | Admin panel for admin and managers: restaurants, dishes, ingredients, employees | `npm run dev:admin` | 4200 | http://localhost:4200 |
| web-empleados | `@resttek/web-empleados` | `packages/web-empleados/` | Staff app: kitchen prepares orders (Preparando → Listo); waiters handle bar and floor and deliver ready items | `npm run dev:empleados` | 4201 | http://localhost:4201 |
| web-clientes | `@resttek/web-clientes` | `packages/web-clientes/` | Customer app: register, browse menu, cart, place orders, "Mis Pedidos" | `npm run dev:clientes` | 4202 | http://localhost:4202 |

`web-shared` (`packages/web-shared/`) is not an app: shared Angular library (auth, interceptors, common components) used by the three frontends.

### Dependencies between apps

- **Code:** the three frontends depend on `@resttek/web-shared`. The API depends on no other package (uses SQLite directly). Frontends never import each other.
- **Runtime:** `web-admin`, `web-empleados`, `web-clientes` → proxy `/api` → API → SQLite. Frontends do not talk to each other; they only talk to the API.
- **The API must be running** for frontends to be useful: they start without it (`ng serve` doesn't need it) but login and every data call fail. The API works standalone (e.g. with `curl`). Only start the frontends you need.
- **Functional flow through shared data:** admin/managers create dishes, ingredients and employees (web-admin) → customers see the menu and place orders (web-clientes) → orders show up for kitchen/waiters (web-empleados) → customer sees the updated status in "Mis Pedidos".
- Frontends need `npm install` at the root first, otherwise `ng: not found`.

## API architecture (`packages/api/src`)

Two architectural styles coexist; follow the style of the context you are editing:

- **Hexagonal/DDD** in `contexts/employee/` (`domain` → `application` use cases → `infrastructure` with Sqlite repo, Bcrypt auth service, and `http/` controllers/routes/`dependencies.ts` for wiring). Also holds auth/login/client registration. `contexts/shared/` has the `Email` value object, error handler and middlewares.
- **Layered** for restaurant, dish, ingredient, order: `models/`, `repositories/`, `services/`, `controllers/`, `routes/` as top-level folders. Repositories have in-memory mocks in `repositories/mocks/` used by service tests.

`app.ts` mounts everything under `/api/v1` (authenticated routes, plus `/api/v1/public/restaurants...` for the customer app; ingredients/dishes/employees are nested under `/restaurants/:restaurantId`). Errors are thrown as `AppError`/domain errors (`errors/`) and translated by `errorHandler`.

Path aliases (tsconfig, also used by vitest via `vite-tsconfig-paths`): `@config`, `@errors`, `@shared`, `@employee`, `@routes`, `@controllers`, `@services`, `@repositories`, `@models`, `@scripts`. ESM project: imports use `.js` extensions.

Database: `config/database.ts` uses `packages/api/resttek.db` normally and `:memory:` when `NODE_ENV=test`; tables are created automatically on startup.

## Frontend architecture

Standalone components, signals, zoneless (no zone.js), functional guards/interceptors, lazy-loaded routes. RxJS only for HTTP.
- `web-admin` and `web-empleados`: feature folders with `models/ pages/ services/ store/` (stores are signals wrapping HTTP via `firstValueFrom`); root component is `AppComponent`.
- `web-clientes`: smaller, models/services centralised in `core/`, components subscribe to Observables directly; root component is `App`.

## Other

`scripts/seed-issues.sh` creates GitHub issues from `scripts/issues.json` using `gh` and `jq` (see `scripts/README.md`).
