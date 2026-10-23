# Handoff

**From:** Architect/QA (design phase) · **To:** Engineering Manager → SDE · **Date:** 2026-10-23

## Where we are
Design for Milestone 1 (spec phases 1–4: foundation, shell, workspaces, native terminal) is written and waiting for the user's approval. Read, in order: `docs/SPEC.md` → `.10x/specs/2026-10-23-youwantsomenamaste-design.md` → ADR-001..003.

## Next actions once the design is approved
1. Add `.github/workflows/ci.yml` (Windows runner) and `README.md`/`TESTING.md` stubs.
2. Scaffold Tauri 2 + React + TS strict + Vite + Tailwind + pnpm. **Pin versions from the first successful CI install**, not from memory.
3. Implement, in this order, with tests per the design: DB + migrations → `AppError` → session registry + fake `PtyBackend` → Windows `PtyBackend` + integration test → layout tree (TS) → shell/monitor/recovery commands → design system and home screen.
4. After each push, read the CI result before marking anything verified.

## Rules to keep
- Never mark a feature tested without a real run log.
- Do not commit secrets. Do not add dependencies without need.
- Update `status.md` and `.10x/decisions/*` after each phase.
