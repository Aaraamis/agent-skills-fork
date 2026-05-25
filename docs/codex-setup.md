# Using agent-skills with Codex

## Overview

Codex can use `agent-skills` as a repository-local workflow library: skills,
personas, references, and command prompts are Markdown files that Codex can read
and follow while working in a project.

Unlike Claude Code, Codex does not use this repository's `.claude/commands/` as
native slash commands. Treat those files as reusable prompts, or install selected
skills into your Codex skills directory when you want first-class skill
activation.

## Setup

### Option 1: Project Instructions

Use `AGENTS.md` as the entry point for Codex project behavior. In the target
project, add a short instruction that points Codex at this repo:

```markdown
# Agent Skills

This project uses agent-skills for development workflows.

When a task matches one of these workflows, read and follow the corresponding
file from `/path/to/agent-skills/skills/<skill-name>/SKILL.md`:

- New feature or significant change: `spec-driven-development`
- Planning or task breakdown: `planning-and-task-breakdown`
- Implementation: `incremental-implementation` and `test-driven-development`
- Bug or failing test: `debugging-and-error-recovery`
- Code review: `code-review-and-quality`
- Security-sensitive work: `security-and-hardening`
- Production release: `shipping-and-launch`

Load supporting references from `/path/to/agent-skills/references/` only when
the active skill calls for them.
```

This keeps `agent-skills` external to the application repo while making the
workflow explicit.

### Option 2: Install Selected Skills for Codex

For skills you want available across projects, copy selected skill directories to
your Codex skills directory:

```bash
mkdir -p ~/.codex/skills
cp -r /path/to/agent-skills/skills/test-driven-development ~/.codex/skills/
cp -r /path/to/agent-skills/skills/code-review-and-quality ~/.codex/skills/
cp -r /path/to/agent-skills/skills/incremental-implementation ~/.codex/skills/
```

Prefer installing a small set of high-value skills instead of all skills. Load
phase-specific skills on demand to keep context focused.

### Option 3: Use Command Files as Prompts

Codex can follow command files directly when you reference them:

```text
Follow the workflow in /path/to/agent-skills/.claude/commands/spec.md.
```

Useful command prompts:

| Workflow | Prompt file |
|---|---|
| Define scope | `.claude/commands/spec.md` |
| Plan tasks | `.claude/commands/plan.md` |
| Build incrementally | `.claude/commands/build.md` |
| Test behavior | `.claude/commands/test.md` |
| Review quality | `.claude/commands/review.md` |
| Simplify code | `.claude/commands/code-simplify.md` |
| Prepare release | `.claude/commands/ship.md` |

## Recommended Configuration

### Always Available

Keep these easy to reference in most application projects:

- `using-agent-skills` — decide which workflow applies.
- `incremental-implementation` — build in small verifiable slices.
- `test-driven-development` — prove behavior with tests.
- `code-review-and-quality` — review before merge.
- `lucia-assisted-development` — gather local context, QA, memory, and handoff
  evidence when Lucia is available.

### Load on Demand

Use these only when the task needs them:

- `spec-driven-development` — new projects, features, or unclear requirements.
- `planning-and-task-breakdown` — convert a spec into implementable tasks.
- `frontend-ui-engineering` — user-facing UI work.
- `api-and-interface-design` — API contracts or public module boundaries.
- `security-and-hardening` — user input, auth, data storage, integrations.
- `performance-optimization` — known performance goals or regressions.
- `shipping-and-launch` — production release readiness.

## Personas

The files in `agents/` are role prompts, not skills. Use them when you want a
specific review perspective:

- `agents/code-reviewer.md` — five-axis code review.
- `agents/security-auditor.md` — vulnerability and threat-model review.
- `agents/test-engineer.md` — test strategy and coverage analysis.

In Codex, ask explicitly for a persona when you want that role:

```text
Review the current diff using the persona in
/path/to/agent-skills/agents/code-reviewer.md.
```

For a release review, Codex can run the `/ship` style fan-out only when you
explicitly ask it to use parallel sub-agents. Otherwise, use the personas one at
a time and synthesize the findings in the main session.

## Working with Lucia

If Lucia is available locally, follow `lucia-assisted-development` to use it as
evidence-gathering support while `agent-skills` defines the workflow:

```bash
lucia session-start --dir . --latest 10
lucia scan --dir . --db /tmp/symbols.db
lucia context --task "describe the implementation task" --symbols-db /tmp/symbols.db --dir . --with-memory 5
lucia qa --dir .
```

Keep the responsibilities separate:

- `agent-skills`: which engineering process to follow.
- `lucia`: repository context, QA, memory, document analysis, and handoff data.

## Example Workflow

For a non-trivial feature in an application repo:

1. Ask Codex to read `using-agent-skills`.
2. Start with `spec-driven-development` if requirements are unclear.
3. Use `planning-and-task-breakdown` to produce small tasks.
4. Use `incremental-implementation` and `test-driven-development` for each task.
5. Use `debugging-and-error-recovery` when tests or builds fail.
6. Use `code-review-and-quality` before merging.
7. Use `shipping-and-launch` before production release.

## Usage Tips

1. Do not load every skill at once. Start with the active workflow and load
   references only when needed.
2. Prefer explicit file references when asking Codex to follow a skill.
3. Treat `.claude/commands/` as prompt templates, not Codex commands.
4. Use personas for review perspectives, not for general implementation.
5. Keep project-specific constraints in the target project's `AGENTS.md`; keep
   reusable workflows in this repo.
