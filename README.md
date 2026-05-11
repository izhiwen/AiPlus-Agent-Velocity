# AiPlus Agent Velocity
[中文 README](README.zh-CN.md)

## The Problem

Agents mis-bill their work in human-engineer hours, badly mismatched with reality.

You ask for a refactor. The agent says five hours. You plan your afternoon around
it. Twenty minutes later, the agent reports completion. Next week, a similar task
gets the same five-hour estimate. Again, twenty minutes. No one keeps the score,
so the same over-estimate happens again next week. After a month of this, you
stop trusting any estimate the agent gives you.

## The Solution

AiPlus Agent Velocity records every estimate and actual completion time as local
JSONL under `.aiplus/velocity/`. It tracks:

- Human estimates (what the agent initially predicts)
- Actual completion times (how long it really took)
- Task types (refactoring, feature, bugfix, review)
- Model and workflow labels (for context)

The system detects **human-time bias** — the tendency to anchor estimates on
engineer hours instead of agent minutes. After accumulating a few records, it
produces:

- **p50 estimate** — The median AI-native time for this task type
- **p90 estimate** — The conservative upper bound
- **Next-estimate adjustment** — A multiplier to apply to future human-style
  estimates

No raw prompts are stored. No data uploads. Normal records rotate at 200
entries, rare cases at 20.

## Quick Start

With AiPlus already installed:

```bash
cd MyProject
aiplus install codex        # or: claude-code, opencode, all
aiplus velocity init
```

Then use the CLI:

```bash
aiplus velocity init                      # Initialize velocity tracking
aiplus velocity estimate                  # Create an AI-native estimate
aiplus velocity complete                  # Record actual completion time
aiplus velocity bias --task <id>          # Check bias for a specific task
aiplus velocity report                    # Show overall bias and adjustment
aiplus velocity doctor                    # Run velocity health checks
aiplus velocity purge --yes               # Purge old records manually
```

## What's Inside

- `core/schemas/` — JSON schemas for config, estimate-record, run-record, and
  rare-case-record
- `core/` — Duration parser, bias detection algorithms, retention logic
- `DESIGN.md` — Architecture decisions and design rationale

## Storage

All data stays in `.aiplus/velocity/` as local JSONL files:

```
.aiplus/velocity/
  config.json           # Configuration
  estimates.jsonl       # Estimate records
  runs.jsonl            # Completion records
  rare-cases.jsonl      # Rare case records
  multipliers.json      # Aggregate adjustment multipliers
  rotation-state.json   # Rotation tracking
```

- No SQLite. No database.
- Normal records: keep latest 200
- Rare cases: keep latest 20
- Aggregate multipliers survive raw record rotation

## Safety Boundaries

AiPlus Agent Velocity does not:

- Store raw prompts, transcripts, or source code
- Upload data or implement telemetry
- Replace tests, review, or Owner gates
- Act as a productivity tracker or KPI system
- Make estimates shorter as an excuse to skip verification

## More Info

See the [main AiPlus repository](https://github.com/izhiwen/aiplus) for the
complete platform.

Current gaps and planned work:
[v0.5.2 known gaps](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md).

## License

[Apache-2.0](LICENSE)
