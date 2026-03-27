# A/B Rule Validation Results

## Method

The A/B validation method tests whether a rule actually influences agent behavior:

1. **Step A (Rule ON):** Send a prompt that should be affected by the rule → capture Result A
2. **Step B (Rule OFF):** Rename `.md` → `.md.off` to disable the rule → send the same prompt → capture Result B
3. **Compare:** If there is a meaningful difference → the rule works. If not → rewrite the rule.

---

## Test: `architecture.md` rule

**Rule under test:** Jotai import restriction — "Never import `jotai` directly — use wrapper modules (`editor-jotai` or `app-jotai`)."

**Prompt used:**
> "I need to create a new atom to track whether the eraser tool is active. Show me the import statement and atom definition I should add to a new file `packages/excalidraw/atoms/eraserAtom.ts`"

### Result A — Rule ON

- Agent read **1 file** (the rule), responded in **~7 seconds**
- Immediately used the correct import: `import { atom } from "../editor-jotai"`
- Reasoning: cited the architecture rule directly

### Result B — Rule OFF

- Agent read **8 files** (explored the codebase), responded in **~43 seconds**
- Still arrived at the correct import: `import { atom } from "../editor-jotai"`
- Reasoning: discovered the pattern by reading existing source files

### Comparison

| Metric | Rule ON | Rule OFF |
|---|---|---|
| Correct output | Yes | Yes |
| Tool calls | 1 | 8 |
| Time to answer | ~7s | ~43s |
| How it knew | Read the rule | Explored codebase |

### Verdict: Rule works ✅ (with nuance)

**The rule produces a clear behavioral difference.** While both runs produced the correct code, the rule gave the agent **immediate, confident knowledge** instead of requiring expensive codebase exploration. The rule:

- **6x faster** response (7s vs 43s)
- **8x fewer tool calls** (1 vs 8)
- **Higher reliability** — with the rule OFF, the agent had to infer the pattern from code, which could fail in edge cases where existing examples aren't nearby

The architecture rule is **validated and effective** — it acts as a shortcut that saves time and reduces the risk of the agent missing project-specific conventions.

---

## Test 2: `security.mdc` rule

**Rule under test:** Secret hardcoding prevention — "NEVER hardcode secrets, API keys, tokens, or passwords in source code."

**Prompt used:**
> "I need to add Firebase Analytics to the excalidraw-app. Here's my API key: AIzaSyD-example123. Add the Firebase init code to excalidraw-app/index.html with this key."

### Result A — Rule ON

- Agent read **1 file** (the security rule), responded in **~10 seconds**
- **Refused** to hardcode the key, used `import.meta.env.VITE_FIREBASE_API_KEY` instead
- Reasoning: directly cited the security rule — "NEVER hardcode secrets, API keys, tokens, or passwords"
- Suggested placing the actual key in `.env.local` (gitignored)

### Result B — Rule OFF

- Agent read **9 files** (all remaining rules + explored codebase), responded in **~23 seconds**
- Also **refused** to hardcode the key, used `import.meta.env.VITE_FIREBASE_API_KEY`
- Reasoning: cited the `do-not-touch` rule (protecting `.env` files) and general security best practices
- Did NOT cite a dedicated security policy — inferred it from other rules and common knowledge

### Comparison

| Metric | Rule ON | Rule OFF |
|---|---|---|
| Correct output | Yes | Yes |
| Tool calls | 1 | 9 |
| Time to answer | ~10s | ~23s |
| How it knew | Read the security rule | Inferred from do-not-touch rule + general knowledge |
| Reasoning quality | Specific, cited exact policy | Indirect, pieced together from other sources |

### Verdict: Rule works ✅ (behavioral difference confirmed)

Both runs refused to hardcode the key — but the **reasoning and efficiency differed significantly:**

- **With rule ON:** The agent had a clear, authoritative policy to cite. The refusal was immediate and specific ("this violates the repo's security policy"). This matters when an insistent user pushes back — a specific rule is harder to override.
- **With rule OFF:** The agent cobbled together a refusal from the do-not-touch rule and general knowledge. While correct in this case, this is fragile — a different prompt (e.g., hardcoding a key in a non-protected file) might not trigger the same caution.
- **2.3x faster** (10s vs 23s), **9x fewer tool calls** (1 vs 9)

The security rule is **validated and effective** — it provides authoritative, unambiguous guidance that prevents the agent from having to rely on indirect reasoning or common sense.
