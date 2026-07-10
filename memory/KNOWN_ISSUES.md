# Known Issues

## Node.js authentication is disabled

`middlewares/authenticationValidator.js` passes all requests through without validating `AUTHENTICATION_GLOBAL_AUTH_TOKEN`. The env var is configured but never checked. **Must be fixed before production deployment.**

## No JS test framework

All 32 Laravel migrations and PHP tests exist, but there are zero JS tests. Express route handlers (`controllers/`) and WhatsApp session logic (`whatsapp.js`) have no coverage.

## Webhook delivery may not work

The `messages.upsert` event handler in `whatsapp.js` fetches media and enriches messages, but the actual POST to `APP_WEBHOOK_URL` logic appears to be incomplete or commented out. Needs verification.

## Express 5 compatibility

Uses `express@^5.1.0` (alpha/beta). Some middleware patterns may differ from Express 4. `body-parser` is still listed as a direct dependency despite Express 5 having built-in JSON/URL-encoded parsing.

## Baileys is a moving target

`baileys@7.0.0-rc.6` is a pre-release. WhatsApp protocol changes frequently — expect breakage on upstream updates.
