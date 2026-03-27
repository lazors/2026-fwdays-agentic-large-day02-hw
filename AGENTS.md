# AGENTS.md

## Project Overview

Excalidraw — an open-source virtual whiteboard for sketching hand-drawn-like diagrams. This is a Yarn monorepo with multiple publishable packages and a hosted web app.

## Agent Guidelines

### Code Changes

- Follow the conventions in `.cursor/rules/conventions.mdc` strictly.
- Respect the architecture and package dependency direction in `.cursor/rules/architecture.mdc`.
- Never modify files listed in `.cursor/rules/do-not-touch.mdc` without explicit approval.
- Follow security rules in `.cursor/rules/security.mdc` — never hardcode secrets.
- Run `yarn test:app --watch=false` after making changes to verify nothing is broken.

### Testing

- Every new feature or bug fix must include tests.
- Follow testing patterns in `.cursor/rules/testing.mdc`.
- Use the existing test helpers — do not create parallel testing infrastructure.

### Pull Requests

- Keep PRs focused on a single concern.
- Ensure ESLint passes: `yarn lint`.
- Ensure types check: `yarn test:typecheck`.
- Write clear commit messages describing *why*, not just *what*.

### Package-Specific Notes

| Package | Key Constraint |
|---|---|
| `packages/math` | Pure functions only, no React or DOM dependencies |
| `packages/common` | No dependencies on other `@excalidraw/*` packages |
| `packages/element` | Depends only on `common` and `math` |
| `packages/excalidraw` | Use direct relative imports internally, not barrel exports |
| `excalidraw-app` | App-specific code only; reusable logic belongs in packages |

### Jotai State

Never import `jotai` directly. Use:
- `editor-jotai` for editor-scoped atoms
- `app-jotai` for app-scoped atoms

### What to Avoid

- Do not add new dependencies without justification.
- Do not refactor unrelated code while fixing a bug.
- Do not lower test coverage thresholds.
- Do not introduce circular dependencies between packages.
