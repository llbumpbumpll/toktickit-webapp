# TokTickIT: Sprint 1 Starting Point for Lab 5


TokTickIT is an IT help desk ticketing app used as the worked example in CPE 334. Complete full-stack implementation of **TokTickIT (Lab 1 – Lab 4)** . This repository shows the state a team is in **before Lab 5**: the accepted Sprint 1 release runs on a laptop, but it has no Docker setup, no CI/CD pipeline, and no cloud deployment yet. The Lab 5 videos add those pieces step by step, using the Lab 5 Starter Kit.

- **Frontend:** React 18, TypeScript, Vite, Bootstrap 5 (`client/`)
- **Backend:** Express, TypeScript, Prisma (`server/`)
- **Database:** PostgreSQL
- **Features:** sign-in with roles (Administrator, IT Staff, Requester), ticket workflow with 8 statuses, comments and internal notes, file attachments, Actions Taken, and dashboards

---



## Repository Status

| Already here (Sprint 1) | Added during Lab 5 |
|---|---|
| App source in `client/` and `server/` | `Dockerfile`, `.dockerignore`, `docker-compose.yml` |
| Prisma schema, migrations, and an idempotent seed | `.github/workflows/ci-docker.yml` (CI on Pull Requests) |
| Backend and frontend test suites | `deploy/` scripts and their tests |
| `.env.example` files with placeholders | `.github/workflows/deploy.yml` (deploy on merge, smoke test, rollback) |

## Branches

| Branch | Purpose |
|---|---|
| `main` | Accepted releases. In Lab 5, only `main` deploys. |
| `staging` | Integration branch for the current sprint. Every feature branch starts here. |

Every change follows the course workflow: Issue → branch from `staging` → Pull Request into `staging` → teammate review → merge → release Pull Request from `staging` into `main`. Do not commit directly to `staging` or `main`. Authors do not approve their own Pull Requests.

---

## Run Locally

### Prerequisites

- **Node.js** `>= 20.x`
- **PostgreSQL** `>= 15.x` running on `localhost:5432`

If PostgreSQL is not installed, you can start a temporary local database in a container. This runs only the database, not the app:

```bash
docker run -d --name toktickit-db -p 5432:5432 \
  -e POSTGRES_USER=toktickit -e POSTGRES_PASSWORD=toktickit -e POSTGRES_DB=toktickit \
  postgres:16
```

These values match the `DATABASE_URL` in `server/.env.example`. They are for your laptop only; the cloud database gets a generated password.

### Backend (`server/`)

```bash
cd server
cp .env.example .env      # edit DATABASE_URL if your database differs
npm install
npx prisma migrate deploy
npm run prisma:seed       # idempotent: safe to run more than once
npm run dev               # API on http://localhost:3000
```

### Frontend (`client/`)

```bash
cd client
cp .env.example .env
npm install
npm run dev               # UI on http://localhost:5173
```

### Check

- `http://localhost:3000/api/health` returns ok
- `http://localhost:3000/api/ready` reports the database connected
- `http://localhost:5173` opens the sign-in page

### Sign In

Locally, the seed creates the administrator `john.smith@example.com`, sample IT Staff and Requester accounts, and 8 sample tickets. The local development password is set in `server/prisma/seed.ts` and works only when `NODE_ENV` is not `production`.

In production the seed behaves differently: it reads `ADMIN_PASSWORD` from the environment, creates only reference data and the administrator (sample users only if `SEED_DEMO_DATA=true`), and every account must change its password at first sign-in.

---

## Tests

```bash
cd server && npm test     # backend API and integration tests (needs the database)
cd client && npm test     # frontend component and workflow tests
```

---

## Environment Variables

Copy each `.env.example` to `.env` for local work. Never commit `.env`; it is already in `.gitignore`.

| Variable | Required | Description |
|---|---|---|
| `DATABASE_URL` | Yes | PostgreSQL connection URL |
| `PORT` | No (default `3000`) | Port the API listens on |
| `NODE_ENV` | No (default `development`) | Set to `production` in the cloud |
| `TZ` | No (default `Asia/Bangkok`) | Time zone for ticket numbers |
| `ADMIN_PASSWORD` | In production | First administrator password, used only when the database has no users |
| `SEED_DEMO_DATA` | No (default `false` in production) | `true` seeds sample users and tickets |
| `DEMO_USER_PASSWORD` | If `SEED_DEMO_DATA=true` in production | Password for the sample users |
| `CORS_ORIGIN` | No | Allowed origins in production, comma-separated |

---

## Rules

- Never put a password, connection string, or key in the repository, an AI chat, a screenshot, or a group chat.
- Read every cloud command an AI agent proposes before it runs. Do not let an agent change billing, IAM, or databases on its own.

---

Built by the CPE 334 TA team (Labs 1–4). Deployment templates come from the Lab 5 Starter Kit.
