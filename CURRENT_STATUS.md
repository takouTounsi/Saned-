# Current status of Inclusa

**Updated November 15, 2026.** This is an observed repository snapshot, not a product approval or a replacement for Issues and accepted ADRs.

## Project and decisions

Inclusa is a disposable, offline social-services demonstration for the Social Tech Challenge 2026. The three selected Ministry programme families are real; the beneficiary, documents, answers, checklist samples, appointments, and worker actions are synthetic. Product and institutional process details still need MAS/ULPS validation. Validated production requirements: **none**. Accepted architecture decisions: **none**. See the [POC-01 source profiles](docs/poc/POC-01-service-profiles.md) and [demo flow](docs/poc/POC-01-demo-flow.md).

## Implemented on `main`

- **POC-01 (#7):** source-backed service framing, one Fatma hero story, and a scripted golden path with two lighter routes are documented.
- **POC-02 (#8):** a browser demo runs locally with Home, Services, and Dossier screens, a shared resettable state in localStorage with a memory fallback, and replaceable mock assistant/OCR provider interfaces.
- **POC-04 (#10):** exactly three service profiles are configured and selectable. The catalogue and dossier sample list read the same registry. Fatma's synthetic profile and planned clear/blurred/mismatch document fixtures are present. A displayed sample list is not an official or computed completeness check.

Run the current demo and tests using [demo/README.md](demo/README.md). The programme names are source-linked, while the Arabic/Derja copy and administrative applicability need review. No real beneficiary data, official identity document, credential, or integration is included.

## In progress and missing from the runnable journey

- [PR #87](https://github.com/Medmas07/Inclusa/pull/87) was merged into `main` and issue #49 is closed. The nine exact French, Derja Arabic-script, and Arabizi routing fixtures are available as regression material but are not yet wired to the assistant provider or UI. Issue #49's Project status still needs separate verification.
- [POC-TECH-01 (#88)](https://github.com/Medmas07/Inclusa/issues/88) is ready for human review on `feat/88-componentized-frontend`: the React/TypeScript shell reuses the vanilla demo store, fixtures, registry, and JSDoc contracts, navigates seven stages, and exposes a typed mock/HTTP adapter boundary. The HTTP adapter explicitly rejects until backend issues #89/#90 are implemented; `?mode=http` displays that error for diagnosis. The Assistant, Documents, Booking, and Worker screens remain honest placeholders. Merge is pending.
- The assistant still returns a placeholder reply; mock OCR returns no extraction. There is no guided form, document upload, computed checklist, simulated booking, worker view, or complete end-to-end journey. These belong to later authorized POC Issues.
- Among the 18 top-level POC Issues (#7–#24), #7, #8, and #10 are closed; the others remain open. Project status for each open issue was not rechecked in this snapshot. Closing reviewed Issues has been observed to move their Project items to Done.

## Local repository and verification

- Remote: `https://github.com/Medmas07/Inclusa`. `origin/main` includes merged POC-01, POC-02, POC-04, and PR #87 work. The local `main` branch has not yet been fast-forwarded to the fetched remote. The #88 branch starts at the fetched `origin/main`.
- On the #88 branch, `npm run build` passed, `npm test` passed 3 frontend tests, `node --test demo/*.test.js` passed 11 existing tests, and `python scripts/validate_repo.py` passed after excluding installed package files from first-party Markdown validation. `npm run dev` started Vite and served HTTP 200 locally. Headless Chrome at 390 px and 1280 px found no horizontal page overflow; a keyboard Tab/Space path opened Services; Arabic labels rendered with `dir=rtl`; diagnostic HTTP mode showed a visible failure. Physical-device usability and official process accuracy remain unverified.

## Next checks

1. Review and validate the provisional wording from PR #87 with a native speaker before presenting it as user guidance.
2. Complete and review #88, then continue the authorized UI, dialogue, dossier, checklist, upload, appointment, and worker tickets in dependency order; keep the offline golden path deterministic.
3. Confirm source currency, document applicability, territorial ULPS, and any appointment process with MAS/ULPS mentors before presenting citizen guidance as verified.

Never add secrets or real beneficiary documents to the repository.
