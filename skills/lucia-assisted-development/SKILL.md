---
name: lucia-assisted-development
description: Uses Lucia as a local evidence-gathering companion for software development. Use when Lucia is available and you need repository context, project memory, static QA, test-gap analysis, dependency checks, code review evidence, or session handoff data without making Lucia responsible for the engineering workflow itself.
---

# Lucia-Assisted Development

## Overview

Use Lucia to gather local evidence before, during, and after development while
the applicable engineering skill still defines the workflow. Lucia supplies
repository context, memory, QA signals, and handoff data; it does not replace
specs, tests, review, or human decisions.

## When to Use

- Lucia is installed on the machine and available as `lucia` or through a known
  absolute path.
- Starting work in a repository and you need git state, recent memory, stale
  warnings, or suggested next commands.
- Preparing context for an implementation, review, debugging session, or test
  plan.
- You need local static QA, invariant checks, test-gap analysis, dependency
  analysis, or diff-to-test mapping.
- Closing a work session and you need a reproducible handoff stored in project
  memory.

**When NOT to use:**

- Lucia is not available and installing it is outside the user's request.
- The user asked for a purely conceptual answer with no repository context.
- The task is trivial enough that running local analysis adds no useful
  evidence.
- You are tempted to use Lucia output as a substitute for reading the relevant
  source files yourself.

## Core Process

### 1. Confirm Availability

Check whether Lucia can run:

```bash
lucia check
```

If `lucia` is not in `PATH`, use the absolute path provided by the environment
or user. If the command returns JSON, read `status`, `advertencias`, and
`errores` before proceeding.

Rules:

- If `status` is `ok`, use the result normally.
- If `status` is `warning`, use the result but report uncertainty.
- If `status` is `error`, inspect `errores` and choose another strategy.
- Do not treat stderr warnings as fatal unless the requested tool depends on the
  missing capability.

### 2. Start the Session with Evidence

At the start of repository work, run:

```bash
lucia session-start --dir . --latest 10
```

Use the result to identify:

- current branch and commit;
- pending tracked and untracked changes;
- whether the branch is ahead or behind upstream;
- recent project memory;
- stale warnings;
- suggested next commands.

If Lucia reports pending changes, respect them as user or prior-agent work. Do
not overwrite or revert them unless the user explicitly asks.

### 3. Select the Engineering Workflow

Lucia gives context; another skill still governs the work:

| Situation | Follow this skill |
|---|---|
| Requirements are unclear | `spec-driven-development` |
| A spec needs task breakdown | `planning-and-task-breakdown` |
| Implementing a change | `incremental-implementation` |
| Changing behavior or fixing a bug | `test-driven-development` |
| Test or build failure | `debugging-and-error-recovery` |
| Reviewing changes | `code-review-and-quality` |
| Security-sensitive work | `security-and-hardening` |
| Release readiness | `shipping-and-launch` |

Do not continue with Lucia commands indefinitely. Once you have enough evidence,
return to the active engineering skill and execute its workflow.

### 4. Build a Context Package

When the repository contains source code, scan symbols before asking for
task-specific context:

```bash
lucia scan --dir . --db /tmp/symbols.db
lucia context --task "describe the concrete task here" \
  --symbols-db /tmp/symbols.db --dir . --with-memory 5
```

Use the context package to decide which files to inspect first. Then read the
actual files before editing; symbol hits and memory entries are pointers, not
proof.

For JavaScript and TypeScript repositories, treat scan results as shallow. Lucia
may miss dynamic exports, decorators, complex generics, or generated code.

### 5. Run Focused Quality Checks

Choose the smallest set of Lucia checks that answers the current question:

```bash
lucia qa --dir .
lucia invariants --dir .
lucia test-gap --dir .
lucia test-diff --dir .
lucia deps --dir .
```

Use these checks as evidence:

