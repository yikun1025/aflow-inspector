# AFlow Inspector

Static viewer + tutorial for [AFlow](https://github.com/FoundationAgents/AFlow) workflow traces.
Produced from running AFlow's trained workflows on Qwen2.5-7B-Instruct-AWQ across 4 benchmarks.

🔗 **Live demo**: https://yikun1025.github.io/aflow-inspector/

## What's here

| File | Purpose |
|------|---------|
| `index.html` | Trace inspector — drag a `traces.jsonl` to view per-query DAG + node-level data + failure analysis |
| `tutorial.html` | 8-chapter tutorial that walks you from "what is AFlow" to "anatomy of a trained workflow" |
| `samples/*.jsonl` | Bundled trace data, n=200 per benchmark — click the buttons on the inspector to load |

## Sample data summary

| Benchmark | n | Avg score | Workflow |
|-----------|:-:|:-:|:-:|
| HotpotQA | 200 | 66.9% F1 | `graphs_test/round_3` (3×AnswerGenerate → ScEnsemble → Custom_FORMAT) |
| DROP | 200 | 64.6% F1 | `graphs_test/round_3` (same structure as HotpotQA) |
| GSM8K | 200 | 90.0% acc | `graphs_test/round_10` (5×Custom → ScEnsemble → Programmer verify) |
| MATH | 200 | 72.5% acc | `graphs_test/round_5` (Programmer → 4×Custom → ScEnsemble) |

Full experiment context: 5/14–5/16 Phoenix HPC, executor model = Qwen/Qwen2.5-7B-Instruct-AWQ via custom batched OpenAI-compatible shim.

## Failure categories

The inspector auto-classifies each query into:

- **CORRECT** — score ≥ 0.99
- **PARTIAL** — partial token F1 match
- **REASONING_FAIL** — none of the candidate solutions contained the gold answer (model knowledge gap)
- **VOTING_FAIL** — at least one candidate had gold but ScEnsemble picked a wrong one
- **FORMAT_FAIL** — ScEnsemble picked correctly but final FORMAT step lost the answer
- **EXTRACTION_BUG** — gold appears as substring of final answer but score is low (scoring quirk)
- **OTHER_FAIL** — none of the above

## Running locally

Just open `index.html` in a browser. Drag-drop your `traces.jsonl`, or use the sample buttons (only work when served via http; file:// can't fetch).

```bash
# Quick local server:
cd <this-repo>
python -m http.server 8000
# open http://localhost:8000
```

## License

Trace data CC-BY-SA. Code MIT.
