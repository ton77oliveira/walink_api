# Backlog

## Priority: High

- [ ] Enable Node.js authentication middleware (validate `AUTHENTICATION_GLOBAL_AUTH_TOKEN`)
- [ ] Add JS test framework (Vitest or Jest) + test coverage for Express controllers
- [ ] Verify/fix webhook POST delivery to `APP_WEBHOOK_URL`

## Priority: Medium

- [ ] Document all Node.js API endpoints (OpenAPI/Swagger)
- [ ] Add rate limiting to Node.js Express routes
- [ ] Remove `body-parser` dependency (Express 5 has built-in parsing)

## Priority: Low

- [ ] Add CI workflow (GitHub Actions) for JS lint + PHP tests
- [ ] Evaluate shared session store (Redis) for horizontal scaling
