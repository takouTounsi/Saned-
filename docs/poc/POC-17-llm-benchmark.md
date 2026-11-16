# POC-17 local LLM intent extraction benchmark

Refs [#80](https://github.com/Medmas07/Inclusa/issues/80) (POC-17.01) and [#81](https://github.com/Medmas07/Inclusa/issues/81) (POC-17.02), parent [#23](https://github.com/Medmas07/Inclusa/issues/23).

**Status: not yet run.** No measurement exists. The table below is intentionally empty until a teammate with Ollama runs the command.

## What is measured

Six synthetic phrases (2 Arabizi, 2 Derja in Arabic script, 2 French) are sent to a local Ollama model. The model only extracts `{intent, languageOrScript, facts}`; the output is validated against an allowlist (`demo/llm-intent.js`). The table compares predicted and expected intent, script and facts, and records latency. `Facts` is "ok" when every expected fact is exact; unexpected additional facts are counted separately. The model never writes replies, decides eligibility or lists documents. The scripted dialogue manager stays the default.

## How to run (needs Ollama)

```sh
ollama pull qwen2.5:3b
ollama serve
node demo/bench/llm-intent-bench.mjs --write
```

Options: `--model=<name>` (default `qwen2.5:3b`), `--base-url=<url>` (default `http://127.0.0.1:11434`, loopback only). Without `--write` the table is only printed. If Ollama is not reachable the script prints `blocked: Ollama not reachable at ...` and exits with code 1.

## Results

| # | Phrase | Expected | Predicted | Intent | Script | Facts | Extra facts | Latency (ms) |
|---|--------|----------|-----------|--------|--------|-------|-------------|--------------|
| | | | | | | | | |

## Decision

TBD. #81 asks to stop if stable extraction is not achieved. The team decides from a real run whether this model is stable enough on Derja and Arabizi; nothing here validates that. Derja and Arabizi support is unverified.
