# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

Small Express REST API (users + health check) used as the starter project for a Claude Code course.

## Commands

- `npm run dev` — start the API on http://localhost:3000 with `node --watch` (port from `PORT`, default 3000)
- `npm test` — run all tests with Node's built-in runner (`node --test`)
- `node --test tests/users.test.js` — run one test file; add `--test-name-pattern="404"` to run a single test
- `npm run lint` — ESLint; CI runs lint then tests on Node 22, so both must pass

## Conventions

- Use CommonJS (`require` / `module.exports`), not ES modules — ESLint is configured with `sourceType: "script"`.
- Routes must read and write data only through the functions in `db/store.js`, never by touching the data directly.
- Error responses are JSON shaped `{ error: "<message>" }` with the matching status code (400 for invalid input, 404 for missing records).
- Tests use `node:test` + `node:assert` + `supertest` against the exported `app` — not Jest or Mocha.

## Architecture

- `server.js` builds the Express app, mounts one router per resource (`/users`, `/health`), and only calls `listen()` when run directly (`require.main === module`). It exports `app` so tests can drive it via supertest without opening a port.
- `routes/` holds one `express.Router()` file per resource; new resources get a new file there and are mounted in `server.js`.
- `db/store.js` is an in-memory store standing in for a real database. Data resets on restart, and its state is module-level, so it is shared across tests in the same process — don't rely on exact counts or IDs.
