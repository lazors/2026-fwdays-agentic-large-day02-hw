# /review-code

Review the currently selected code or the files changed in the current branch.

## Steps

1. **Identify scope:** If code is selected, review that. Otherwise, run `git diff --name-only master...HEAD` to find changed files.

2. **For each file, check:**
   - **Correctness:** Logic errors, off-by-one, null/undefined risks
   - **TypeScript:** No `any` types, proper `import type` usage, strict mode compliance
   - **Security:** No hardcoded secrets, no `eval`, no `dangerouslySetInnerHTML` without sanitization
   - **Conventions:** Naming (PascalCase components, camelCase functions, UPPER_SNAKE constants), import ordering
   - **Architecture:** Respects package dependency direction, no circular imports, Jotai via wrappers only
   - **Tests:** New features/fixes should have corresponding tests
   - **Do-not-touch:** Flag if any protected files were modified

3. **Output format:**

   ```text
   ## <filename>
   - 🔴 Critical: <issue>
   - 🟡 Warning: <issue>
   - 🟢 Good: <positive observation>
   ```

4. **Summary:** End with an overall verdict (✅ Ready to merge / ⚠️ Needs changes / 🚫 Major issues).

## Rules

- Be specific — reference line numbers.
- Don't nitpick formatting if ESLint handles it.
- Focus on things a linter can't catch: logic, architecture, security.
