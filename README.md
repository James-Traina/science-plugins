# science-plugins

A personal Claude Code plugin marketplace for mathematical computing, scientific research, and developer productivity. Add this marketplace once, then install any plugin with a single command.

## Add This Marketplace

```
/plugin marketplace add James-Traina/science-plugins
```

## Plugins

---

### [wolfram-hart](https://github.com/James-Traina/wolfram-hart)

Gives Claude a real math engine. When a math or science question comes up, Claude translates it to Wolfram Language, runs it through `wolframscript`, and presents the exact result. Plots are saved as PNGs and displayed inline. No special syntax needed.

Covers symbolic algebra, calculus, linear algebra, statistics, differential equations, number theory, and plotting. If Wolfram Language can do it, this plugin makes it available.

**Components:** 1 skill · 3 commands (`/eval`, `/check`, `/patterns`) · 1 agent (`wolfram-reviewer`)

**Prerequisites:** A free [Wolfram Engine](https://www.wolfram.com/engine/) (local, ~1 GB) or [wolframscript](https://www.wolfram.com/wolframscript/) for cloud evaluation. Requires a free Wolfram ID.

```
/plugin install wolfram-hart@science-plugins
```

---

### [compound-science](https://github.com/James-Traina/compound-science)

Research workflow toolkit for structural econometrics, causal inference, game theory, applied micro, identification arguments, Monte Carlo studies, and reproducible pipelines. Inspired by [compound-engineering](https://github.com/EveryInc/compound-engineering-plugin) — the same plan → work → review → compound loop, adapted for quantitative social science.

Every solved methodological problem (a convergence fix, an identification argument, a numerical issue) gets documented and made findable in `docs/solutions/`. The next project starts where the last one left off.

Domain commands handle complete pipelines: `/estimate` runs structural estimation with convergence monitoring and standard errors; `/simulate` designs Monte Carlo studies; `/identify` formalizes identification arguments. Ambient hooks run without being invoked — watching for estimation packages, missing seeds, and unvalidated merges.

**Components:** 20 agents · 15 commands · 14 skills · 7 hooks (56 total)

**Domain coverage:** NFXP/MPEC/BLP, IV/2SLS/GMM, DiD (including staggered), RDD, synthetic control, double ML, causal forests, Nash/BNE equilibria, entry models, auction theory, Bayesian estimation, AEA-compliant replication packages

```
/plugin install compound-science@science-plugins
```

---

### [claude-codex](https://github.com/James-Traina/claude-codex)

Intelligent task router that automatically delegates mechanical coding tasks to OpenAI Codex, reducing Anthropic token usage by 60–85% on eligible work — automatically, transparently, and without changing how you use Claude Code.

A `UserPromptSubmit` hook classifies every prompt with weighted pattern matching and routes it to Codex or Claude. Delegated responses are prefixed with `[via Codex · category]` so you always know which model answered. A correctness gate instructs Claude to append a `[Claude note]` correction if the Codex output is wrong. Use `/savings` to see a running report of tasks delegated and estimated costs saved.

**Components:** 2 hooks · 1 agent · 1 skill · 2 commands (`/codex`, `/savings`)

**Prerequisites:** [OpenAI Codex CLI](https://github.com/openai/codex) (`npm install -g @openai/codex`), authenticated with `codex auth`. Requires `jq`.

```
/plugin install claude-codex@science-plugins
```

---

### [ralpha-team](https://github.com/James-Traina/ralpha-team)

Runs your prompt in a loop until the job is actually done. Give it an objective, a verification command, and optionally a team size. It feeds the prompt back, iteration after iteration, until the verification command exits 0.

Solo mode runs a single session with access to previous work in files and git history. Team mode spawns parallel agents — architect, implementer, tester, reviewer, debugger — coordinated by a lead. Both modes use a dual gate: Claude must output a promise phrase *and* the verification command must pass. If Claude claims done but the command fails, the loop continues with the failure output fed back in.

**Components:** 5 agents · 5 commands (`/team`, `/solo`, `/status`, `/cancel`, `/qa`) · 5 hooks

**Prerequisites:** Requires `jq`. Team mode requires `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` in your Claude Code settings.

```
/plugin install ralpha-team@science-plugins
```

---

## Updating

```bash
# Refresh the marketplace catalog
/plugin marketplace update science-plugins

# Update an installed plugin to its latest version
/plugin update wolfram-hart
/plugin update compound-science
/plugin update claude-codex
/plugin update ralpha-team
```

## License

MIT
