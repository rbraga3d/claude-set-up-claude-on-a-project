# CLAUDE.md


## Commands

- `npm run dev` — start the API with `node --watch` on http://localhost:3000
- `npm test` — run all tests with the built-in Node test runner
- `npm run lint` — ESLint over the whole repo


## Conventions

- Use CommonJS (`require` / `module.exports`), not ES modules — ESLint parses files as `sourceType: "script"`.
- Write tests with `node:test`, `node:assert` and `supertest`, not Jest or Mocha. Import `app` from `../server` and pass it to `request(app)`; never call `app.listen` in a test.
- Route handlers read and write data only through the functions exported by `db/store.js`, never by touching the `users` array directly.
- Return errors as JSON `{ "error": "<message>" }` with the matching status code (400 for invalid input, 404 for a missing resource).


## Architecture

- `server.js` builds the Express app, mounts each router under its path prefix, and exports `app`. It only calls `listen` when run directly (`require.main === module`), which is what lets the tests import it without opening a port.
- `routes/` holds one `express.Router()` per resource (`users.js`, `health.js`). Adding a resource means a new file here plus an `app.use("/<name>", ...)` line in `server.js`.
- `db/store.js` is the only data layer: module-level state that resets on every restart. 
