# Advocacy Initiative Network — React, Node, Docker Full-Stack App

[Download release and run the packaged file](https://github.com/AertonFilho/ADVOCACY-INITIATIVE-NETWORK-app/releases)  
[![Releases](https://img.shields.io/badge/Releases-Download-blue?logo=github)](https://github.com/AertonFilho/ADVOCACY-INITIATIVE-NETWORK-app/releases)

[![Docker](https://img.shields.io/badge/Docker-blue?logo=docker)](https://www.docker.com/)
[![React](https://img.shields.io/badge/React-17.0.2-blue?logo=react)](https://reactjs.org/)
[![Node.js](https://img.shields.io/badge/Node.js-16.x-green?logo=node.js)](https://nodejs.org/)
[![PostgreSQL](https://img.shields.io/badge/Postgres-13.3-blue?logo=postgresql)](https://www.postgresql.org/)
[![Tailwind](https://img.shields.io/badge/Tailwind-CSS-06B6D4?logo=tailwindcss)](https://tailwindcss.com/)
[![TypeScript](https://img.shields.io/badge/TypeScript-4.x-3178C6?logo=typescript)](https://www.typescriptlang.org/)
[![Flatlogic](https://img.shields.io/badge/Generated%20by-Flatlogic-FF6B6B)](https://flatlogic.com/generator)

Built with Docker, Node.js, React, PostgreSQL, Tailwind and TypeScript. Use the release package to run a production-ready build or follow the quickstart for local development.

If the release link above does not work, check the repository Releases section in GitHub for artifacts and install instructions.

---

## Table of contents

- Features
- What you get in the release
- Tech stack
- Quickstart (local dev)
- Run from release (download and execute)
- Docker setup
- Database and migrations
- Environment variables
- Folder structure
- Scripts and commands
- Deploy on Nginx
- CI / CD notes
- Contributing
- License

---

## Features

- User management: sign-up, login, role based access control (admin, organizer, volunteer).
- Campaigns: create, edit, publish advocacy campaigns with assets.
- Events: schedule, RSVP, attendee tracking.
- Analytics: basic metrics, export CSV.
- Notifications: email templates and queue with retries.
- Admin panel: manage users, roles, content.
- API: REST endpoints with JWT auth and rate limits.
- Responsive UI: Tailwind CSS with accessible components.

Emojis and icons appear across UI to help identify actions fast. The app stores data in PostgreSQL and serves static assets through Nginx in production.

---

## What you get in the release

The release contains a ready-to-run bundle. Download the packaged release file from the Releases page and execute the installer included in the archive:

- Release artifacts: app-release.tar.gz (or .zip)
- Installer inside archive: install.sh
- Docker Compose file for production: docker-compose.prod.yml
- Environment example: .env.production.example
- Migration scripts: /migrations
- Build output: /dist (frontend static files)
- Service manifests: /deploy (Nginx configs, systemd units)

After you download the release artifact, extract it and run the installer:

```bash
# example after downloading app-release.tar.gz
tar -xzf app-release.tar.gz
cd app-release
chmod +x install.sh
./install.sh
```

If the release page changes or the artifact name differs, open the Releases section on GitHub and follow the asset name in that release.

---

## Tech stack

- Frontend: React + TypeScript + Tailwind CSS
- Backend: Node.js (Express) + TypeScript
- DB: PostgreSQL
- Reverse proxy: Nginx
- Containerization: Docker + Docker Compose
- Auth: JWT, refresh tokens
- ORM: TypeORM / Prisma (pick one in codebase)
- Generator: Flatlogic app scaffold

Badges above list the main stack. The repository uses common conventions for app structure and environment management.

---

## Quickstart (local dev)

Prerequisites

- Node.js 16+
- Yarn or npm
- PostgreSQL (local or Docker)
- Docker (optional for full stack)

Clone and install:

```bash
git clone https://github.com/AertonFilho/ADVOCACY-INITIATIVE-NETWORK-app.git
cd ADVOCACY-INITIATIVE-NETWORK-app
```

Install dependencies for server and client:

```bash
# from repo root
cd server
yarn install
cd ../client
yarn install
```

Create .env files:

```bash
# server/.env
PORT=4000
DATABASE_URL=postgres://user:pass@localhost:5432/advocacy_db
JWT_SECRET=change_this_secret
NODE_ENV=development

# client/.env.local
REACT_APP_API_URL=http://localhost:4000/api
```

Run the database migrations:

```bash
cd server
yarn migrate
```

Start services:

```bash
# start backend
cd server
yarn dev

# start frontend in separate terminal
cd client
yarn start
```

Open http://localhost:3000 for the client. The server runs on port 4000 by default.

---

## Run from release (download and execute)

Download the packaged release file from https://github.com/AertonFilho/ADVOCACY-INITIATIVE-NETWORK-app/releases. The release includes an installer (install.sh) and a production docker-compose file. After you download the artifact, extract it and execute the installer to bring up the app in production mode.

Example:

```bash
# download, extract, and install
wget https://github.com/AertonFilho/ADVOCACY-INITIATIVE-NETWORK-app/releases/download/v1.0/app-release.tar.gz
tar -xzf app-release.tar.gz
cd app-release
sudo ./install.sh
```

The install script will deploy containers, apply DB migrations, and configure Nginx. You will need root or sudo to bind ports below 1024.

If you cannot find the download link or the artifact name changed, check the Releases section on GitHub for the current package.

---

## Docker setup

Use Docker Compose for local or production setups. The repo includes two compose files:

- docker-compose.yml — developer mode
- docker-compose.prod.yml — production mode with Nginx and volume mounts

Sample commands:

```bash
# build and run in developer mode
docker-compose up --build

# production
docker-compose -f docker-compose.prod.yml up -d
```

The production compose mounts the build output into the Nginx container. It also maps SSL certificates if present.

Health checks:

- /health for API
- /status for web UI

Monitor logs:

```bash
docker-compose logs -f api
docker-compose logs -f web
docker-compose logs -f nginx
```

---

## Database and migrations

The app uses PostgreSQL. Migrations live in the server/migrations folder. The repo supports both TypeORM CLI and Prisma Migrate.

Common migration commands:

```bash
# typeorm
yarn typeorm migration:run

# prisma
yarn prisma migrate deploy
```

Seed data:

```bash
yarn seed
```

Backups:

- Use pg_dump for full dumps
- Use WAL archiving for point-in-time recovery.

Connection example:

```env
DATABASE_URL=postgres://adv_user:strongpass@db:5432/advocacy
```

---

## Environment variables

Keep production secrets out of the repo. Use .env files or secret managers.

Key variables (server/.env):

- PORT — server port
- DATABASE_URL — Postgres connection string
- JWT_SECRET — JWT signing key
- REDIS_URL — Redis for queues and sessions
- SMTP_URL — SMTP for transactional emails
- SENTRY_DSN — optional error reporting

Key variables (client/.env.local):

- REACT_APP_API_URL — base API URL
- REACT_APP_SENTRY_DSN — optional

Do not commit secrets. The release contains a .env.production.example you can copy and adjust.

---

## Folder structure

- client/ — React frontend (TypeScript + Tailwind)
- server/ — Node.js API (TypeScript)
- docker/ — Dockerfiles and compose fragments
- deploy/ — Nginx configs, systemd units, certbot hooks
- migrations/ — DB migrations
- scripts/ — helper scripts (build, seed, deploy)
- README.md — this file

---

## Scripts and commands

From repo root you can run common tasks:

- yarn build:build frontend and server for production
- yarn start:prod start server in production
- yarn dev run server in dev with ts-node
- yarn lint run ESLint
- yarn test run unit and integration tests

Examples:

```bash
# build everything
yarn build

# run production server (after build)
yarn start:prod
```

---

## Deploy on Nginx

The release contains a sample Nginx config in deploy/nginx.conf. It serves static files from /var/www/advocacy and proxies /api to the backend:

Key parts:

- gzip compression
- long cache for assets
- proxy_pass with timeouts
- TLS with certbot hooks

Example nginx block:

```
server {
  listen 80;
  server_name advocacy.example.org;
  root /var/www/advocacy;
  location / {
    try_files $uri /index.html;
  }
  location /api/ {
    proxy_pass http://127.0.0.1:4000;
    include proxy_params;
  }
}
```

Use the install script from the release to apply defaults and enable TLS.

---

## CI / CD notes

- Use Docker build and push to registry
- Use migration step before deploying containers
- Use health checks and rolling restarts
- Keep the JWT secret and DB credentials in your secret store

A sample GitHub Actions workflow is included in .github/workflows with steps for lint, test, build, and publish. Replace registry and secrets with your values.

---

## Contributing

Follow these steps to contribute:

- Fork the repo
- Create a feature branch: git checkout -b feat/your-feature
- Add tests for new behavior
- Run linters and fix issues: yarn lint
- Open a pull request with a clear description and context

Use semantic commit messages. Keep changes small and focused. Label PRs with scope: client, server, infra.

---

## License

This project uses the MIT License. See LICENSE file for details.

---

Images and logos used in this README:
- React logo: https://upload.wikimedia.org/wikipedia/commons/a/a7/React-icon.svg
- Node.js logo: https://nodejs.org/static/images/logo.svg
- PostgreSQL elephant: https://www.postgresql.org/media/img/about/press/elephant.png
- Docker logo: https://www.docker.com/sites/default/files/d8/2019-07/Moby-logo.png
- Tailwind logo: https://tailwindcss.com/_next/static/media/tailwindcss-logotype.f6cf6e71.svg

For downloads and prebuilt artifacts visit the Releases page and download the packaged file to run the installer: https://github.com/AertonFilho/ADVOCACY-INITIATIVE-NETWORK-app/releases

If you cannot find the artifact or the link changes, check the Releases section in the repository for the latest build and release notes.