# dev-boxes-caddy

Local dev stack using Caddy as a base proxy for DB and Portainer.

## What you get
- Caddy reverse proxy
  - HTTP proxy to Portainer at: http://dckr.dev.local
  - TCP proxy to Postgres at: pg.dev.local:5432
- Postgres 16 database
- Portainer CE for Docker management

## Requirements
- Docker Desktop or Docker Engine + Docker Compose v2
- Add host aliases to resolve local domains

## Setup
1. Clone this repo and cd into it.
2. Copy environment defaults (optional):
   - cp .env.example .env
3. Add entries to your /etc/hosts (or system hosts file):
   - 127.0.0.1 dckr.dev.local
   - 127.0.0.1 pg.dev.local
4. Start the stack:
   - docker compose up -d --build

## Usage
- Portainer UI:
  - Open http://dckr.dev.local
  - On first run, set an admin password and select the local Docker environment.
- Postgres DB:
  - Connect your client to host: pg.dev.local, port: 5432
  - Default credentials (can be overridden via .env):
    - user: postgres
    - password: postgres
    - database: app
  - Example psql:
    - psql postgresql://postgres:postgres@pg.dev.local:5432/app

Notes:
- Direct host mapping to DB is also exposed on 15432 (localhost) for convenience; you can comment out that port in docker-compose.yml if you want to force access through Caddy's TCP proxy.
- Caddy is built with the layer4 (TCP) module to forward raw Postgres connections.

## File overview
- docker-compose.yml — defines caddy, db, and portainer services
- Dockerfile.caddy — builds Caddy with layer4 module
- Caddyfile — HTTP routes and TCP (l4) proxy rules
- .env.example — environment defaults for Postgres

## Troubleshooting
- If port 80/443/5432 are in use, stop conflicting services or change the host ports in docker-compose.yml.
- If dckr.dev.local does not resolve, ensure your hosts file includes the aliases and you flushed DNS cache if necessary.
- If DB clients fail to connect via Caddy, try the direct mapping on localhost:15432 to validate DB readiness.

## Cleaning up
- docker compose down -v
