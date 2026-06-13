# Seekho-Seekhao Backend

This folder contains the backend API and database structure for the Seekho-Seekhao app.

The current scaffold is designed to match the frontend API contract in `frontend/src/lib/api.ts` so the frontend can move from mock mode to backend mode with minimal changes.

## Tech Stack

- Node.js + Express + TypeScript
- Zod for request validation
- PostgreSQL (via Docker Compose)
- Prisma schema included for migration-based database development

## Folder Structure

```text
backend/
  src/
    app.ts
    server.ts
    data/
      store.ts
    routes/
      ai.ts
      auth.ts
      board.ts
      bookings.ts
      campus.ts
      classes.ts
      config.ts
      users.ts
      wallet.ts
    types/
      domain.ts
  database/
    README.md
    schema.sql
  prisma/
    schema.prisma
  .env.example
  docker-compose.yml
  package.json
  tsconfig.json
```

## Local Setup (Teammates)

1. Install prerequisites:
- Node.js 20+
- Docker Desktop

2. Open terminal in `backend/`.

3. Install dependencies:

```bash
npm install
```

4. Create environment file:

```bash
cp .env.example .env
```

On Windows PowerShell, use:

```powershell
Copy-Item .env.example .env
```

5. Start PostgreSQL:

```bash
docker compose up -d
```

6. Start backend in dev mode:

```bash
npm run dev
```

Backend runs at `http://localhost:8000` in local dev.

Optional config in `.env`:
- `PLATFORM_COMMISSION_RATE=0.10`

7. Health check:

`GET http://localhost:8000/api/health`

## Connect Frontend to Backend

In your frontend env (usually `frontend/.env.local`):

```env
NEXT_PUBLIC_USE_MOCK=false
NEXT_PUBLIC_API_URL=http://localhost:8000/api
```

For production on Vercel, point `NEXT_PUBLIC_API_URL` to your backend deployment URL, for example:

```env
NEXT_PUBLIC_API_URL=https://your-backend.vercel.app/api
```

Then run frontend and backend together.

## API Coverage (Parity with Frontend)

### Auth
- `POST /api/auth/login`
- `POST /api/auth/logout`

### Classes
- `GET /api/classes`
- `GET /api/classes/:id`
- `POST /api/classes`
- `PUT /api/classes/:id`
- `DELETE /api/classes/:id`
- `GET /api/users/:userId/classes`

### Bookings
- `POST /api/bookings`
- `POST /api/bookings/:bookingId/cancel`
- `POST /api/bookings/:bookingId/rate`
- `GET /api/users/:userId/bookings`

### Board
- `GET /api/board`
- `POST /api/board`
- `POST /api/board/:postId/respond`

### Users
- `GET /api/users/:userId`
- `PUT /api/users/:userId`

### Wallet
- `GET /api/wallet/:userId`
- `GET /api/wallet/:userId/transactions`
- `POST /api/wallet/:userId/withdraw`

### Platform Config
- `GET /api/config/platform`
- `PUT /api/config/platform/commission`

### Campus Integration
- `GET /api/campus/room-bookings`
- `POST /api/campus/room-bookings`
- `POST /api/campus/room-bookings/:roomBookingId/cancel`
- `POST /api/campus/attendance/verify`
- `GET /api/campus/attendance/:bookingId`

### AI Assist
- `POST /api/ai/recommendations`
- `POST /api/ai/moderate`

## Current State

- API routes are implemented and runnable.
- Data persistence is currently in-memory (`src/data/store.ts`) for quick integration.
- PostgreSQL + Prisma structure is included so you can migrate to persistent storage next.
- Architecture points extracted from PDF images are documented in `ARCHITECTURE_IMAGE_EXTRACT.md`.
- Architecture-aligned modules now include commission config, room booking, QR attendance verification, and AI assist hooks.

## Connect To Neon

If you want the backend to use Neon in production, the code already supports it through `DATABASE_URL`.

1. Create the Neon database and copy the connection string from the Neon dashboard.
2. In Vercel, set the backend environment variables:
- `DATABASE_URL=<your Neon connection string>`
- `CORS_ORIGIN=https://your-frontend.vercel.app`

3. If you are using Prisma commands against Neon from your local machine, put the same Neon connection string into your local `.env` file and run:

```bash
npm run db:push
```

4. Redeploy the backend after the environment variables are updated.

For local development you can keep using Docker Postgres and the default localhost `DATABASE_URL` from `.env.example`.

### Sample Data Commands

- `npm run db:sample` seeds the current demo data into the connected database.
- `npm run db:sample:clear` removes the seeded demo rows from the connected database.

## Suggested Team Workflow

1. Keep frontend and backend contracts aligned using `src/types/domain.ts`.
2. Migrate one module at a time from in-memory store to Prisma queries.
3. Add auth middleware (JWT/session) before production deployment.
4. Add tests for route handlers as soon as persistence starts.

## Useful Commands

```bash
npm run dev
npm run typecheck
npm run build
npm run start
```
