# Contributing

## The handoff principle

Every PR description in this repo is a **handoff document for the next task**.

When you open a PR, the description must include:

1. **What this PR does** (1–3 sentences)
2. **What it does NOT do** (explicit out-of-scope list)
3. **Next handoff** — a paragraph describing what the *next* task is, written so the next contributor (which may be you tomorrow, or a stranger) can pick it up cold and ship it without re-reading the whole codebase

This convention exists because we work in phases, asynchronously, with a public 30-day kill-gate. There is no time for tribal knowledge.

## The PR template enforces this

See [`.github/PULL_REQUEST_TEMPLATE.md`](.github/PULL_REQUEST_TEMPLATE.md). PRs that don't fill out the "Next handoff" section will be marked `status-review` blocked.

## Issues

- One issue = one task. If it has subtasks, file them as separate issues and reference with `Tracked by #N`.
- Use the issue templates: `feature`, `spec`, `research`, `bug`, `infra`, `design`.
- All issues get labels for `phase-N-*`, `type-*`, `priority-pN`, and `area-*`.

## Branch convention

`<type>/<issue-number>-<slug>` — e.g. `feature/42-add-af-cart-card-primitive`

## Commits

Conventional Commits — `feat:`, `fix:`, `chore:`, `docs:`, `spec:`, `infra:`, `test:`.

## Tests

If you ship code in `packages/`, ship tests. CI will block merges without ≥80% coverage on new code.

## Code of Conduct

[Contributor Covenant 2.1](https://www.contributor-covenant.org/version/2/1/code_of_conduct/) applies. Be excellent.
