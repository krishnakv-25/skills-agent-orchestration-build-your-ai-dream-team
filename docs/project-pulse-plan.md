# Project Pulse Dashboard Implementation Plan

## Summary

Build Mona’s Project Pulse as a small, static dashboard that helps contributors scan active projects, owners, status, recent activity, priority or risk, and a short summary. Use only HTML, CSS, and JSON—no framework, package manager, build process, or additional application files.

Repository research found that `app/` has no existing files and `.vscode/launch.json` does not exist. The repository already has `.vscode/tasks.json`; leave it unchanged. The Project Pulse brief, exercise instructions, agent definitions, and Step 3 workflow checks define the required UI and launch behavior. Those checks validate file presence, keyphrases, and JSON syntax, but do not prove that the dashboard renders or the launch configuration works.

The **Designer** owns the visual design and `app/styles.css`. The **Coder** owns the HTML, JSON data, and VS Code launch configuration. The **Orchestrator** coordinates the shared contracts, integration, and validation; it does not implement files.

## Ordered implementation steps

### 1. Agree on the shared UI and data contracts

**Owner:** Orchestrator, with Designer and Coder input
**Files:** None

Before implementation, confirm the following contract to avoid CSS/HTML/data mismatches:

- The JSON has a top-level `projects` array. Each project includes `name`, `owner`, `status`, `recentActivity`, and `priority`.
- Include a `summary` field as well, so the dashboard can meet the brief’s contributor-friendly summary requirement.
- Agree on deterministic class hooks before parallel work begins: at minimum `.dashboard` and `.project-card`, plus the class names needed for the project grid and status and priority treatments.
- The HTML loads `project-data.json` and creates a visible card for each project.
- Treat project data as illustrative unless Mona’s team provides approved real project information.

**Dependency:** This contract must precede the parallel implementation work in Step 2.

### 2. Implement the files in parallel using isolated ownership

**Designer assignment — `app/styles.css`**

Create a polished, responsive dashboard style based on the agreed class hooks. Include readable spacing and typography, visible project cards, clear status and priority treatments, rounded corners, and shadows. Maintain accessible color contrast and do not rely on color alone to convey status or priority.

**Coder assignment — `app/index.html` and `app/project-data.json`**

- In `app/index.html`, use the exact title **Project Pulse**, semantic page structure, and a stylesheet reference to `styles.css`.
- Load `project-data.json` and render the agreed card markup with the `.project-card` class. Show each project’s name, owner, status, recent activity, priority, and summary.
- Since this is a static app with no additional script file in scope, use a small inline script for JSON loading and rendering. Use safe text insertion rather than interpreting project values as HTML.
- In `app/project-data.json`, provide multiple representative project entries using the agreed schema.

**Coder assignment — `.vscode/launch.json`**

Create a strict JSON workspace launch configuration named **Run Project Pulse Dashboard**. Use the repository’s specified command, `python3 -m http.server 5500`, set `cwd` to `${workspaceFolder}/app`, and configure `serverReadyAction` to open `http://localhost:%s/index.html`. Ensure the readiness pattern matches the Python server’s actual output in the target environment.

**Parallel decision:** Once Step 1’s contracts are agreed, the Designer can work on `app/styles.css` while the Coder works on the HTML/data and launch configuration. These assignments do not overlap files. The launch configuration is also independent of the dashboard markup and can be completed in parallel. The Orchestrator should give each task only its listed file scope.

### 3. Integrate and resolve mismatches

**Owner:** Orchestrator coordinates; Designer and Coder make scoped corrections
**Files:** `app/index.html`, `app/styles.css`, `app/project-data.json`, `.vscode/launch.json`

After the parallel work finishes, verify that:

- The HTML’s selectors match the Designer’s CSS hooks.
- The renderer reads the agreed JSON fields and places all required values in each card.
- Relative asset paths work when served from `app/`.
- The launch target opens `index.html`, not the directory root.

If changes are needed, assign them to the existing file owner. Do not have both agents edit the same file concurrently.

**Dependency:** Integration follows completion of Step 2.

### 4. Validate the integrated dashboard and launch flow

**Owner:** Orchestrator coordinates; Designer and Coder address findings within their file scopes
**Files:** All four implementation files

Run the checks in the next section. Start the dashboard using **Run Project Pulse Dashboard**, confirm the rendered page, then stop the server. Resolve integration or validation failures before handoff.

