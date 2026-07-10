# AGENTS.md — Whatsender API

## Project structure

Hybrid PHP + JS monolith. Two independent entrypoints:

- **Laravel 12** (PHP 8.2+): Web dashboard, user management, billing, templates, scheduling, webhooks. Entry: `public/index.php` (PHP-FPM).
- **Node.js / Express** (Node >=18.16): WhatsApp WebSocket gateway via `baileys`. Entry: `app.js`. Serves on port defined by `WA_SERVER_PORT` (default 8000).

The two communicate via HTTP — Laravel calls the Node server at `WA_SERVER_URL`.

## Key directories

| Path | Purpose |
|---|---|
| `app/` | Laravel PHP app (Models, Http/Controllers, Jobs, etc.) |
| `Modules/Wacore/` | nwidart/laravel-modules module for core WhatsApp features (enabled in `modules_statuses.json`) |
| `controllers/` | Node.js Express route handlers |
| `middlewares/` | Node.js Express middlewares (`authenticationValidator` currently passes through — auth is effectively **disabled**) |
| `routes/` | Node.js Express route definitions (`*.js`) + Laravel PHP routes (`*.php`) |
| `sessions/` | WhatsApp auth state + store files (gitignored) |
| `store/` | In-memory chat store with JSON file persistence |
| `database/migrations/` | 32 Laravel migrations |
| `tests/` | PHP only (PHPUnit). No JS test framework configured. |

## Commands

```bash
# Node.js WhatsApp gateway
node .                          # start Express server (alias: npm start)
npx eslint .                    # JS lint
npx prettier --check .          # JS format check
npx prettier --write .          # JS format fix

# Laravel
php artisan serve               # dev server
php artisan migrate             # run DB migrations
php artisan queue:work          # process queued jobs
./vendor/bin/phpunit            # PHP tests
./vendor/bin/pint               # PHP lint (Laravel Pint)
```

Required order: `lint -> format` (ESLint catches issues Prettier doesn't).

## Code style (JS)

- `"type": "module"` — all imports use ESM (`import`/`export`)
- No semicolons, single quotes, 4-space indent, 120 print width
- ESLint + Prettier enforced via eslint-plugin-prettier (`"prettier/prettier": "error"`)
- Exceptions: `radix: off`, `new-cap: off`, `no-return-assign: off`, `no-await-in-loop: off`

## WhatsApp API details

- Session authentication: QR code (default) or pairing code (`typeAuth: "code"` + `phoneNumber` body param)
- Sessions auto-recover on server restart from `sessions/md_*` files
- Max reconnect attempts: `WA_SERVER_MAX_RETRIES` (default 5)
- Global auth token in env: `AUTHENTICATION_GLOBAL_AUTH_TOKEN` (middleware currently passes all requests through)
- Webhook URL configured via `APP_WEBHOOK_URL` env. Allowed events: `CONNECTION_UPDATE`, `MESSAGES_UPSERT` (or `ALL`)
- Media in webhooks as base64 when `APP_WEBHOOK_FILE_IN_BASE64=true`

## Testing

- No JS test framework or test scripts exist. PHP tests with `./vendor/bin/phpunit`.
- PHP test suites: `tests/Unit` and `tests/Feature` (Test.php suffix).
