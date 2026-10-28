# Purrfect Shelter — Agent-First Harness Engineering Lab

> **Status:** Project charter / learning plan. No application code has been written yet.
>
> **Starting principle:** Humans steer; agents execute. Our aim is not merely AI-assisted coding, but enabling a coding agent to participate reliably in the **full engineering lifecycle**: specification, design, implementation, testing, UI inspection, data investigation, observability, code review, delivery, and maintenance.

## 1. Two connected goals

### Product: a cozy, educational virtual cat shelter

Build a playable browser game in which the player volunteers at a cat shelter, cares for cats with different personalities, and learns evidence-informed principles of responsible cat ownership through decisions and observed consequences.

The teaching loop is **observe → decide → experience → understand**. Gameplay should help players learn:

1. Cat body language, consent, trust, and stress signals.
2. Cat-friendly home setup: safe retreats, litter, scratching, vertical space, resources, and enrichment.
3. Daily routines: feeding, play, grooming, hygiene, and positive reinforcement.
4. When changes in behavior or health warrant veterinary attention.
5. Personality differences, multi-cat compatibility, and gradual introductions.
6. Adoption readiness: time, cost, housing, travel, and long-term responsibility.

**Educational integrity:** Keep reviewed animal-welfare guidance separate from simplified game mechanics. Behavioral responses should not imply that every cat reacts identically. Do not present the game as veterinary diagnosis or medical advice. Link meaningful educational claims to reviewed sources (e.g. International Cat Care, Cats Protection, AAFP/ISFM guidelines, and Dutch-specific LICG guidance). Human review is required before publishing factual care lessons.

### Engineering: learn harness engineering by building the game with Codex

Use **Codex** as the primary engineering agent, with the human acting as product owner, architectural decision-maker, quality gate, and harness designer.

The learning objective is to create an environment in which the agent can independently:

- Read product context and propose specifications and designs.
- Implement features across the game, APIs, database, and tests.
- Run the application and investigate real UI behavior.
- Use scoped SQL queries to investigate persisted state and simulation history.
- Read logs, metrics, and traces to diagnose problems.
- Validate changes, review diffs, open PRs, and react to CI feedback.
- Improve documentation, tools, and constraints when recurring failures occur.

We will measure whether the harness improves reliability and autonomy; adding tools alone does not count as success.

## 2. Operating model: human, agent, and harness

```text
Human (goals, priorities, acceptance criteria, approvals)
                |
                v
Codex (design -> implement -> run -> inspect -> debug -> review)
                |
                v
Engineering harness
  |-- Repository knowledge (AGENTS.md, specs, ADRs, plans)
  |-- Development tools (shell, Git, builds, tests, local services)
  |-- Browser interaction (Playwright, screenshots, console errors)
  |-- Database investigation (scoped, primarily read-only SQL)
  |-- Observability (structured logs, metrics, traces)
  |-- Guardrails (linting, architecture constraints, permissions)
  `-- Delivery (PRs, CI, preview, release checks)
                |
                v
Running Purrfect Shelter (frontend + backend + database)
                |
                +---- feedback/evidence back to Codex and human
