# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

Starter Express API for the Claude Code course: an in-memory `/users` and `/health` service used to practice setting up `CLAUDE.md` and permission rules — not a project meant to grow real features.

## Commands

- `npm run dev` — start the API on http://localhost:3000 with auto-reload (`node --watch`)
- `npm test` — run all tests (`node --test`, uses `node:test` + `supertest`)
- `npm test -- --test-name-pattern="<name>"` — run a single test by name
- `npm run lint` — check code style with ESLint

## Conventions

- Routes live one file per resource in `routes/` (e.g. `users.js`, `health.js`) and are mounted in `server.js` — new resources follow the same pattern.
- All data access goes through `db/store.js`; routes never touch the in-memory `users` array directly.
- Route handlers return errors as JSON (`{ error: "..." }`) with the appropriate status code (400 for bad input, 404 for missing resources) rather than throwing.
- `server.js` only calls `app.listen` when run directly (`require.main === module`), so `app` can be imported into tests without opening a port — preserve this when editing `server.js`.

## Architecture

- `server.js` is the entry point: builds the Express app, mounts `routes/users.js` at `/users` and `routes/health.js` at `/health`, and exports `app` for tests.
- `db/store.js` is a tiny in-memory data helper (no real database) — state resets on every restart.
- `tests/` uses `supertest` against the exported `app` instance, not a running server.
