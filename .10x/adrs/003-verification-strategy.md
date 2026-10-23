# ADR-003: Verification via GitHub Actions Windows runner and a user-run checklist

**Status:** Proposed
**Date:** 2026-10-23
**Feature:** testing / delivery
**Author:** 10x-Team (Architect + QA)

## Context
The spec forbids claiming a test passed unless it ran. The development workspace is Linux and its proxy denied the npm registry (HTTP 403 policy denial, 2026-10-23), so dependencies cannot be installed and nothing can be compiled or tested there. The target is Windows. The code lives in a public GitHub repository.

## Decision
- Code is written in the workspace but treated as **implemented, not tested** until a real run exists.
- Tests run in **GitHub Actions on `windows-latest`** (install, `tsc --noEmit`, lint, Vitest, `cargo test`, a build check). A feature may be marked *tested-and-verified* only when the corresponding workflow run is green and its log has been read. Pure-logic Rust tests also run on `ubuntu-latest` as a cheap second signal.
- Behavior that needs a human at a real desktop (fullscreen recovery, multi-monitor, tray, floating window, typing in the terminal) uses a short **manual checklist** the user runs and reports; those items stay *implemented, not tested* until reported.
- Whether the workspace can read workflow results (for example via the `gh` CLI) is **unverified**; if not, the user pastes the failing log or the run URL and the fix loop continues from there.
- If registry access is later enabled for the workspace, local runs are added; CI remains the reference.

## Alternatives Considered
| Alternative | Pros | Cons | Why Not |
|---|---|---|---|
| Write code, never test | fast | violates spec | Unacceptable |
| User runs everything locally | no CI setup | slow feedback, easy to skip | Kept only for UI/hardware items |
| Mock-only tests | runs anywhere | cannot prove ConPTY works | Spec says browser/mocks cannot establish native behavior |

## Consequences
### Positive
- Real Windows verification, including ConPTY, without needing Windows in the workspace.
### Negative
- Slower feedback (push → run → read); first green build may take several iterations.
### Risks
- Runner image differs from the user's machine → the manual checklist covers the gap.

## Dependencies
Requires the user's approval to add `.github/workflows/` to the public repository.
