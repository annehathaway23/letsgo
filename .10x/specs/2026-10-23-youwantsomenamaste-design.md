# YouWantSomeNamastê? — Design (Milestone 1 detailed, M2–M4 outlined)

Status: **Draft for user approval** · Date: 2026-10-23
Source of requirements: `docs/SPEC.md` (the original specification; it wins on any conflict with this file).

## 1. Constraints that shape the design

| Constraint | Consequence |
|---|---|
| Target is Windows; the development workspace is Linux. | ConPTY, tray, monitors, floating window, Credential Manager and the Tauri window itself cannot be executed here. Code for them can be written here but is **"implemented, not tested"** until run on Windows. |
| The workspace proxy denied `registry.npmjs.org` (HTTP 403, policy denial) on 2026-10-23. | `pnpm install`, `cargo` dependency fetches, Vitest, `tsc`, `cargo test` **cannot run in this workspace**. Nothing written here is compiled or tested here. See ADR-003. |
| Personal-use app, no distribution infrastructure. | No auto-updater, no installer polish in M1–M3. |
| Spec forbids claiming unverified results. | Every status in `status.md` is one of: not started / in progress / implemented-not-tested / tested-and-verified / blocked. "Tested" requires a real log from a real run (GitHub Actions Windows runner or the user's machine). |

Dependency versions are **not yet verified** (registry unreachable here). They are pinned in M1 task 1 from the first successful install on a runner, not from memory.

## 2. Repository layout

```
/
├─ docs/SPEC.md                original specification (read-only reference)
├─ README.md ARCHITECTURE.md IMPLEMENTATION_PLAN.md TESTING.md DECISIONS.md CHANGELOG.md
├─ .10x/                       team state: specs, ADRs, status, handoff
├─ .github/workflows/          CI (windows-latest is the source of truth for tests)
├─ package.json pnpm-lock.yaml vite.config.ts tsconfig.json eslint/prettier config
├─ src/                        React + TypeScript frontend
│  ├─ app/                     shell, routing, providers
│  ├─ design/                  tokens, primitives (Button, Panel, Dialog…)
│  ├─ features/                workspaces, panels, terminal, settings, search …
│  ├─ native/                  typed wrappers over Tauri commands/events (only place that calls invoke)
│  └─ shared/                  types, error model, utilities
└─ src-tauri/
   ├─ Cargo.toml tauri.conf.json capabilities/
   └─ src/
      ├─ main.rs lib.rs        wiring only
      ├─ commands/             thin Tauri command layer (validate → call service → map error)
      ├─ services/             terminal, window, workspace, settings, shells, studies …
      ├─ db/                   connection, migrations/, repositories
      ├─ secrets/              Credential Manager access (trait + Windows impl + in-memory test impl)
      └─ error.rs              AppError (serializable, stable codes)
```

Rule: `commands/` contains no business logic; `services/` has no Tauri types (so it is testable with plain `cargo test`).

## 3. Module map (owner → responsibility)

| Module | Owner | Owns | Depends on | Persistent data |
|---|---|---|---|---|
| App shell / navigation | TS | routes, home, command palette host | design, native | UI state only (memory) |
| Window & monitors | Rust | borderless fullscreen, monitor list, move, emergency exit, tray | tauri window API, Win32 where needed | settings (monitor id, mode) |
| Workspaces & layouts | Rust (persist) + TS (render) | workspace identity, layout tree | db | SQLite |
| Terminal sessions | Rust | process + ConPTY lifecycle, output buffering | OS, session registry | SQLite metadata only; **process state is in-memory in Rust** |
| OpenCode launcher | Rust | executable detection, launch config | terminal service | settings |
| Shells manager | Rust | reusable shell configs, test, start/stop | terminal service, secrets | SQLite (no secrets) |
| WebShell | Rust agent (M4) | remote attach, auth, revocation | terminal service, secrets | SQLite + secrets |
| SkyNetChat | TS UI + Rust persistence | conversations, provider adapters | db, secrets | SQLite |
| Studies | Rust (files) + TS (editor) | `.md` files, link index, conflict detection | filesystem | files authoritative; SQLite index is rebuildable |
| Namastê | TS (in-app), Rust (floating window) | states, animations, companion window | window, settings | settings |
| Settings / shortcuts / notifications | Rust (store) + TS (UI) | typed settings, validation | db | SQLite |
| Credentials | Rust | secret get/set/delete | Windows Credential Manager | OS store only |

**Single sources of identity** (no module may redefine them): `WorkspaceId`, `SessionId`, `SettingsKey` are generated and owned by Rust; the frontend only references them.

## 4. Session lifecycle (authoritative design)

States: `Starting → Running → {Exited | Failed | Interrupted}`; `Disconnected` is a **viewer** state (no panel/browser attached) that does not change process state; `LostAfterRestart` is assigned only at app startup to DB rows that have no live process.

```
            spawn ok            child exits (code known)
Starting ───────────► Running ─────────────────────────► Exited(code)
   │                    │  │ app/OS kills, handle error
   │ spawn error        │  └──────────────────────────► Interrupted
   ▼                    │ read/write error
 Failed(reason)         └──────────────────────────────► Failed(reason)
(startup) every non-terminal row in DB with no live handle ─► LostAfterRestart
```

- The **session registry in Rust is the only source of truth for "running"**. The DB stores metadata (id, workspace, shell, cwd, started_at, last known state). On startup, a reconciliation step marks every non-terminal DB row `LostAfterRestart` before the UI can read them. UI never infers "running" from a stored row.
- **Panel lifecycle ≠ process lifecycle.** Closing a panel applies a per-session policy chosen at creation (`keep_running` | `ask` | `terminate`), default `ask`. Hiding the main window or the tray "Hide" never touches processes. "Exit" shows how many sessions are running and what will happen to them.
- **Output**: Rust reads the PTY on a blocking thread, appends to a bounded ring buffer (configurable, default bounded by bytes), and emits chunked events. A reattaching panel replays the buffer. No unbounded growth.
- **Cancellation/cleanup**: closing a session kills the child, closes the pseudoconsole, joins the reader thread, removes the registry entry; tested with a fake PTY backend (trait) in plain `cargo test`.

PTY backend sits behind a trait `PtyBackend` so lifecycle logic is testable on Linux/CI without ConPTY; the Windows implementation is the one verified on the `windows-latest` runner and on the user's machine (see ADR-002).

## 5. Contracts

Every Tauri command returns `Result<T, AppError>`. `AppError { code: string (stable), message: string (user-safe), details?: object }`; no secrets, no raw OS paths beyond what the user supplied. Types are defined once in Rust and mirrored in `src/shared/types` (generation tool decided in M1 task 3; manual mirror + a contract test is the fallback).

M1 commands/events (names fixed here, shapes finalized in code):

| Operation | Command | Success | Failure codes | Cancel / cleanup |
|---|---|---|---|---|
| Create workspace | `workspace_create` | `Workspace` | `INVALID_PATH`, `DB_ERROR` | transactional |
| Save layout | `layout_save` | `{version}` | `NOT_FOUND`, `DB_ERROR` | last-writer-wins by version, rejects stale |
| Spawn terminal | `terminal_spawn` | `SessionInfo` (state `Starting`) | `SHELL_NOT_FOUND`, `INVALID_CWD`, `SPAWN_FAILED` | on failure no registry entry remains |
| Terminal input | `terminal_write` | `()` | `SESSION_NOT_RUNNING` | — |
| Resize | `terminal_resize` | `()` | `SESSION_NOT_RUNNING` | — |
| Close | `terminal_close` | final state | `NOT_FOUND` | kills child, frees handles |
| List monitors | `monitors_list` | `Monitor[]` | — | — |
| Leave fullscreen | `window_recover` | `()` | — | also bound to emergency shortcut |

Events: `terminal://output/{session_id}` (chunked bytes), `terminal://state/{session_id}` (state + exit code), `monitors://changed`.

## 6. Data ownership

- **SQLite (authoritative):** workspaces, layouts, session metadata, settings, shell configs (no secrets), conversations (M3), activity history. Versioned by `PRAGMA user_version` + ordered migration list; each migration runs in a transaction; startup refuses to open a DB newer than the app knows.
- **Filesystem (authoritative):** Studies notes (`.md`). SQLite holds only a rebuildable index (titles, links, backlinks, mtimes, content hash for conflict detection).
- **Windows Credential Manager:** all secrets (SkyNetChat API keys, GSocket secrets, WebShell credentials). Never in SQLite, settings, logs, exports, backups.

## 7. Security baseline (applies from M1)

- Tauri capabilities minimal and explicit; no frontend shell-exec, no broad fs scope. All process spawning and file access go through Rust commands that validate input.
- Path validation: canonicalize, reject non-existent cwd for terminals, reject traversal outside the Studies vault for notes.
- Remote access (WebShell) **does not exist in M1–M3**; nothing listens on a network port.
- Logs: structured, secret-free by construction (secret values never pass through `Debug`/`Display` of logged types).

## 8. Milestones

### M1 — Foundation, shell, workspaces, terminal (spec phases 1–4)
Done criteria for M1 (each needs a real run log, see ADR-003):
1. Repo builds on `windows-latest` in CI: `pnpm install`, `tsc --noEmit`, lint, `vitest`, `cargo test`, `tauri build --debug` (or `cargo check` if bundling is too slow).
2. Rust unit tests: migrations (fresh + rerun + newer-DB refusal), session state machine, startup reconciliation → `LostAfterRestart`, ring buffer bound, path validation, `PtyBackend` fake lifecycle.
3. Windows-only integration test on the runner: spawn `cmd.exe /c echo` through ConPTY, read output, observe exit code; spawn PowerShell, resize, close, confirm no leaked process.
4. Vitest: layout tree operations (split/resize/close/tabs/serialize/restore), settings validation, command-palette search.
5. **User-run manual checklist on real hardware** (fullscreen recovery, monitor move/fallback, tray, real typing in terminal) — reported back by the user; until then those items stay "implemented, not tested".

### M2 — OpenCode, Shells, Studies (phases 5–6)
### M3 — SkyNetChat, Namastê, floating companion (phases 7–8)
### M4 — WebShell, recovery/backup, performance measurement (phases 9–10)

M2–M4 get their own design pass before implementation. Known up-front risks to resolve then, not now: GSocket real documentation (must be read, not guessed); which SkyNetChat integrations are *officially* available for the user's account; WebShell provider choice (Cloudflare Tunnel + Access is the default candidate); floating-window click-through and fullscreen-detection behavior on Windows.

## 9. Namastê asset note
Namastê will be an original SVG/CSS character (white chibi quadruped fox, violet details, small shirt with an Om symbol), authored in this repo. No external character art is used. Animations: CSS/SVG state classes, paused when the window is hidden or animations are disabled. Voice stays off by default (M3).

## 10. Open items for the user
1. Approve this design (or request changes).
2. Windows prerequisites on your machine: Rust (MSVC toolchain), MSVC Build Tools, Node, pnpm, WebView2.
3. Confirm you accept the verification model in ADR-003 (CI on GitHub's Windows runner + your manual checklist), since tests cannot run in this workspace.