- `qa` for TODOs, large files, unused imports, and complexity.
- `invariants` for project-specific forbidden or required patterns.
- `test-gap` for public functions without tests or shallow tests.
- `test-diff` for changed functions and suggested tests.
- `deps` for missing or unused dependencies.

Do not run every check by default. Pick checks that match the active task and
the risk involved.

### 6. Use Lucia for Failure Triage

When test output is large, summarize failures before debugging:

```bash
lucia fail-summary --file pytest_output.txt
```

Then follow the `debugging-and-error-recovery` skill:

1. reproduce the failure;
2. localize the root cause;
3. reduce the failing case;
4. fix the cause;
5. add or update a guard test.

Failure summaries classify symptoms; they do not prove root cause.

### 7. Use Lucia for Review Evidence

Before a review, gather local signals:

```bash
lucia test-diff --dir .
lucia deps --dir .
lucia review --base main
```

Then follow `code-review-and-quality` for the actual review. Treat Lucia's LLM
review as one source of findings, not the final verdict. Confirm important
findings against the diff and tests.

### 8. Close the Session

At the end of a meaningful work session, create a handoff:

```bash
lucia sprint-close --project project-name \
  --tests "command that was run" \
  --next "next concrete action" \
  --blocker "none"
```

If no sprint number exists, use `lucia handoff` or add a memory entry directly:

```bash
lucia memory --action add --tag handoff --text "summary of the session"
```

Record:

- what changed;
- what was verified;
- what remains;
- any blockers or risks;
- the next skill or workflow that should run.

## Command Selection Guide

| Need | Lucia command |
|---|---|
| Check local environment | `lucia check` |
| Start a repository session | `lucia session-start --dir .` |
| Extract symbols for context | `lucia scan --dir . --db /tmp/symbols.db` |
| Build task-specific context | `lucia context --task ... --symbols-db ... --dir .` |
| Static QA | `lucia qa --dir .` |
| Project invariants | `lucia invariants --dir .` |
| Test coverage gaps | `lucia test-gap --dir .` |
| Changed code to tests | `lucia test-diff --dir .` |
| Dependency health | `lucia deps --dir .` |
| Pytest failure summary | `lucia fail-summary --file ...` |
| Review a diff | `lucia review --base main` |
| Store technical memory | `lucia memory --action add ...` |
| Close a session | `lucia sprint-close ...` |

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "Lucia already scanned the repo, so I do not need to read files." | Scan results are pointers. You still need to read the code you will modify or review. |
| "I should run every Lucia command before starting." | Unfocused analysis wastes time and context. Run the commands that answer the current risk. |
| "Lucia review found no issues, so the change is approved." | Automated review is evidence, not approval. Follow the review skill and verify tests/builds. |
| "There is a warning, so the result is useless." | Warnings often mean partial capability. Read the warning and decide what uncertainty it creates. |
| "Memory says this pattern is correct." | Memory can be stale. Compare it with current code and recent commits before relying on it. |

## Red Flags

- Running Lucia commands without reading their JSON `status`.
- Treating `warning` output as either success without caveats or automatic
  failure without analysis.
- Using Lucia to avoid asking a clarifying question.
- Editing files based only on symbol search or memory entries.
- Letting Lucia's suggested next command override the active engineering skill.
- Failing to record a handoff after a long or multi-step session.
- Reporting extracted facts without source, confidence, or uncertainty.

## Verification

After using this skill, confirm:

- [ ] Lucia availability was checked or a known working Lucia path was used.
- [ ] Every Lucia JSON response used for decisions had its `status` inspected.
- [ ] Any `warning` result was reported with the relevant uncertainty.
- [ ] The active engineering skill was identified and followed.
- [ ] Source files were read before editing or making review claims.
- [ ] Quality checks were selected based on task risk, not run mechanically.
- [ ] Verification commands, test results, or review evidence were reported.
- [ ] Session memory or handoff was created when the work was substantial.

