# EasyPanel

## GitHub

- Repository: https://github.com/ton77oliveira/walink_api
- Private: no
- Production branch: main
- Staging branch: main

## EasyPanel Access

### Staging

- URL: http://falcon9.taila8e748.ts.net:3000/
- Login: contato@link2.pro

## Project

- EasyPanel project: temporarios
- Local project name: walink_api

## Services

| Environment | Service | Type | Branch | Domain | Internal Port | Notes |
|---|---|---|---|---|---|---|
| staging | walink_api-web | app (dockerfile) | main | app.walink.com.br | 80 | Dockerfile.web: multi-stage Composer 2 (PHP 8.2) + Node 20 + serversideup/php:8.2-fpm-nginx. Build: `composer install --no-dev`, `npm ci && npm run build`. Runtime: Nginx + PHP-FPM 8.2. |
| staging | walink_api-node | app (dockerfile) | main | api.walink.com.br | 8000 | Dockerfile.node: Node 20-alpine. Start: `node .`. WhatsApp API Gateway (Express). |
| staging | mysql_walink_api | mysql | — | — | 3306 | MySQL 8.0 database for Laravel. DB: `walink_api`, User: `walink_api`. |

## Deploy History

| Date | Commit | Status | Notes |
|---|---|---|---|
