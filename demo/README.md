# Inclusa local demo foundation

This disposable, offline prototype implements [POC-02](https://github.com/Medmas07/Inclusa/issues/8) and the three-service configuration for [POC-04](https://github.com/Medmas07/Inclusa/issues/10). Plain browser JavaScript and Python's standard library keep startup fast and avoid a package or build dependency. Ministry programme labels are real; identities, documents, answers, dossier data, and checklist samples are synthetic. Its `demo_unverified` services have **no approved administrative requirements**. It makes no eligibility, official completeness, or administrative decision.

## Start

From the repository root, run:

```sh
python -m http.server 4173 --bind 127.0.0.1 --directory demo
```

Open `http://127.0.0.1:4173/` in a browser. Stop the server with Ctrl+C. Python 3 is the only startup prerequisite. No package installation, secret, external API, or Internet connection is required. The browser stores fictional session state in localStorage when available and continues in memory if storage is unavailable. **Reset demo** restores the original fixtures and returns to Home.

## Shared contracts

- `contracts.js` defines `Service`, `Beneficiary`, `Dossier`, `Document`, `DialogState`, the readiness values, and the provider signatures with JSDoc. Import this file from each PoC track instead of defining separate shapes.
- `service-config.js` is the single POC-04 registry. It contains exactly `SERVICE_A/B/C` with POC-01 canonical IDs, Ministry provenance, French/Arabic labels, provisional Derja/Arabizi copy, sample items, and `officialMandatory: null`. `requirements` is a list of **demo-only sample keys**; it is not an official checklist. B's two ages remain variants of one service, and its 6–18 route has no invented document bundle. Edit this registry to update catalogue and demo sample-list views.
- `DEMO_ERROR_COPY` contains short French, standard Arabic, and provisional Derja text for missing, unreadable, and mismatched demo items. The current catalogue does not run these checks; later upload and checklist tickets can use the copy after native-speaker review.
- `document-fixtures.js` names planned clear, blurred, and mismatch synthetic text placeholders for A. These are not image assets or OCR results; [POC-09](https://github.com/Medmas07/Inclusa/issues/15) owns upload and mock extraction.
- `routing-fixtures.js` defines nine exact demonstration prompts for [POC-06.01](https://github.com/Medmas07/Inclusa/issues/49): one French, one Derja in Arabic script, and one Arabizi example for each configured service. `lookupRoutingFixture()` returns a fixed service/intent/next-question key for those scripts or `clarify_service` for unknown input. It is not wired into the assistant provider yet; POC-06.02 owns the dialogue manager. The Arabic and Derja copy needs native-speaker review.
- `fixtures.js` creates a fresh fictional Fatma profile, the service registry, one draft dossier, and an unstarted dialog. No real identity, CIN, address, contact, or medical data is included.
- `store.js` exports `createDemoStore()`. Consumers call `getState()`, `subscribe(listener)`, `update(updater)`, `navigate(screen)`, and `reset()`. `update` receives a detached copy and must return a valid full state. Subscribers receive detached copies. The versioned localStorage key is `inclusa-poc-demo-v1`.
- `providers.js` exports `createProviders({assistant, ocr})`. The assistant adapter implements `respond(dialogState, input)`; the OCR adapter implements `extract({fileName})`. Both default to deterministic mocks. The assistant does not route to a service yet; OCR never claims it read a document. Later real adapters are optional and require their own authorized work and privacy review.

The service catalogue lets a user select A, B, or C and see its demo-only sample list in the draft dossier. Saved local sessions reload the current registry, so a configuration edit updates the catalogue and sample list. This is a display of planned examples, not a completed checklist or official decision. Guided forms, document uploads, OCR, worker actions, and real providers belong to later Issues. Arabic and Derja wording still needs native-speaker review; MAS/ULPS must validate applicable documents, current sources, and any real-world process.

## Validate

```sh
node --test demo/*.test.js
python scripts/validate_repo.py
```

Node.js 20 or newer is used only for the automated JavaScript tests. The browser demo has no Node runtime dependency.
