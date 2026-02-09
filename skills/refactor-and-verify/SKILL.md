---
name: refactor-and-verify
description: A comprehensive workflow to identify dead code, perform safe deletions, and execute full-spectrum codebase verification to ensure production readiness..
---

## Usage

`/refactor-verify [mode]`
Modes: `full` (default), `quick` (build/types), `pre-commit`, `pre-pr` (includes security)

## 1. Discovery Phase (Static Analysis)

Identify unused assets and code patterns without executing them.

- **Dependency Audit**: Run `depcheck` to find unused `package.json` dependencies.
- **Export Audit**: Run `knip` and `ts-prune` to find unused files, functions, and types.
- **Log Audit**: Scan for `console.log`, `todo`, or `fixme` markers in source files.
- **Security Audit**: (For `pre-pr` mode) Scan for hardcoded secrets or sensitive credentials.

## 2. Transformation Phase (Safe Refactoring)

Execute deletions following a strict "Verify-Apply-Verify" protocol.

- **Categorization**:
  - **SAFE**: Unused test files, local utilities with no internal references.
  - **CAUTION**: UI Components, API routes, Hooks.
  - **DANGER**: Config files (`.env.example`, `tsconfig`), Entry points, Middleware.
- **Atomic Deletion Loop**:
  1. **Pre-check**: Run the test suite. If it fails, abort.
  2. **Apply**: Delete a single categorized item.
  3. **Verify**: Re-run tests.
  4. **Rollback**: If tests fail, immediately revert the change via `git checkout`.
- **Reporting**: Generate a log in `.reports/dead-code-analysis.md`.

## 3. Integrity Phase (Comprehensive Verification)

Validate the final state of the codebase in the following order:

1. **Build Integrity**: Ensure the project compiles/builds without errors.
2. **Type Integrity**: Run `tsc` or equivalent type-checkers.
3. **Stylistic Integrity**: Run `eslint` or `prettier` to ensure linting compliance.
4. **Functional Integrity**: Run all unit, integration, and E2E tests.
5. **Git Integrity**: Summarize uncommitted changes and modified files.

## Execution Logic (System Instructions)

- **Order Matters**: Always perform Discovery before Transformation, and Integrity after any change.
- **Zero Tolerance**: In `pre-pr` or `full` mode, any Build or Type error must result in a `VERIFICATION: FAIL` status.
- **Report Generation**: At the end of the process, output the following concise summary.

## Output Format: Verification Report

```markdown
## VERIFICATION: [PASS/FAIL]

### 1. Analysis & Refactoring

- Dead Code Removed: [Count/None]
- Unused Dependencies: [Count/None]
- Reports: `.reports/dead-code-analysis.md`

### 2. Quality Metrics

- Build: [OK/FAIL]
- Types: [OK/X errors]
- Lint: [OK/X issues]
- Tests: [X/Y passed, Z% coverage]
- Logs: [OK/X found]

### 3. Readiness

- **Ready for PR**: [YES/NO]
- **Recommended Action**: [e.g., Fix linting errors before pushing]
```
