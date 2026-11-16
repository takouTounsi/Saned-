# Current status of Inclusa

**Updated November 14, 2026.** This is an observed repository snapshot, not a product approval or a replacement for Issues and accepted ADRs.

## Project and decisions

Inclusa is a disposable, offline social-services demonstration for the Social Tech Challenge 2026. The three selected Ministry programme families are real; the beneficiary, documents, answers, checklist samples, appointments, and worker actions are synthetic. Product and institutional process details still need MAS/ULPS validation. Validated production requirements: **none**. Accepted architecture decisions: **none**. See the [POC-01 source profiles](docs/poc/POC-01-service-profiles.md) and [demo flow](docs/poc/POC-01-demo-flow.md).

## Implemented on `main`

- **POC-01 (#7):** source-backed service framing, one Fatma hero story, and a scripted golden path with two lighter routes are documented.
- **POC-02 (#8):** a browser demo runs locally with Home, Services, and Dossier screens, a shared resettable state in localStorage with a memory fallback, and replaceable mock assistant/OCR provider interfaces.
- **POC-04 (#10):** exactly three service profiles are configured and selectable. The catalogue and dossier sample list read the same registry. Fatma's synthetic profile and planned clear/blurred/mismatch document fixtures are present. A displayed sample list is not an official or computed completeness check.

Run the current demo and tests using [demo/README.md](demo/README.md). The programme names are source-linked, while the Arabic/Derja copy and administrative applicability need review. No real beneficiary data, official identity document, credential, or integration is included.

## In progress and missing from the runnable journey

- [POC-06.01 (#49)](https://github.com/Medmas07/Inclusa/issues/49): nine exact French, Derja Arabic-script, and Arabizi routing fixtures were merged in [PR #87](https://github.com/Medmas07/Inclusa/pull/87) and are kept as regression cases.
- POC-06.02/06.03/06.04 ([#50](https://github.com/Medmas07/Inclusa/issues/50), [#51](https://github.com/Medmas07/Inclusa/issues/51), [#52](https://github.com/Medmas07/Inclusa/issues/52)) were merged in [PR #96](https://github.com/Medmas07/Inclusa/pull/96) and [PR #97](https://github.com/Medmas07/Inclusa/pull/97). The mock assistant runs a scripted, offline, rule-based dialogue manager (keywords and fact rules over the three configured services, one question per turn, manual-choice fallback, no eligibility statements) wired into an Assistant screen with quick replies and a "Préparer ce dossier" action. It is not a language model. Its Derja and Arabizi reply copy and the French document `whereToGet` text are provisional and need native-speaker and MAS/ULPS review.
- [POC-16 (#78)](https://github.com/Medmas07/Inclusa/issues/78): `ocr-scanner/`, a local CIN scanner spike (phone capture, quality checks, EasyOCR), was added in [PR #95](https://github.com/Medmas07/Inclusa/pull/95). It is not wired to `demo/`, was only tried on personal captures that are not in the repository, and its acceptance criteria remain pending until synthetic sample images and a results and limitations table exist.
- POC-17.01/17.02 ([#80](https://github.com/Medmas07/Inclusa/issues/80), [#81](https://github.com/Medmas07/Inclusa/issues/81)) are in an open Pull Request: an opt-in, experimental local LLM (Ollama) extracts intent, script and facts, validated by an allowlist, while the scripted dialogue manager still writes every reply. Code and fake-fetch tests exist; the benchmark has **not been run** (no Ollama available to the author) and Derja/Arabizi extraction quality is unknown. #81 stays open until a teammate runs it.
- Mock OCR in `demo/` returns no extraction. There is no guided form, document upload, computed checklist, simulated booking, worker view, or complete end-to-end journey. These belong to later authorized POC Issues.
- Among the 18 top-level POC Issues (#7–#24), #7, #8, and #10 are closed and Done. #9 is in Backlog; the remaining top-level Issues are currently Blocked. Closing reviewed Issues has been observed to move their Project items to Done.

## Local repository and verification

- Remote: `https://github.com/Medmas07/Inclusa`. The local `main` and `origin/main` include merged POC-01, POC-02, and POC-04 work and were synchronized for this snapshot. Fetch before creating later branches to see subsequent remote changes.
- This documentation update is separate from [PR #87](https://github.com/Medmas07/Inclusa/pull/87) and does not change its routing fixtures.
- The latest local JavaScript run passed 11 tests; repository validation passed. These checks do not establish browser usability or official process accuracy.

## Next checks

1. Review and validate PR #87, including native-speaker review of the provisional prompts, before merge.
2. Continue the authorized UI, dialogue, dossier, checklist, upload, appointment, and worker tickets in dependency order; keep the offline golden path deterministic.
3. Confirm source currency, document applicability, territorial ULPS, and any appointment process with MAS/ULPS mentors before presenting citizen guidance as verified.

Never add secrets or real beneficiary documents to the repository.
