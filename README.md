# science-plugins

A personal Claude Code plugin marketplace for mathematical computing, scientific research, and developer productivity. Add the marketplace once, then install any plugin with a single command.

## What this is

Claude Code can be extended with plugins — bundles of agents, commands, skills, and hooks that add new capabilities to your sessions. This repository is a marketplace catalog: it tells Claude Code where to find plugins and what they contain. Point Claude Code at this catalog once, and from then on you can install any listed plugin with `/plugin install`.

The plugins here are focused on quantitative research and developer productivity, and they're designed to work together. `wolfram-hart` handles symbolic math. `compound-science` handles econometric workflows. `claude-codex` routes mechanical tasks to a cheaper model. `ralpha-team` drives long-running jobs to completion without babysitting.

## How plugins work

Claude Code plugins have four types of components:

- Skills are passive triggers. When your prompt matches a skill's description, Claude loads that skill's instructions and follows them without any explicit invocation from you.
- Commands are slash commands you call directly (e.g. `/estimate`, `/eval`). They run a structured prompt with defined tools and argument handling.
- Agents are subprocesses Claude spawns to handle a focused task (research, review, implementation), each with its own context window.
- Hooks run in response to Claude Code lifecycle events (session start, prompt submit, tool call) without being invoked at all.

Most real-world behavior comes from combining these. A hook detects context, a skill loads additional instructions, a command handles the interface for the heavy lifting.

## Add this marketplace

```
/plugin marketplace add James-Traina/science-plugins
```

This downloads the catalog and registers it locally. You don't need to reinstall the marketplace when plugins are updated.

## Plugins

---

### [wolfram-hart](https://github.com/James-Traina/wolfram-hart)

Gives Claude a real computer algebra system. Without this, Claude approximates symbolic math: it reasons about it rather than computing it. This plugin connects Claude to `wolframscript`, the command-line interface to Wolfram Language (the engine behind Mathematica and Wolfram Alpha).

When a math or science question comes up, the included skill triggers. Claude translates the problem to Wolfram Language, runs it through `wolframscript`, and shows you the computed result — the actual output, not a reconstruction from training data. Plots are saved as PNGs and shown inline. You never need to write any Wolfram Language yourself.

Coverage includes symbolic algebra, calculus, linear algebra, statistics, differential equations, number theory, combinatorics, and plotting. The three commands give you direct control: `/eval` runs Wolfram Language code you supply; `/check` verifies a mathematical claim step-by-step; `/patterns` lists the problem types the skill handles. The `wolfram-reviewer` agent audits a derivation or proof, running each step through the engine to check it independently.

**Components:** 1 skill · 3 commands (`/eval`, `/check`, `/patterns`) · 1 agent (`wolfram-reviewer`)

