# AiPlus Agent Velocity

[中文 README](README.zh-CN.md)

## The Pain

The agent says a task will take five hours. You nod and go get coffee. Twenty minutes later it says "done." Next week the same agent estimates another five hours for a similar task. Again, twenty minutes. No one writes down the actual time. The pattern repeats until you stop trusting any estimate at all.

## The Solution

AiPlus Agent Velocity records every estimate and actual completion time as local JSONL under `.aiplus/velocity/`. It detects human-time bias when estimates anchor on engineer hours instead of agent minutes. After a few records, it produces p50 and p90 AI-native estimates and shows a next-estimate adjustment. No raw prompts are stored. No data uploads. Normal records rotate at 200, rare cases at 20.

## Quick Start

If you already have AiPlus:

```bash
cd MyProject
aiplus install codex        # or: claude-code, opencode, all
aiplus velocity init
aiplus velocity estimate --task-type compact-hardening --human-estimate 5h
```

Or clone the standalone source:

```bash
git clone https://github.com/izhiwen/aiplus-agent-velocity.git
cd aiplus-agent-velocity
```

## Runtime Support

Velocity works with all three supported agents. Install AiPlus for your runtime:

```bash
aiplus install codex        # Codex
aiplus install claude-code  # Claude Code
aiplus install opencode     # OpenCode
```

## What's Inside

- `core/schemas/` — JSON schemas for config, estimate-record, run-record, rare-case-record
- `core/` — Duration parser, bias detection, retention logic
- `README.md` — This file
- `DESIGN.md` — Architecture and design decisions

## Common Commands

```bash
aiplus velocity init                      # Initialize velocity tracking
aiplus velocity estimate                  # Create an AI-native estimate
aiplus velocity complete                  # Record actual completion time
aiplus velocity bias --task <id>          # Check bias for a task
aiplus velocity report                    # Show bias and adjustment report
aiplus velocity doctor                    # Run velocity health checks
aiplus velocity purge --yes               # Purge old records
```

## Storage

All data stays in `.aiplus/velocity/` as local JSONL:

- `estimates.jsonl` — Estimate records
- `runs.jsonl` — Completion records
- `rare-cases.jsonl` — Rare case records
- `config.json` — Configuration
- `multipliers.json` — Aggregate adjustments

No SQLite. No cloud. No telemetry.

## Safety Boundaries

AiPlus Agent Velocity does not:
- Store raw prompts or transcripts
- Upload data or implement telemetry
- Replace tests, review, or Owner gates
- Act as a productivity tracker or KPI system

## Roadmap

See the [main AiPlus repo](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md) for current gaps.

## License

[Apache-2.0](LICENSE)
