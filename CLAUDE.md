# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

FlowSync is a two-package repo (no root `package.json`, no workspaces): `backend/` (AdonisJS 7, TypeScript, SQLite) and `frontend/` (React 19 + Vite 8, TypeScript). It is the base project for a course exercise on agent harnesses (see `README.md`). **Node.js 24+ is required** (Node 20 fails with `Unknown file extension ".ts"`).

Run each package from its own directory; each has its own `npm install`.

## Commands

Backend (`backend/`, serves on `http://localhost:3333`):

```bash
cp .env.example .env && node ace generate:key   # first-time setup
node ace migration:run                          # apply migrations
npm run dev          # node ace serve --hmr
npm test             # node ace test (Japa); add -- --files "tests/<path>.spec.ts" for one file
npm run lint         # eslint .
npm run typecheck    # tsc --noEmit
npm run format       # prettier --write .
```

Frontend (`frontend/`, serves on `http://localhost:5173`; run in a separate terminal from the backend):

```bash
npm run dev
npm run build        # tsc -b && vite build
npm run lint         # oxlint
```

The frontend has no formatter, no test runner, and no shadcn/ui setup yet. `backend/tests/` only contains `bootstrap.ts`; there are no tests yet.

## Backend architecture

AdonisJS layers, wired through `start/routes.ts`:

- **Routes** (`start/routes.ts`): everything lives under `/api/v1`. `auth/signup` and `auth/login` are public; `account/profile` (GET) and `account/logout` (POST) sit behind `middleware.auth()`.
- **Controllers** (`app/controllers/`): referenced in routes via the generated `controllers` barrel (`#generated/controllers`, built into `.adonisjs/` by codegen on `node ace serve`), not by direct imports. New controllers appear there automatically after codegen runs.
- **Validators** (`app/validators/user.ts`, VineJS): the source of truth for request shape. Notably, signup requires `email`, `password` (8-32 chars), **and `passwordConfirmation`** (must equal `password`); `fullName` is nullable; email must be unique. Login takes `email` + `password`. Check the validator, not just the ticket, when building clients.
- **Auth**: token guard (`@adonisjs/auth` access tokens, DB-backed). Signup and login both return `{ user, token }` where `token` is an `oat_...` string; send it as `Authorization: Bearer <token>`.
- **Transformers** (`app/transformers/`): responses go through `serialize(...)` with a transformer that whitelists fields (`id`, `fullName`, `email`, `createdAt`, `updatedAt`, `initials`). Never return models directly.
- **Models / DB**: Lucid models extend generated schema classes from `database/schema.ts`. Schema changes are done with a new migration in `database/migrations/`, never by editing tables by hand.
- **Imports** use subpath aliases from `package.json` (`#controllers/*`, `#models/*`, `#validators/*`, ...), not relative paths.
- **CORS** (`config/cors.ts`): any origin is allowed in dev, so the Vite dev server on :5173 can call the API directly.

## Frontend

Currently the stock Vite + React template (`src/main.tsx`, `src/App.tsx`). The planned work is login/signup screens against the backend API above, using shadcn/ui.

## Process rules

1. Create a new branch before touching any code. Never work on `main` or `s1/start`.
2. When the work is done, run `/commit` to create a conventional commit, then open the pull request with `gh pr create`.
3. After the pull request is open, run the `adversarial-reviewer` subagent on it.
4. In the chat, reply with only the pull request URL.
