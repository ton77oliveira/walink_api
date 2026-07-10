# Decisions (ADRs)

## ADR-001: Hybrid Laravel + Node.js architecture

**Context:** Need a web dashboard (user management, billing, templates) + real-time WhatsApp WebSocket gateway.

**Decision:** Run Laravel (PHP) for the web layer and a separate Node.js/Express process for WhatsApp connectivity via `baileys`.

**Consequences:**
- Two processes to manage and monitor
- HTTP communication between them via `WA_SERVER_URL`
- Laravel handles auth, billing, CRUD; Node handles raw WhatsApp WebSocket ops

---

## ADR-002: baileys library for WhatsApp Web

**Context:** Need to interact with WhatsApp without a phone number (multi-device protocol).

**Decision:** Use `baileys` (v7.0.0-rc.6), a community-maintained WhatsApp Web JS library.

**Consequences:**
- Session state stored as `sessions/md_*` multi-file auth state
- Requires periodic updates as WhatsApp protocol changes
- Pairing code and QR code auth both supported

---

## ADR-003: In-memory store with JSON persistence

**Context:** Need fast access to chat data without a secondary database for the Node process.

**Decision:** Custom in-memory store (`store/memory-store.js`) with periodic JSON file saves.

**Consequences:**
- Chat data survives restarts via `*_store.json` files in `sessions/`
- Not scalable to multiple Node instances without shared storage
- Auto-saves every 10 seconds (`autoSaveInterval: 10000`)
- Cleanup on exit via `node-cleanup` hook

---

## ADR-004: Auth middleware disabled by default

**Context:** Need to allow API access during development/testing.

**Decision:** `middlewares/authenticationValidator.js` calls `next()` unconditionally.

**Consequences:**
- All Node API routes are publicly accessible
- `AUTHENTICATION_GLOBAL_AUTH_TOKEN` is configured but never checked
- Must be enabled before production deployment
