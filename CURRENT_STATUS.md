# Current status of Inclusa

**Updated November 16, 2026.** This is an observed repository snapshot, not a product approval or a replacement for Issues and accepted ADRs.

## Project and decisions

The active local pack starts the OCR scanner, application backend, and installed Ollama service with `python scripts/start_local.py`.

Inclusa is a disposable, offline social-services demonstration for the Social Tech Challenge 2026. The three selected Ministry programme families are real; the beneficiary, documents, answers, checklist samples, appointments, and worker actions are synthetic. Product and institutional process details still need MAS/ULPS validation. Validated production requirements: **none**. Accepted architecture decisions: **none**. See the [POC-01 source profiles](docs/poc/POC-01-service-profiles.md) and [demo flow](docs/poc/POC-01-demo-flow.md).

## Implemented on `main`

- **POC-01 (#7):** source-backed service framing, one Fatma hero story, and a scripted golden path with two lighter routes are documented.
- **POC-02 (#8):** shared state and mock provider contracts remain available as legacy logic references.
- **POC-04 (#10):** exactly three service profiles are configured and selectable. The catalogue and dossier sample list read the same registry. Fatma's synthetic profile and planned clear/blurred/mismatch document fixtures are present. A displayed sample list is not an official or computed completeness check.

The programme names are source-linked, while the Arabic/Derja copy and administrative applicability need review. No real beneficiary data, official identity document, credential, or integration is included.

## In progress and missing from the runnable journey

- [POC-06.01 (#49)](https://github.com/Medmas07/Inclusa/issues/49): nine exact French, Derja Arabic-script, and Arabizi routing fixtures were merged in [PR #87](https://github.com/Medmas07/Inclusa/pull/87) and are kept as regression cases.
- POC-06.02/06.03/06.04 ([#50](https://github.com/Medmas07/Inclusa/issues/50), [#51](https://github.com/Medmas07/Inclusa/issues/51), [#52](https://github.com/Medmas07/Inclusa/issues/52)) were merged in [PR #96](https://github.com/Medmas07/Inclusa/pull/96) and [PR #97](https://github.com/Medmas07/Inclusa/pull/97). The mock assistant runs a scripted, offline, rule-based dialogue manager (keywords and fact rules over the three configured services, one question per turn, manual-choice fallback, no eligibility statements) wired into an Assistant screen with quick replies and a "Préparer ce dossier" action. It is not a language model. Its Derja and Arabizi reply copy and the French document `whereToGet` text are provisional and need native-speaker and MAS/ULPS review.
- [POC-16 (#78)](https://github.com/Medmas07/Inclusa/issues/78): `ocr-scanner/` remains a separate local FastAPI scanner and dashboard. The new `backend/` FastAPI service calls its HTTP API for sessions, uploads, analysis, review, discard, state and images. Scanner startup survives a missing OCR engine and reports configured/local-ready state at `/api/health`; a CIN scan then needs an engine for analysis. JPEG/PNG uploads are limited to 15 MB, extracted text and field values are not logged, and rejected rescans preserve the previous confirmed document. The four slots are fictional and OCR accuracy remains unverified without synthetic sample images and a results and limitations table.
- POC-17.01/17.02 ([#80](https://github.com/Medmas07/Inclusa/issues/80), [#81](https://github.com/Medmas07/Inclusa/issues/81)) were merged in [PR #98](https://github.com/Medmas07/Inclusa/pull/98). The opt-in local LLM extracts intent, script and facts; the scripted dialogue manager still writes every reply. A local six-phrase run with `qwen3:4b-instruct` on November 16 scored 6/6 intent, 5/6 script and 4/6 expected fact sets. This small synthetic run is not a validation of Derja/Arabizi quality.
- There is no active beneficiary frontend, computed checklist, simulated booking, worker view, or complete end-to-end journey. These belong to later authorized POC Issues.
- Among the 18 top-level POC Issues (#7–#24), #7, #8, and #10 are closed and Done. #9 is in Backlog; the remaining top-level Issues are currently Blocked. Closing reviewed Issues has been observed to move their Project items to Done.

## Local repository and verification

- Remote: `https://github.com/Medmas07/Inclusa`. This local `main` includes merged PRs #87, #96, #97, and #98. The backend/scanner changes described above are currently uncommitted and have not been pushed.
- Backend/scanner contract tests: `python -m pytest backend/tests ocr-scanner/tests -q -p no:cacheprovider --basetemp ocr-scanner/data/pytest-run-final2` passed 6 tests with synthetic data on November 16. `python scripts/validate_repo.py` also passed. The OCR virtual environment still contained only `pip`, so real OCR accuracy and end-to-end browser behavior were not validated.
- Local launcher: `python -m pytest scripts/test_start_local.py -q -p no:cacheprovider --basetemp ocr-scanner/data/pytest-launcher` passed its process cleanup test. Syntax compilation and repository validation passed. The full service pack was not started while a separate OCR requirements installation was still running; its first real startup remains to be verified after installation.
- GPU OCR audit on November 16: `nvidia-smi` detects an NVIDIA GeForce RTX 3050 6 GB, and global Python sees CUDA (`torch 2.5.1+cu121`), but `ocr-scanner/.venv` has `torch 2.15.0+cpu` and lacks Falcon packages. Automatic OCR now selects Falcon when CUDA is available, EasyOCR only without an NVIDIA GPU, and an explicit error when an NVIDIA GPU exists but scanner PyTorch lacks CUDA. GPU model execution remains unverified; the scanner environment still needs CUDA PyTorch and Falcon dependencies. Approximately 2 GB was available on C: during this audit, so no large GPU packages were downloaded.
- The Falcon/EasyOCR selection and existing backend checks passed 11 tests with `python -m pytest backend/tests ocr-scanner/tests scripts/test_start_local.py -q -p no:cacheprovider --basetemp ocr-scanner/data/pytest-falcon-policy`. A direct warmup with the real scanner `.venv` detected the NVIDIA GPU and raised the expected CUDA-PyTorch configuration error. No real Falcon inference was run.
- The local pack now supports a persistent Falcon GPU worker on port 8002 using the host CUDA PyTorch installation through `falcon_gpu/.venv --system-site-packages`. The scanner remains in its CPU-PyTorch `.venv` and calls the worker through HTTP; the backend remains separate. Contract and launcher tests use a fake model. A real Falcon download/inference remains pending because the host Hugging Face cache drive had about 2 GB free, below the launcher's 3 GB preflight threshold.
- The split-environment contract suite passed 13 tests with `python -m pytest backend/tests ocr-scanner/tests scripts/test_start_local.py falcon_gpu/test_api.py -q -p no:cacheprovider --basetemp ocr-scanner/data/pytest-split-gpu`. `python scripts/validate_repo.py` and `git diff --check` also passed. Host CUDA detection returned true; the real cache-space preflight returned false, so no real model startup was attempted.
- A subsequent local check found about 1.05 GB free on C: and 4.27 GB free on removable F:. The launcher now accepts `--hf-home` so the project owner can explicitly place the Falcon model cache on F: with one command. No model download or file move was performed during this change.
- The explicit cache argument passed 4 focused tests with `python -m pytest scripts/test_start_local.py falcon_gpu/test_api.py -q -p no:cacheprovider --basetemp ocr-scanner/data/pytest-cache-choice`; repository validation and `git diff --check` passed. A read-only preflight with `HF_HOME=F:\Inclusa-hf-cache` returned sufficient space. Full GPU inference remains untested.
- The first worker launch failed because its unpinned `transformers 5.23.0` disabled inherited host PyTorch 2.5.1. Transformers was pinned to 4.57.1 and its `AutoModelForCausalLM` import passed, but this check did not cover the downloaded model's own PyTorch API needs.
- The next Falcon launch exposed that the downloaded model revision imports `AuxRequest`, which is absent from PyTorch 2.5.1 and present in PyTorch 2.9. The worker now pins the model revision and requires CUDA PyTorch 2.9.1 with Triton Windows 3.5. The launcher is prepared to provision this pair only in the worker environment and verifies it before startup. The 2.86 GB PyTorch wheel download was started and stopped after slow network progress; the local worker still has PyTorch 2.5.1 and Triton 3.1. Real GPU inference and the full provisioning path remain unverified.
- The updated contract suite passed 15 tests with `python -m pytest backend/tests ocr-scanner/tests scripts/test_start_local.py falcon_gpu/test_api.py -q -p no:cacheprovider --basetemp ocr-scanner/data/pytest-falcon-aux`; repository validation passed. The worker still needs the large CUDA wheel before a live Falcon startup can be validated.

## Next checks

1. Once OCR dependencies finish installing, run a synthetic CIN photo through the real engine and record recognition limits. The mocked contract tests do not measure OCR accuracy.
2. Connect the beneficiary frontend to the new backend in a separately scoped change; preserve the deterministic assistant path and opt-in local LLM path.
3. Confirm source currency, document applicability, territorial ULPS, and any appointment process with MAS/ULPS mentors before presenting citizen guidance as verified.

Never add secrets or real beneficiary documents to the repository.
