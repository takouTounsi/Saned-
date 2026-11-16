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
- `routing-fixtures.js` defines nine exact demonstration prompts for [POC-06.01](https://github.com/Medmas07/Inclusa/issues/49): one French, one Derja in Arabic script, and one Arabizi example for each configured service. `lookupRoutingFixture()` returns a fixed service/intent/next-question key for those scripts or `clarify_service` for unknown input. `PARAPHRASE_FIXTURES` adds ten non-exact phrases (`ALL_ROUTING_FIXTURES` has 19); these are resolved by the dialogue manager. The Arabic and Derja copy needs native-speaker review.
- `dialogue.js` ([POC-06.02](https://github.com/Medmas07/Inclusa/issues/50)) is the scripted, offline, rule-based dialogue manager (not a language model). It detects the script (French, Derja Arabic script, Arabizi), normalizes text, matches keywords against the configured services only, and recognizes simple facts (mother, children count and ages, unemployed, school need, yes/no). It asks at most one question per turn, never re-asks a known fact, never asks for a name or CIN, answers an eligibility question by saying only the competent authority decides, and falls back to a numbered choice of the three services on unknown input. `dialogue-copy.js` ([POC-06.03](https://github.com/Medmas07/Inclusa/issues/51)) holds the reply copy per message key and script; Derja and Arabizi wording is provisional and needs native-speaker review. `explainDocumentItem()` in `service-config.js` renders a document in at most two lines (what it is, where to get it) from the item's `whereToGet` text, which is French, provisional and marked `needs_review`.
- `fixtures.js` creates a fresh fictional Fatma profile, the service registry, one draft dossier, and an unstarted dialog. No real identity, CIN, address, contact, or medical data is included.
- `store.js` exports `createDemoStore()`. Consumers call `getState()`, `subscribe(listener)`, `update(updater)`, `navigate(screen)`, and `reset()`. `update` receives a detached copy and must return a valid full state. Subscribers receive detached copies. The versioned localStorage key is `inclusa-poc-demo-v1`.
- `providers.js` exports `createProviders({assistant, ocr})`. The assistant adapter implements `respond(dialogState, input)`; the OCR adapter implements `extract({fileName})`. Both default to deterministic mocks. The default assistant is the scripted dialogue manager (`kind: 'scripted-simulated'`); the Assistant screen ([POC-06.04](https://github.com/Medmas07/Inclusa/issues/52)) is its chat UI. OCR never claims it read a document. Later real adapters are optional and require their own authorized work and privacy review.

`assistant-view.js` holds the pure helpers behind the Assistant screen: text direction (Arabic script renders right-to-left), quick-reply chips derived from the pending question, the service suggestion card data, and the example prompts. The conversation is stored in the shared store (`conversation`) and cleared by **Reset demo**. The screen shows a visible simulation badge; "Préparer ce dossier" selects the service and opens the Dossier screen. The shell styling takes its palette from the Ministry of Social Affairs website (navy, gold, flat cards) using system font stacks only; it reuses no logo, emblem, name or image and is marked "Démonstrateur — non officiel".

The service catalogue lets a user select A, B, or C and see its demo-only sample list in the draft dossier. Saved local sessions reload the current registry, so a configuration edit updates the catalogue and sample list. This is a display of planned examples, not a completed checklist or official decision. Guided forms, document uploads, OCR, worker actions, and real providers belong to later Issues. Arabic and Derja wording still needs native-speaker review; MAS/ULPS must validate applicable documents, current sources, and any real-world process.

## Experimental: local LLM intent extraction (POC-17, opt-in)

Scripted mode stays the **default**. An opt-in "Mode LLM local (expérimental)" toggle on the Assistant screen (or `?llm=1`, optionally `&model=<name>`) asks a **local** Ollama model to extract only `{intent, languageOrScript, facts}` from the user's message ([#80](https://github.com/Medmas07/Inclusa/issues/80), [#81](https://github.com/Medmas07/Inclusa/issues/81)). `llm-intent.js` validates it against an allowlist in plain JS (allowlisted intents, `fr|derja-ar|arabizi`, allowlisted fact keys with strict types such as child ages as integers 0-18); anything else is dropped or the whole extraction is rejected. The scripted dialogue manager then merges the validated facts and still decides the service, the next question, the reply text, documents and every guardrail. The model never writes user-facing text and never decides eligibility or required documents; an eligibility or document list in its output is ignored. Each assistant message shows a small "LLM" or "scripté" tag. On any failure (Ollama not running, network error, timeout after about 8 s, invalid JSON, schema violation) the turn is answered by the scripted path with the note "LLM indisponible — mode scripté utilisé".

Honest status: **experimental, not validated on Derja or Arabizi** (no measurement has been run yet, see [the benchmark template](../docs/poc/POC-17-llm-benchmark.md)). The model is local (default `qwen2.5:3b`), there is no cloud API, key or secret, and the base URL must be loopback (`127.0.0.1`, `localhost`, `[::1]`) or nothing is sent. Settings live in `DEFAULT_LLM_SETTINGS` (`baseUrl`, `model`, `timeoutMs`) in `llm-intent.js`.

To try it (requires [Ollama](https://ollama.com), which is not installed by this repository):

```sh
ollama pull qwen2.5:3b
ollama serve
python -m http.server 4173 --bind 127.0.0.1 --directory demo
# open http://127.0.0.1:4173/?llm=1  (or tick the toggle on the Assistant screen)
```

Ollama accepts browser requests from `http://127.0.0.1:*` and `http://localhost:*` origins by default. If the demo is served from another origin, start Ollama with `OLLAMA_ORIGINS` set to that origin (for example `OLLAMA_ORIGINS=http://192.168.1.20:4173 ollama serve`). Run the six-phrase benchmark with `node demo/bench/llm-intent-bench.mjs` (add `--write` to update the benchmark document); without a reachable Ollama it prints `blocked: Ollama not reachable at ...` and exits with code 1. The tests use a fake `fetch` and never touch the network.

## Validate

```sh
node --test demo/*.test.js
python scripts/validate_repo.py
```

Node.js 20 or newer is used only for the automated JavaScript tests. The browser demo has no Node runtime dependency.
