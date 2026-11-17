# POC-TECH-01 frontend shell

This is the Stage A React/TypeScript shell authorized by [issue #88](https://github.com/Medmas07/Inclusa/issues/88). It is a local, synthetic simulation, not an approved production architecture. The Vite/React/TypeScript choice follows the provisional three-day baseline in the issue. Vite provides a small local build and server; React renders one shared set of screens; TypeScript checks the adapter boundary. Vitest, jsdom, and React Testing Library support a screen navigation test. These are the only new runtime/build dependencies for this ticket.

## Run

With Node.js 20.19+ or 22.12+ (or a newer supported release):

```sh
cd frontend
npm ci
npm run dev
```

Open the local address printed by Vite, normally `http://127.0.0.1:5173`. No backend, account, secret, or API key is needed. Run `npm test` and `npm run build` from `frontend/` for verification. The original [vanilla prototype](../demo/README.md) remains available separately.

To inspect the unavailable HTTP seam, open `http://127.0.0.1:5173/?mode=http`. This diagnostic URL shows an error rather than a fake successful load. It does not contact a backend or enable Stage B. Remove the query string to return to the offline mock.

## Scope and handoff

- Home, Services, and Dossier reuse the Stage 0 store and the single `demo/service-config.js` registry. The three service IDs, names, sources, example items, and synthetic Fatma profile are not copied into a second data file.
- Assistant, Documents, Booking, and Worker are navigable placeholders with explicit descriptions of the work still pending in their authorized issues. The mock assistant fixture work from PR #87 remains in `demo/`; this screen does not yet claim to run it.
- `ApiAdapter` defines `load`, `selectService`, and `reset`. Its view types project the existing JSDoc contracts in `demo/contracts.js`; the fixture data comes through `demo/store.js` and `demo/fixtures.js`. `MockApiAdapter` works offline through that store. `HttpApiAdapter` implements the same TypeScript interface but explicitly rejects until issues #89 and #90 define and test its backend contract. Mock is the default; `?mode=http` is a diagnostic only.
- The screen banner and navigation always show that the experience is a simulation. The Arabic service label uses RTL direction; focus and a skip link support keyboard use. Language copy still needs native-speaker review.
- Later feature issues own forms, assistant routing, uploads, computed checklist, appointment persistence, and worker receipt. No official submission, eligibility decision, or ministry integration is implemented here.

Only fictional local session data is stored by the reused demo store. Use its reset control before sharing a browser. The source-linked programme names do not validate the sample checklist or administrative applicability.
