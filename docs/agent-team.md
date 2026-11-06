# Agent team for Mona's Project Pulse dashboard

I will use a four-agent custom team to plan, design, implement, and coordinate the Mona's Project Pulse dashboard work. The agent definitions live under `.github/agents/` and are orchestrated through GitHub Copilot CLI in a Codespace.

- Orchestrator — Model: Claude Opus 5.5 (copilot). Responsibilities: break the work into phases, delegate tasks to specialists, manage dependencies and parallelism, and verify the final integration. Definition: `.github/agents/orchestrator.agent.md`.
- Planner — Model: Claude Opus 5.5 (copilot). Responsibilities: research the repo, inspect relevant docs and constraints, and produce the implementation plan, file assignments, dependencies, risks, and validation expectations. Definition: `.github/agents/planner.agent.md`.
- Coder — Model: GPT-5.5 (copilot). Responsibilities: implement the code and fix logic within the assigned file scope, with support for runnable app tasks like creating the VS Code launch config for the dashboard. Definition: `.github/agents/coder.agent.md`.
- Designer — Model: Gemini 3.7 Flash (copilot). Responsibilities: shape the dashboard UX/UI, accessibility, responsive layout, visual hierarchy, and Project Pulse styling direction. Definition: `.github/agents/designer.agent.md`.

The workflow uses the GitHub Copilot CLI in a Codespace as the coordinator, with the Orchestrator delegating to the Planner, Designer, and Coder so each specialist can focus on their domain while the work stays aligned with the dashboard requirements.
