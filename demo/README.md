# Inclusa local demo foundation

This disposable, offline prototype implements [POC-02](https://github.com/Medmas07/Inclusa/issues/8). Plain browser JavaScript and Python's standard library keep startup fast and avoid a package or build dependency. All identities, services, and dossier data are fictional. Its `demo_unverified` services have **no approved requirements**. It makes no eligibility, completeness, or administrative decision.

## Start

From the repository root, run:

```sh
python -m http.server 4173 --bind 127.0.0.1 --directory demo
```

Open `http://127.0.0.1:4173/` in a browser. Stop the server with Ctrl+C. Python 3 is the only startup prerequisite. No package installation, secret, external API, or Internet connection is required. The browser stores fictional session state in localStorage when available and continues in memory if storage is unavailable. **Reset demo** restores the original fixtures and returns to Home.

## Shared contracts

- `contracts.js` defines `Service`, `Beneficiary`, `Dossier`, `Document`, `DialogState`, the readiness values, and the provider signatures with JSDoc. Import this file from each PoC track instead of defining separate shapes.
- `fixtures.js` creates a fresh fictional beneficiary, three unverified service placeholders, one draft dossier, and an unstarted dialog. Later authorized tickets can replace placeholder content.
- `store.js` exports `createDemoStore()`. Consumers call `getState()`, `subscribe(listener)`, `update(updater)`, `navigate(screen)`, and `reset()`. `update` receives a detached copy and must return a valid full state. Subscribers receive detached copies. The versioned localStorage key is `inclusa-poc-demo-v1`.
- `providers.js` exports `createProviders({assistant, ocr})`. The assistant adapter implements `respond(dialogState, input)`; the OCR adapter implements `extract({fileName})`. Both default to deterministic mocks. The assistant does not route to a service yet; OCR never claims it read a document. Later real adapters are optional and require their own authorized work and privacy review.

The current screens expose navigation and the shared draft. Guided forms, service requirements, document uploads, worker actions, and real providers belong to later Issues.

## Validate

```sh
node --test demo/*.test.js
python scripts/validate_repo.py
```

Node.js 20 or newer is used only for the automated JavaScript tests. The browser demo has no Node runtime dependency.