**Prerequisites:** A free [Wolfram Engine](https://www.wolfram.com/engine/) (local, ~1 GB) or [wolframscript](https://www.wolfram.com/wolframscript/) for cloud evaluation. Requires a free Wolfram ID.

```
/plugin install wolfram-hart@science-plugins
```

---

### [compound-science](https://github.com/James-Traina/compound-science)

A research workflow toolkit for structural econometrics, causal inference, game theory, applied micro, identification arguments, Monte Carlo studies, and reproducible pipelines. Inspired by [compound-engineering](https://github.com/EveryInc/compound-engineering-plugin): the same plan → work → review → compound loop, adapted for quantitative social science.

The organizing idea is that solved problems should stay solved. Every methodological fix — a convergence workaround, an identification argument that held up, a numerical issue and how it was handled — gets documented and made searchable in `docs/solutions/`. The next project starts where the last one left off. Over time you build a searchable record of hard-won solutions, and Claude can draw on it when facing a similar problem.

The plugin is large by design: 56 components across a wide surface of quantitative methods. Domain commands handle complete pipelines: `/estimate` runs structural estimation end-to-end with convergence monitoring, gradient diagnostics, and standard errors; `/simulate` designs a Monte Carlo study with seed management and summary statistics; `/identify` walks through an identification argument and checks it for holes. The ambient hooks run without being called — one watches for estimation packages being imported and loads relevant context, another checks for missing random seeds before a simulation runs, and a merge hook validates that numerical results are reproducible before a commit goes through.

The 20 agents each handle a specific methodological task: `model-architect` for structural model design, `estimator` for numerical implementation, `identification-auditor` for checking exclusion restrictions and instrument validity, `replication-packager` for producing AEA-compliant submission packages, and more. Each agent has a narrow scope and its own context window, so complex research workflows can be split into focused subtasks without any single context getting cluttered.

**Components:** 20 agents · 15 commands · 14 skills · 7 hooks (56 total)

**Domain coverage:** NFXP/MPEC/BLP, IV/2SLS/GMM, DiD (including staggered), RDD, synthetic control, double ML, causal forests, Nash/BNE equilibria, entry models, auction theory, Bayesian estimation, AEA-compliant replication packages

```
/plugin install compound-science@science-plugins
```

---

### [claude-codex](https://github.com/James-Traina/claude-codex)

Routes mechanical coding tasks to OpenAI Codex, cutting Anthropic token usage by 60–85% on eligible work — without changing how you use Claude Code.

A `UserPromptSubmit` hook intercepts every prompt before Claude sees it. Mechanical, well-specified tasks (writing a function from a docstring, generating boilerplate, search-and-replace refactors) go to Codex. Anything that needs reasoning, context, or judgment stays with Claude. You don't have to decide which model handles what.

Delegated responses come back prefixed with `[via Codex · category]` so you know what happened. A correctness gate runs on every Codex response: if Claude catches a mistake, it appends a `[Claude note]` correction. `/codex` lets you force a specific prompt to Codex directly. `/savings` prints a running report of tasks delegated and estimated cost saved.

**Components:** 2 hooks · 1 agent · 1 skill · 2 commands (`/codex`, `/savings`)

**Prerequisites:** [OpenAI Codex CLI](https://github.com/openai/codex) (`npm install -g @openai/codex`), authenticated with `codex auth`. Requires `jq`.

```
/plugin install claude-codex@science-plugins
```

---

### [ralpha-team](https://github.com/James-Traina/ralpha-team)

An agentic loop that runs your prompt until the job is actually done. Give it an objective, a verification command, and optionally a team size. It feeds the prompt back, iteration after iteration, until the verification command exits 0.

The problem it solves: on long tasks, Claude often produces something that looks done but fails the actual test. You feed back the error, Claude fixes it, it fails again for a different reason, and so on. `ralpha-team` automates that loop. You define what "done" means (a test suite passing, a lint check clearing, a specific file with specific content), and it keeps going until that definition is met.

Solo mode runs a single session with access to previous work through files and git history. Team mode spawns parallel agents — architect, implementer, tester, reviewer, debugger — coordinated by a lead that assigns subtasks and aggregates results. Both modes require a dual exit: Claude must output a specific promise phrase *and* the verification command must exit 0. If Claude claims it is done but the command fails, the loop continues with the failure fed back in as context. This closes the common failure mode where the model declares success without checking.

`/status` and `/cancel` let you inspect and interrupt a running loop. `/qa` runs a standalone quality audit on the current state without triggering a new iteration.

**Components:** 5 agents · 5 commands (`/team`, `/solo`, `/status`, `/cancel`, `/qa`) · 5 hooks

**Prerequisites:** Requires `jq`. Team mode requires `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` in your Claude Code settings.

```
/plugin install ralpha-team@science-plugins
```

---

## Updating

Plugins are versioned: Claude Code caches them locally and only fetches an update when the version number in the manifest changes.

```bash
# Refresh the marketplace catalog (check for newly added or removed plugins)
/plugin marketplace update science-plugins

# Update an installed plugin to its latest version
/plugin update wolfram-hart
/plugin update compound-science
/plugin update claude-codex
/plugin update ralpha-team
```

Run `/plugin marketplace update` periodically to check for new additions to the catalog. Run `/plugin update <name>` to pull new component files for an already-installed plugin.

## License

MIT
