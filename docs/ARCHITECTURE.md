# AgentPulse Architecture & Vision

## Vision
AgentPulse is a platform for managing and supervising AI coding agents in an intuitive, visual environment. It is designed to be developer-first, supporting multiple primary agents (Claude Code, Codex, Gemini CLI) to reduce friction and context switching.

## Core Problem
1.  **Visual Debugging:** CLI agents are hard to monitor in parallel.
2.  **Context Fragmentation:** Switching agents leads to loss of task intent and mental context.
3.  **Attention Loss:** Background tasks complete without the user knowing, breaking flow.

## Architecture

### 1. AgentNotifier (The "Attention" Layer)
*   **Purpose:** Detects task completion and user focus state.
*   **Function:** "Tell me when my agent finishes while I’m doing something else."
*   **Mechanism:** Watches process capability/stdout or hooks into the shell to trigger system notifications.

### 2. AgentVitals (The "Quantified" Layer)
*   **Purpose:** Track the "health" and consumption of the agent session.
*   **Metrics:**
    *   **Token Usage:** Cost and quota tracking.
    *   **Context Window:** Percentage of context used (visual gauge).
    *   **Time:** Session duration and efficiency.

### 3. Orchestration & Dashboard (Future)
*   **Multi-Agent Manager:** Run Claude and Gemini side-by-side.
*   **Context Manager:** Snapshot repo state and prompt context to restore later.
*   **Visual Dashboard:** A unified UI (TUI or Web) to view all running agents.

## Roadmap
1.  **Phase 1:** Build **AgentNotifier** (Completion detection) & **AgentVitals** (Usage tracking).
2.  **Phase 2:** Basic CLI Dashboard to view status.
3.  **Phase 3:** Context automation and session restoration.