**Dependency:** Validation must follow integration. The launch smoke test requires the finished app and launch configuration.

## Dependencies and parallel work

- **Must run sequentially:** Agree on the data and CSS/HTML contracts (Step 1) before implementation; integrate after both implementation tracks finish; validate after integration.
- **Can run in parallel:** Designer’s stylesheet work and Coder’s HTML/data work after Step 1. The launch configuration can also be created independently in Step 2.
- **Must not overlap:** Assign `app/styles.css` only to Designer and `app/index.html`, `app/project-data.json`, and `.vscode/launch.json` only to Coder. If follow-up edits are needed, keep the same ownership boundaries.

## Edge cases and risks

- **Missing, invalid, or empty data:** Show a clear loading, error, or empty state rather than a blank dashboard or broken layout.
- **Missing or malformed fields:** Avoid rendering `undefined`; use a clear fallback or report invalid input.
- **Untrusted text:** Insert project values as text, not HTML.
- **Unexpected status or priority values:** Keep badges understandable and readable without depending on a fixed set of colors.
- **Long content and small screens:** Ensure long names and activity summaries wrap without breaking the card layout; check narrow viewport behavior.
- **Accessibility:** Use semantic landmarks and heading order, visible focus where interactive elements exist, readable contrast, and text or labels in addition to color.
- **JSON loading:** `fetch()` requires serving the app over HTTP; opening `index.html` directly as a `file://` URL is not the supported preview path.
- **Launch behavior:** A port conflict, unavailable Python command, unsupported launch type, or readiness-pattern mismatch can prevent auto-opening. Confirm the configuration against VS Code’s debugger schema and test it in the target Codespace.
- **Server scope:** Python’s `http.server` is appropriate for this local preview, not production hosting.

## Validation expectations

1. **Static checks**
   - Confirm all four assigned files exist.
   - Parse `app/project-data.json` and `.vscode/launch.json` as JSON.
   - Check that the dashboard title is **Project Pulse**, the HTML references `styles.css` and `project-data.json`, and project cards expose all required fields.
   - Confirm `.dashboard` and `.project-card` styling hooks and the expected visual treatment (`border-radius`, `box-shadow`, responsive layout).
   - Confirm the launch configuration is named **Run Project Pulse Dashboard**, uses the `app/` working directory and port 5500 command, and targets `index.html`.

2. **Runtime smoke test**
   - Start **Run Project Pulse Dashboard** in VS Code.
   - Confirm the browser opens the dashboard page, not a directory listing, and that multiple JSON-backed project cards render.
   - Check that a data-load failure or empty project list produces a useful state.
   - Inspect the dashboard at desktop and narrow viewport widths; verify keyboard access and that status/priority remain understandable without color.
   - Stop the preview server after testing.

3. **Repository checks**
   - Run the Step 3 workflow checks where available. Treat their file, keyphrase, and JSON checks as necessary but not sufficient: they do not prove browser rendering, accessibility, or launch behavior.

## Open questions and assumptions

- No approved project names, owners, statuses, activity updates, priorities, or summaries were found in the repository. **Assumption:** use clearly illustrative entries unless Mona supplies real data.
- The brief lists five required data fields while also requiring a contributor-friendly summary. **Plan decision:** add `summary` as a sixth project field and render it.
- No separate JavaScript file is in scope. **Plan decision:** keep the small JSON-loading renderer inline in `app/index.html`; ask before adding another file.
- The target launch debugger type and Python server output may vary by VS Code/Codespaces version. Confirm the chosen launch type and readiness pattern using VS Code’s schema and an actual run.
- Search, filtering, sorting, persistence, API integration, and production deployment are outside this dashboard’s scope.

## Documentation consulted

- Repository: `.github/project-pulse-brief.md`, `.github/steps/3-step.md`, `.github/agents/designer.agent.md`, `.github/agents/coder.agent.md`, `.github/agents/orchestrator.agent.md`, `.github/workflows/3-step.yml`, and `.vscode/tasks.json`.
- [VS Code: Configure debugging](https://code.visualstudio.com/docs/debugtest/debugging-configuration) — workspace `launch.json`, debugger-specific properties, and `serverReadyAction`.
- [Python: `http.server`](https://docs.python.org/3/library/http.server.html) — local server behavior and its production-use warning.
- [MDN: `<main>` element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/main) — semantic main landmark.
- [MDN: `Promise.prototype.catch()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) — handling rejected asynchronous data loads.
