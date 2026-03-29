# AGENTS.md

## Project Overview

Excalidraw — an open-source virtual whiteboard for sketching hand-drawn-like diagrams. This is a Yarn monorepo with multiple publishable packages and a hosted web app.

## Tech Stack

- **Languages:** TypeScript, JavaScript
- **Runtime:** Node.js
- **UI Framework:** React 19
- **State Management:** Jotai (via `editor-jotai` / `app-jotai` wrappers)
- **Build Tool:** Vite
- **Testing:** Vitest + @testing-library/react
- **Package Manager:** Yarn (workspaces)
- **Linting:** ESLint + Prettier

## Project Structure

```
packages/common/     — shared constants, utilities, colors, queues, emitters
packages/math/       — 2D vector/geometry math (pure functions, no React)
packages/element/    — element logic: drawing, geometry, binding, alignment
packages/utils/      — export utilities (PNG, SVG, Canvas), file handling
packages/excalidraw/ — main React component library (core UI)
excalidraw-app/      — hosted app at excalidraw.com (PWA, collaboration)
```

## Architecture

Package dependency direction: `common` ← `math` ← `element` ← `excalidraw` ← `excalidraw-app` (and `common` ← `utils`). Never introduce circular dependencies.

- **Action system:** User interactions modeled as actions in `packages/excalidraw/actions/`
- **Canvas rendering:** Custom canvas renderer in `packages/excalidraw/renderer/`
- **Tunnels:** UI composition via tunnel pattern (`context/tunnels.ts`)
- **Component composition:** Sub-components via `Object.assign` (e.g., `Sidebar.Tabs`)

## Key Commands

| Command | Description |
|---|---|
| `yarn start` | Start dev server (Vite, port 5173) |
| `yarn build:packages` | Build all publishable packages |
| `yarn test:app --watch=false` | Run tests (Vitest) |
| `yarn test:typecheck` | TypeScript type checking |
| `yarn test:code` | ESLint linting |
| `yarn test:all` | Full suite (typecheck + code + app) |
| `yarn test:coverage` | Coverage report |

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
- Ensure ESLint passes: `yarn test:code`.
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
