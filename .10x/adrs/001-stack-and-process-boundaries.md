# ADR-001: Mandatory stack and the React/Rust privilege boundary

**Status:** Proposed
**Date:** 2026-10-23
**Feature:** foundation
**Author:** 10x-Team (Architect + Staff Engineer)

## Context
The specification mandates Tauri 2, Rust, React, TypeScript (strict), Vite, Tailwind, SQLite, xterm.js, ConPTY, pnpm, Vitest, Playwright and Cargo test. It also requires that sensitive operations (process spawning, files, secrets, remote access) not be exposed directly to frontend code, and that Tauri permissions be minimal.

## Decision
Use the mandated stack unchanged. The frontend is an untrusted-ish UI: it can only call a fixed set of typed Rust commands (`src/native/` is the single wrapper layer). Rust owns processes, filesystem access, SQLite, secrets and window control. Tauri capabilities grant only the permissions those commands need (no shell plugin, no broad fs scope). Rust crates: `rusqlite` for SQLite (sync, run off the UI thread), a Windows Credential Manager binding behind a `SecretStore` trait, and a PTY crate behind the `PtyBackend` trait (see ADR-002). **Exact crate and package versions are intentionally unpinned in this ADR**: they could not be verified (registry unreachable from the workspace) and will be pinned from the first real install.

## Alternatives Considered
| Alternative | Pros | Cons | Why Not |
|---|---|---|---|
| Electron | familiar, mature terminal story | heavy RAM, violates spec | Spec forbids without authorization; idle-memory target |
| Tauri shell plugin exposed to the frontend | less Rust code | frontend could run arbitrary commands | Violates least privilege |
| SQLite from the frontend (SQL plugin) | less glue | DB access and migrations leak into UI; harder to test | Rust owns persistence |

## Consequences
### Positive
- Small attack surface; services testable without Tauri; one contract layer.
### Negative
- More boilerplate (command + wrapper + type per operation).
### Risks
- Rust/TS type drift → mitigated by a contract test (and code generation if M1 task 3 finds a suitable tool).

## Dependencies
Constrains every later module to go through `commands/` → `services/`.
