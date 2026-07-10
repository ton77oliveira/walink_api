# Project Context — Whatsender API

## Current version

**v5.0** (from `APP_VERSION` in `.env`). No git tags.

## Status

**Manutenção.** Projeto comercial (CodeCanyon) completo com entregas contínuas de correções e adaptações.

## Stack

| Camada | Tecnologia |
|---|---|
| **Web Framework** | Laravel 12 (PHP 8.2+) |
| **WhatsApp Gateway** | Node.js ≥18.16, Express 5, baileys 7.0.0-rc.6 |
| **Database** | MySQL (Laravel ORM + migrations) |
| **Queue** | `database` driver (MySQL-based job queue) |
| **Cache** | file driver (Laravel) |
| **Session** | file driver (Laravel) |
| **Storage** | Local `public/` disk + optional AWS S3 |
| **Auth (Laravel)** | Laravel Sanctum + spatie/laravel-permission |
| **Auth (Node)** | `AUTHENTICATION_GLOBAL_AUTH_TOKEN` (middleware currently **pass-through** — disabled) |
| **Payments** | PayPal, Stripe, Mercado Pago, Razorpay, Mollie, Paystack, Flutterwave, Thawani, Instamojo, Toyyibpay, PayU, Custom |
| **JS Lint/Format** | ESLint 9 + Prettier 3 (via eslint-plugin-prettier) |
| **PHP Lint** | Laravel Pint |

## Architecture

```
Browser ──HTTPS──> Laravel (PHP-FPM, port 80/443)
                       │
                       │ HTTP (WA_SERVER_URL)
                       ▼
              Node.js Express (port 8000)
                       │
                  baileys lib
                       │
                  WhatsApp WebSocket
```

- Laravel serves the web dashboard, user management, billing, template CRUD, scheduling, and webhooks.
- Node.js Express acts as a WhatsApp gateway — creates/manages sessions, sends/receives messages, manages groups.
- Two processes: `php artisan serve` (Laravel) + `node .` (Express). They communicate via HTTP.

## Key env vars

| Var | Default | Purpose |
|---|---|---|
| `WA_SERVER_HOST` | `127.0.0.1` | Express bind host |
| `WA_SERVER_PORT` | `8000` | Express bind port |
| `WA_SERVER_URL` | `http://127.0.0.1:8000` | Laravel → Node URL |
| `WA_SERVER_MAX_RETRIES` | `5` | Max reconnect attempts per session |
| `WA_SERVER_RECONNECT_INTERVAL` | `5000` | ms between reconnects |
| `AUTHENTICATION_GLOBAL_AUTH_TOKEN` | — | Node API token (disabled in middleware) |
| `APP_WEBHOOK_URL` | — | Webhook endpoint for events |
| `APP_WEBHOOK_ALLOWED_EVENTS` | `ALL` | `CONNECTION_UPDATE`, `MESSAGES_UPSERT`, or `ALL` |
| `APP_WEBHOOK_FILE_IN_BASE64` | `false` | Include media as base64 in webhooks |
| `APP_VERSION` | `5.0` | App version |
| `DELAY_TIME` | `2000` | Default send delay ms |

## Next step

The project is feature-complete. Focus areas for future work:

1. Re-enable Node authentication middleware (`middlewares/authenticationValidator.js` currently passes through)
2. Add JS test framework and test coverage for Express routes
3. Webhook event delivery is partially implemented — the `messages.upsert` handler in `whatsapp.js` fetches messages but does not POST to `APP_WEBHOOK_URL` (the axios/webhook call is commented/not visible)
