# AgentPulse

**Orchestrate. Monitor. Maximize your AI coding agents.**

AgentPulse is a developer-first monorepo of tools that reduce the friction of working with AI coding agents (Claude Code, Codex, Gemini CLI). Instead of juggling terminals and losing context, you get smart notifications when tasks finish and live token/cost dashboards so you always know where you stand.

Built because I use agents every day and the tooling around them hasn't kept up.

---

## Packages

| Package | What it does | Install |
|---|---|---|
| [**AgentNotifier**](packages/agent-notifier) | Desktop notifications when agent tasks complete | `pipx install agent-notifier` |
| [**AgentVitals**](packages/agent-vitals) | Live token usage, context window, and cost dashboard | `pip install agent-vitals` |

Each package is an independent PyPI release with its own repo, versioned and releasable separately. This repo is the integration point.

---

## AgentNotifier

Sends a desktop notification (with optional chime) the moment an agent task finishes, so you can stay focused on other work without polling.

**Supports:** Claude Code · Codex · Gemini CLI · Ollama · any shell command

```bash
# Wrap any long-running command
agent-notifier run -- python train.py

# Or wire it into your agent CLI hooks (one-time setup)
agent-notifier setup all --project "$PWD"
```

Integrates via the native hook systems of each agent CLI — no wrappers required after setup. Works on macOS, Windows, and Linux with platform-native notification backends and a console fallback.

→ [Full documentation](packages/agent-notifier/README.md)

---

## AgentVitals

A live Rich TUI dashboard that tracks token accounting, estimated cost, and context window consumption in real time as you work with an agent.

```
┌─ AgentVitals ────────────────────────────────────────────┐
│ Model        │ gpt-4-turbo                                │
│ Input tokens │ 18,432        Output tokens │ 4,210        │
│ Session cost │ $0.41                                      │
│ Context used │ ████████████░░░░░░░░  63%  (81K / 128K)   │
│ Throughput   │ 1,240 tok/min  Interactions: 7             │
└──────────────────────────────────────────────────────────┘
```

Run a live dashboard alongside Codex in a second terminal:

```bash
# Terminal 1 — use Codex normally
codex

# Terminal 2 — live vitals
agent-vitals watch
```

→ [Full documentation](packages/agent-vitals/README.md)

---

## Architecture

```
AgentPulse
├── packages/agent-notifier   ← Attention layer: "tell me when it's done"
│   ├── CLI entry point (Click)
│   ├── Hook bridges (codex-hook, claude-hook, gemini-hook)
│   ├── Notification backends (macOS / Windows / Linux / console)
│   └── Setup automation + doctor diagnostics
│
└── packages/agent-vitals     ← Quantified layer: "can I keep going?"
    ├── CLI + Rich TUI dashboard
    ├── VitalsTracker (token accounting, cost, context utilization)
    ├── Pricing tables (OpenAI, Anthropic, Google)
    └── Event store (JSONL log + JSON summary state)
```

**Design decisions worth noting:**

- **Hook bridge pattern** — separate entry points (`agent-notifier-codex-hook`, etc.) keep the notification logic decoupled from each agent CLI. Uses `shutil.which()` at setup time to write absolute paths into config files, solving the subprocess PATH isolation problem that affects all hook-based integrations.
- **Graceful degradation** — notification backends fall through: `terminal-notifier → osascript → console` on macOS; `BurntToast → win10toast → console` on Windows. Never silently fails.
- **Setup automation with backups** — `agent-notifier setup` edits Codex/Gemini/Claude config files in place with timestamped backups, handling TOML/JSON merging without clobbering existing entries.
- **Event-driven state in AgentVitals** — JSONL event log + summary JSON allows session history reconstruction and makes the dashboard resilient to restarts.
- **Submodule monorepo** — each package maintains its own GitHub repo and PyPI release cycle while this repo coordinates integration work.

---

## Roadmap

| Phase | Status | Description |
|---|---|---|
| 1 | Done | AgentNotifier (v0.1.5) + AgentVitals (v0.1.0) |
| 2 | Planned | Unified CLI dashboard — view all running agents in one pane |
| 3 | Planned | Context snapshots — save and restore session state across agent switches |

---

## Quick Setup (macOS)

```bash
# Install notifier
pipx install agent-notifier

# Wire all supported agent CLIs in one command
agent-notifier setup all --project "$PWD"

# Verify everything is connected
agent-notifier doctor --project "$PWD"
```

See [packages/agent-notifier/README.md](packages/agent-notifier/README.md) for Windows/Linux, per-tool setup, and troubleshooting.

---

## License

Both packages are MIT licensed. See individual package directories for details.
