# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

This repo is a teaching project (course material, see `docs/*.pdf` — 极客时间 LoopEngineering) that builds the **same AI knowledge-base pipeline four times**, each version adding one layer of agent-engineering sophistication on top of the last. All four versions coexist side by side as independent, runnable projects — they are not branches, they are the curriculum:

| Dir | Adds | Orchestration |
|---|---|---|
| `v1-skeleton/` | Agent role definitions only, no code | manual `@collector`/`@analyzer`/`@organizer` calls in OpenCode |
| `v2-automation/` | `pipeline/pipeline.py` script, JSON validation hooks, GitHub Actions | linear Python script |
| `v3-multi-agent/` | LangGraph state graph with a review/revise loop, Router & Supervisor patterns, cost guard, security/PII checks | `workflows/graph.py` (StateGraph) |
| `v4-production/` | Multi-channel distribution (Telegram/飞书), interactive bot, OpenClaw gateway, Docker | v3's graph, wrapped by `pipeline/pipeline.py` + `distribution/` |

Each version's `pipeline`/`workflows` code is **copied forward**, not imported across directories — `v4-production/workflows/` is a snapshot of `v3-multi-agent/workflows/`, kept in sync manually. When fixing a bug that exists in both, check whether it needs fixing in both places.

The agent runtime used by this project is **OpenCode**, not Claude Code. Each version has its own `AGENTS.md` — that is OpenCode's equivalent of this file and is the actual source of truth for that version's coding rules, file-naming conventions, and agent-collaboration rules. Read the relevant version's `AGENTS.md` before working inside it; this root file only covers what's common across all four.

## Commands

All commands are run from inside the relevant version directory (`cd v3-multi-agent` etc.), with `.env` set up from `.env.example` (needs `LLM_API_KEY`, `LLM_BASE_URL`, `LLM_MODEL` — DeepSeek/Qwen/OpenAI-compatible).

```bash
pip install -r requirements.txt
```

**v2-automation** — linear pipeline:
```bash
python pipeline/pipeline.py --sources github,rss --limit 20 --verbose
python hooks/validate_json.py knowledge/articles/*.json   # required-field / ID-format checks
python hooks/check_quality.py knowledge/articles/*.json   # five-dimension quality scoring
```

**v3-multi-agent** — LangGraph workflow and its standalone tests (no pytest runner wired up; scripts are run directly and expected to exit 0):
```bash
python3 -m workflows.graph                 # run the full plan→collect→analyze→review(→revise)→organize graph
python3 -m patterns.planner                 # zero-LLM unit check
python3 tests/cost_guard.py                 # zero-LLM unit check
python3 tests/eval_test.py                  # add --slow to include LLM-calling eval cases
python3 patterns/router.py "<query>"        # Router pattern (1 request → 1 handler)
python3 patterns/supervisor.py "<query>"    # Supervisor pattern (1 task → N workers → merge)
```

**v4-production** — pipeline + distribution + bot:
```bash
python3 -m pipeline.pipeline --no-publish   # run graph only, write knowledge/articles/*.json
python3 -m pipeline.pipeline                # run graph + publish to Telegram/飞书/file
python3 daily_digest.py                     # publish-only, reads existing articles
docker compose up -d                        # pipeline (cron) + bot + openclaw gateway, all three services
```

There is no linter or formatter configured in any version — don't invent one.

## Architecture (applies across v2–v4)

**Pipeline stages, strictly one-directional**: Collector → Analyzer → Organizer (→ Reviewer → Publisher in v3/v4). Never write code that has a later stage mutate an earlier stage's output file.

- v3/v4's LangGraph topology (`workflows/graph.py`) is 7 nodes: `plan → collect → analyze → review`, which then branches three ways — pass → `organize` (END); fail and under the retry cap → `revise → review` (loop); fail and over the cap → `human_flag` (END). There is no separate `save` node — persistence happens inside `organize`.
- The review loop is capped (3 iterations in v3) specifically to prevent infinite revise/review cycling — respect that cap if touching `nodes.py`/`reviewer.py`/`reviser.py`.
- `patterns/router.py` and `patterns/supervisor.py` are standalone alternative orchestration demos (not wired into the main graph) — Router for 1:1 intent dispatch, Supervisor for 1:N fan-out/merge. Keep them independent of `workflows/`.
- Quality gate: analyzer/reviewer scores below threshold are dropped/looped, not silently kept — an Organizer that writes low-scoring items is a bug, not a feature.

**Data conventions** (all versions):
- Raw data: `knowledge/raw/{source}-{YYYY-MM-DD}.json`; finished entries: `knowledge/articles/{YYYY-MM-DD}-{slug}.json`; `knowledge/articles/index.json` is a regenerated index, not hand-edited.
- JSON: 2-space indent, UTF-8, ISO 8601 timestamps. Required fields on every article: `id`, `title`, `source`, `url`, `collected_at`, `summary`, `tags`, `relevance_score` (v3+ also `category`, `key_insight`).
- Language split: code/JSON keys/filenames in English; summaries and analysis text in Chinese; tags are lowercase-hyphenated English (e.g. `large-language-model`).
- Idempotency matters: re-running the same day's collection must not create duplicate entries.
- Errors are logged and the offending item is skipped — never let one bad article/network failure abort the whole pipeline run. Malformed items get written to `knowledge/raw/errors-{date}.json` for manual review, not discarded silently.

**GitHub Actions**: `.github/workflows/daily-collect.yml` (v2, 08:00 UTC) and `daily-collect-v4.yml` (v4, 00:00 UTC, deliberately offset to avoid a commit race) both auto-commit new `knowledge/` files back to the repo. v4's job runs `--no-publish` in CI — actual distribution (Telegram/飞书) is left to the local OpenClaw bot / local cron, since those secrets aren't in CI.

**v4 distribution/bot layer**: `distribution/formatter.py` converts an article set into Markdown/Telegram/飞书-specific formats; `distribution/publisher.py` pushes to all configured channels concurrently and independently — one channel failing (e.g. missing webhook) must not block the others. `bot/knowledge_bot.py` does read/write/delete permission tiers and command-prefix-first intent matching (`/search` before natural-language keyword matching). `openclaw/` is the message-gateway config (JSON5, not JSON) that routes user messages to `knowledge-query` / `daily-briefing` / `subscription-manager` / `general-chat` agents.