```

**Human responsibilities:** choose what matters, challenge design assumptions, approve educational rules, define safety/security limits, review consequential changes, and interpret evaluation results.

**Agent responsibilities:** produce plans and code, use the supplied tools to gather evidence, investigate failures, propose repairs, keep documentation current, and report uncertainty rather than claiming success without verification.

**Harness responsibilities:** make the right context and tools discoverable, restrict unsafe actions, expose verifiable signals, make feedback actionable, and preserve reproducibility.

## 3. Proposed technology stack (subject to design review)

| Layer | Initial proposal | Why it exists |
| --- | --- | --- |
| 2D game | TypeScript + Phaser, with React for menus / learning UI | Interactive cat behavior and educational overlays |
| Backend | Node.js + Fastify | Interaction APIs and persisted player progress |
| Database | PostgreSQL | Cat state, interaction history, learning progress; observable SQL diagnostics |
| Engineering agent | Codex CLI / Codex-supported development environment | Agent-first design, coding, debugging, and delivery |
| Browser checks | Playwright | Realistic interactions, screenshots, console/network inspection |
| Telemetry | Structured logs + OpenTelemetry; add Grafana tooling as warranted | Agent-readable debugging evidence |
| Local execution | Docker Compose | Reproducible app/database environment |
| CI and PRs | GitHub Actions | Automated quality gates and delivery workflow |

These are working hypotheses, not locked architectural decisions. The first design session should evaluate complexity and alternatives; begin with the thinnest viable end-to-end path, then add infrastructure when a learning objective requires it.

## 4. Concrete agent-driven feature workflow

**Example feature request:** “Add Mochi, a shy rescue cat. Let the player observe and interact with her, explain respectful approaches to frightened cats, and persist her state and interaction history.”

1. **Specify:** Codex reads product goals and animal-welfare references. It drafts requirements, accepted learning outcomes, UX states, and testable acceptance criteria. Human reviews educational claims.
2. **Design:** Codex proposes a behavioral state machine, data model, API contracts, UI interactions, and trade-offs. Record nontrivial decisions in an Architecture Decision Record (ADR).
3. **Implement:** Codex writes gameplay code, API handlers, migrations, tests, and supporting docs in a feature branch.
4. **Run and observe:** Codex starts the app, interacts through Playwright, captures screenshots, inspects browser console/network signals, and checks that the actual user experience matches the specification.
5. **Investigate persisted state:** Codex executes scoped, read-only SQL queries to examine cat state and interaction history. Local test fixtures make bugs reproducible.
6. **Debug with telemetry:** Codex correlates application logs, traces, and database observations to identify a root cause; fixes the issue and repeats the checks.
7. **Review and deliver:** Codex runs independent acceptance checks, inspects the diff, opens a PR with evidence (test results, screenshots, diagnostics), and responds to CI/review feedback. Human approval gates merging or external deployment.
8. **Improve the harness:** If the agent repeatedly fails, diagnose whether the cause is missing context, inadequate tools, weak tests, poor observability, or unclear constraints; improve the corresponding harness component and evaluate the change.

## 5. Learning milestones

### M1 — Agent-first repository and product architecture

**Question:** Can Codex understand the goal, propose a coherent design, and navigate the repository with minimal repeated explanation?

- Codex drafts `PRODUCT.md`, `ARCHITECTURE.md`, and `PLANS.md` from this guide.
- Review architecture choices and scope; make important trade-offs explicit.
- Codex scaffolds a minimal running shelter and creates a concise `AGENTS.md` that points to authoritative docs rather than duplicating them.
- Set up one-command local startup, lint/typecheck, and a smoke test.

**Done when:** A fresh agent session can understand the project, launch the app, and identify the next approved task using repository documentation alone.

### M2 — Agent-operated application and persistence

**Question:** Can Codex implement and demonstrate an end-to-end feature using the tools of a real running system?

- Build one interactive room and a first cat behavior, then add Mochi's shy-cat interaction.
- Add persistence and controlled data fixtures to enable inspection of state transitions.
- Equip Codex with Playwright and scoped development-database query tools.
- Record the evidence chain: user action → UI response → API request → persisted state.

**Done when:** Codex can independently implement, launch, exercise, and verify a cat interaction across the UI, API, and database.

### M3 — Agent-observable application and debugging

**Question:** Can Codex identify and correct an unexpected behavior using evidence rather than guessing?

- Add structured logs, stable request/interaction IDs, and traces where useful.
- Deliberately introduce a reproducible state-persistence or simulation defect in a disposable environment.
- Have Codex inspect browser behavior, SQL state, and telemetry; produce a root-cause explanation with evidence.
- Validate the fix with independent acceptance tests and replay the original failure case.

**Done when:** Codex can diagnose, repair, and verify a cross-layer bug and document the investigation without step-by-step human debugging instructions.

### M4 — Agent-driven delivery, guardrails, and maintenance

**Question:** Can Codex move a feature from approved specification to a reviewable, validated PR without compromising maintainability or safety?

- Add GitHub Actions checks: typecheck, lint, unit/integration tests, and browser smoke tests.
- Add architecture rules, regression tests, and documentation consistency checks as needed.
- Codex opens PRs with design rationale, test evidence, screenshots, risk notes, and unresolved questions.
- Test how the agent responds to failed CI, review comments, and small maintenance tasks.

**Done when:** A feature reaches a human-reviewed PR with a reproducible evidence trail and explicit quality gates.

### Optional M5 — Build selected harness components ourselves

Once we have observed genuine limits of existing coding-agent tooling, implement a lightweight orchestration/controller component (for example, tool permissions, bounded retries, evidence collection, task routing, or evaluation runs). Do **not** build a generic multi-agent system without a demonstrated need.

## 6. How we'll evaluate progress

Maintain a simple experiment log for representative tasks, including:

- Task ID, initial specification, model/tool version, and relevant harness configuration.
- Whether independent acceptance checks pass (not tests silently weakened by the implementation agent).
- First-pass success versus success after retry.
- Number and nature of human interventions.
- Time, approximate token/cost usage where available, and retries.
- Evidence retained: screenshots, logs, traces, SQL query output, and CI links.
- What failed, suspected cause, harness change, and observed result after change.

Compare baseline versus improved setups on comparable tasks, controlling the environment and execution budget as much as possible. Avoid claims of improvement based on one anecdote. Keep a small fixed regression set and introduce new tasks to limit overfitting.

## 7. Rules and boundaries

- **No claim without evidence:** “Implemented” is not the same as “tested in the running app.”
- **Document the truth:** The repository documents actual architecture and operational commands. Update them when the system changes.
- **Protect evaluation:** Keep independent acceptance criteria; the implementation agent must not weaken checks simply to get a green build.
- **Use safe sandboxes:** Restrict development tools to local/ephemeral environments. Database inspection is read-only by default; migrations and destructive changes require explicit approval.
- **No production secrets:** Keep credentials out of the repository, logs, and agent-visible context unless securely scoped and necessary.
- **Human sign-off:** Require review before merges, deployments to shared/production environments, and publishing cat-care guidance.
- **Escalate uncertainty:** The agent should stop or request help when constraints conflict, required knowledge is missing, or checks cannot validate a risky change.
- **Avoid ceremony:** Every new harness mechanism must address an observed limitation or a clearly justified risk.

## 8. First Codex session — start here

**Do not implement app code immediately.** The first session is an agent-first product/architecture exercise.

1. Initialize/open a private repository containing this `GUIDE.md`.
2. Ask Codex to read it and inspect the initially empty repo.
3. Ask Codex to draft `PRODUCT.md`, `ARCHITECTURE.md`, and `PLANS.md` with explicit scope, alternatives, risks, and acceptance criteria.
4. Review these artifacts with the human. Revise until the design is approved.
5. Only then ask Codex to bootstrap the minimum runnable game, tooling, and `AGENTS.md`.
6. Make the first baseline observation: what can a fresh agent session accomplish using only repository context?

### Starter prompt for Codex

> Read `GUIDE.md` carefully. This repository is an agent-first harness engineering lab for a cozy, educational virtual cat shelter. Do **not** implement application code yet. Draft `PRODUCT.md`, `ARCHITECTURE.md`, and `PLANS.md` with a narrowly scoped first playable milestone, proposed architecture and trade-offs, data and observability design, end-to-end verification strategy, agent/tool access boundaries, and the first four milestones. Separate factual cat-care guidance from game-simulation assumptions. Surface decisions that require my approval, and wait for design review before scaffolding the app.

## 9. References and inspiration

- OpenAI, **Harness engineering: leveraging Codex in an agent-first world** — <https://openai.com/index/harness-engineering/>
- Thoughtworks / Martin Fowler, **Harness engineering for coding agent users** — <https://martinfowler.com/articles/harness-engineering.html>

---

**Project north star:** A cat shelter worth playing, whose development lifecycle makes the agent's decisions, execution, observations, and failures understandable and verifiable by a human engineer.