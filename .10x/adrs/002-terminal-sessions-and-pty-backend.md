# ADR-002: Rust-owned terminal sessions behind a `PtyBackend` trait

**Status:** Proposed
**Date:** 2026-10-23
**Feature:** terminal
**Author:** 10x-Team (Architect + Staff Engineer)

## Context
Terminals must be real Windows processes through ConPTY, rendered with xterm.js. Process lifetime must be independent of panel lifetime; the UI must never show "running" for a dead process; restarts must report old sessions as lost. The development workspace cannot run ConPTY.

## Decision
1. A Rust `SessionRegistry` is the only authority on live sessions. SQLite stores metadata and last known state only.
2. All PTY access goes through a `PtyBackend` trait (`spawn`, `read`, `write`, `resize`, `kill`, `try_wait`). Two implementations: a **ConPTY-backed Windows implementation** (via a maintained Rust PTY crate that uses ConPTY on Windows, or direct `windows` crate calls if the crate proves inadequate — decided in M1 with evidence), and a **fake backend** for deterministic lifecycle tests.
3. Output goes into a bounded ring buffer; panels attach/detach freely and replay from the buffer.
4. Close policy per session: `keep_running | ask | terminate`, default `ask`. App exit lists running sessions and requires an explicit choice.
5. Startup reconciliation marks non-terminal DB rows `LostAfterRestart` before the UI loads them.

## Alternatives Considered
| Alternative | Pros | Cons | Why Not |
|---|---|---|---|
| JS-simulated terminal | trivial | not real | Explicitly forbidden |
| Spawn processes with piped stdio (no PTY) | simple | no ANSI/TTY behavior, broken interactive tools | Not a real terminal |
| Tie process to panel lifetime | simple | closing a tab kills work; incompatible with WebShell reattach | Violates spec |
| Hand-written ConPTY FFI from day one | full control | high risk, slow | Only if the crate fails verification |

## Consequences
### Positive
- Lifecycle logic is unit-testable anywhere; only the thin Windows backend needs Windows to verify.
### Negative
- The fake backend can hide ConPTY-specific bugs → a Windows-only integration test on the CI runner is mandatory (ADR-003).
### Risks
- PTY crate chosen may have resize/Unicode quirks → test with non-ASCII output and resize in the Windows integration test.

## Dependencies
Depends on ADR-001. WebShell (M4) will attach to the same registry; it must not introduce a second session model.
