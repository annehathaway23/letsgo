# YouWantSomeNamastê? — Complete Project Specification

## 1. Role and objective

Act as a senior Windows desktop application engineer, software architect, UI/UX designer, and QA engineer.

Your task is to design, implement, test, and document **YouWantSomeNamastê?**, a personal Windows productivity environment that integrates workspaces, real terminals, OpenCode, AI conversations, notes, remote terminal access, and an interactive fox companion named Namastê.

This is a real application, not a mockup, prototype-only interface, or collection of disconnected screens.

The final result must be functional, lightweight, responsive, visually polished, secure, maintainable, and suitable for being open for many hours every day.

### Core principles

- Functionality takes priority over decorative complexity.
- The application must feel like one coherent environment.
- Every interactive control must perform its stated action.
- Real processes and real data must be used wherever required.
- Never simulate a feature and present it as implemented.
- Prefer simple, robust solutions over unnecessary abstractions.
- Avoid excessive dependencies and background processes.
- Preserve existing functionality when modifying the project.
- Work incrementally and validate changes before proceeding.
- Never claim a test passed unless it was actually executed successfully.

This application is for personal use. Do not prioritize public distribution infrastructure, automatic update services, or a commercial multi-user deployment model.

## 2. Product identity and visual design

The application should have a distinctive, minimal, modern identity.

The visual experience should feel carefully designed rather than like a generic administration dashboard.

### Design requirements

- Dark-first interface.
- Predominantly black and dark neutral surfaces.
- Violet and purple accents used consistently.
- Clear visual hierarchy and readable typography.
- Restrained borders, shadows, and separators.
- Smooth, subtle transitions.
- Consistent spacing, sizing, and component behavior.
- High information density without visual clutter.
- Useful empty states, loading states, and error states.
- Keyboard-first interaction wherever practical.

Avoid excessive gradients, oversized cards, unnecessary labels, constant animations, and decorative elements that consume space without improving usability.

Use a shared design system with reusable components, color tokens, spacing tokens, typography rules, and consistent interaction states.

The application must remain comfortable to use at 1920×1080 and adapt to different window sizes and monitor configurations.

## 3. Namastê — the fox companion

Namastê is a central part of the application's identity.

She is a **white chibi fox that walks on four legs**, with simple eyes and a simple mouth. She must remain predominantly animal-like, not a human character or an anime girl.

### Appearance

- White fur.
- Violet decorative details.
- A small shirt adapted to her quadrupedal body, featuring the requested symbol associated with Om/Aum.
- Simple, expressive eyes and mouth.
- A friendly, recognizable silhouette.
- Animations that preserve her animal anatomy.

### Behaviors and expressions

Support appropriate states such as:

- Idle.
- Sleeping.
- Curious.
- Playful.
- Happy.
- Thinking.
- Attentive.
- Working.
- Reacting to completed operations.

Animations must be lightweight and must not interfere with work.

A soft, high-pitched feminine voice may be offered as an optional feature. Voice playback must be configurable and disabled or quiet by default.

### Optional introduction

On the first launch, optionally show a short introduction:

1. Namastê plays.
2. She notices the user.
3. She runs toward the viewer.
4. She says: “Boas vindas chefe 23, a Namastê já está preparada para trabalhar!”
5. The screen darkens.
6. The main workspace appears.

The introduction must be skippable and must never block normal access to the application.

### Floating desktop companion

Allow Namastê to optionally appear as a floating Windows desktop companion.

Requirements:

- Drag and reposition her.
- Remember her position.
- Support configurable size.
- Hide and show her using a configurable shortcut.
- Avoid stealing keyboard focus.
- Do not block clicks on underlying applications when in a noninteractive state.
- Support multiple monitors.
- Handle monitor disconnection gracefully.
- Optionally hide during games or fullscreen applications.
- Provide Companion, Quiet, and Focus modes.
- Allow the user to disable floating mode entirely.
- Never punish the user or repeatedly interrupt them for ignoring the mascot.

The floating companion must be implemented using appropriate native window behavior rather than a fake in-app overlay.

## 4. Main window and monitor management

The main application should use a borderless fullscreen presentation without traditional window controls.

However, it must never trap the user inside the application.

Required capabilities:

- Detect connected monitors.
- Display available monitors in settings.
- Allow the user to choose the target monitor.
- Start on the primary monitor or the last-used monitor.
- Provide a configurable shortcut to move the application between monitors.
- Fall back to the primary monitor if the selected monitor disconnects.
- Provide an emergency shortcut or command to leave fullscreen and recover normal window control.
- Preserve access to Windows, Alt+Tab, the taskbar when appropriate, and other applications.
- Handle display-resolution and scaling changes.
- Support a recoverable windowed mode for troubleshooting.

Include a system tray icon with options to show, hide, and exit the application.

Hiding the main window must not accidentally terminate running terminal processes. The user must be able to explicitly exit the application and understand what will happen to its managed processes.

Starting the application with Windows may be an optional setting.

## 5. Home screen and navigation

The home screen should provide fast access to:

- New Workspace.
- Last Workspace.
- All Workspaces.
- Shells.
- Studies.
- SkyNetChat.
- Settings.
- Documentation.

Avoid making the home screen a large, static dashboard.

Show relevant recent workspaces, useful shortcuts, and active operations only when they provide practical value.

The user should be able to navigate between the application's major features without repeatedly returning to the home screen.

## 6. Workspaces and panel management

Workspaces are the main organizational unit.

Each workspace should support:

- Name.
- Description.
- Icon or visual identifier.
- Root directory.
- Favorite status.
- Saved layout.
- Associated notes.
- Associated conversations.
- Terminal sessions.
- OpenCode sessions or launch configuration.
- Optional Shells and remote connections.

### Workspace operations

Implement:

- Create.
- Rename.
- Duplicate.
- Delete with confirmation.
- Search.
- Favorite and unfavorite.
- Open and close.
- Save and restore layouts.
- Select a root directory.
- Choose a workspace template.

Suggested templates:

- Development.
- Studies.
- Terminal.
- AI Research.
- Remote Work.
- Custom.

### Panel system

Within a workspace, allow panels to be:

- Resized using visible dividers or borders.
- Split horizontally or vertically.
- Reordered.
- Moved between supported layouts.
- Maximized.
- Minimized or collapsed.
- Closed.
- Grouped into tabs.
- Restored from saved layouts.

Support terminal, OpenCode, SkyNetChat, editor, Studies, and WebShell panels where appropriate.

Panel layout changes must be persisted and restored reliably.

Do not implement panel resizing as a visual-only effect: actual panel dimensions and content layout must update correctly.

## 7. Real Windows terminals

Terminals must execute actual Windows processes.

Use **xterm.js for terminal rendering and interaction, with ConPTY and an appropriate Rust-side process-management layer** for native Windows terminal integration.

Support PowerShell and Command Prompt. Additional shells may be supported when installed and configured.

Required capabilities:

- Real-time terminal input and output.
- ANSI escape sequences and terminal colors.
- Keyboard input.
- Copy and paste.
- Text selection.
- Scrollback.
- Terminal resizing.
- Working-directory selection.
- Process exit detection.
- Clear terminal.
- Create and close sessions.
- Configurable initial shell and arguments.
- Bounded scrollback buffers.
- Correct handling of Unicode and special characters.
- Proper cleanup of native resources.
- Clear errors when a shell cannot start.

Separate the lifecycle of a process from the lifecycle of its visual panel.

Closing a panel must follow an explicit policy for its process. Never silently terminate unrelated processes.

The interface must not use a JavaScript simulation in place of a real terminal.

## 8. OpenCode integration

Provide a way to launch OpenCode inside a workspace terminal.

Requirements:

- Detect an available OpenCode executable or command.
- Allow manual configuration of the executable path.
- Allow selection of the working directory.
- Start the command in the selected workspace.
- Show real output in the terminal.
- Handle missing installations and launch failures.
- Allow opening OpenCode in an external terminal when desired.
- Preserve the distinction between the OpenCode application and the terminal process hosting it.

Do not assume fixed installation paths.

Do not modify OpenCode's global configuration, installed executable, plugins, skills, or project files without explicit authorization.

Do not claim deep integration with OpenCode's internal APIs unless such integration has actually been implemented and verified.

## 9. Shells manager

Create a Shells section for managing reusable local and remote shell configurations.

Each entry may include:

- Name.
- Description.
- Connection type.
- Working directory.
- Shell or command to launch.
- Documented arguments.
- Local or remote classification.
- Optional GSocket configuration.
- Open inside the application or externally.
- Favorite status.

Implement:

- Create.
- Edit.
- Duplicate.
- Remove.
- Search.
- Favorite.
- Test configuration.
- Start and stop sessions.
- Display connection and process status.

GSocket support must follow its actual documentation. Do not invent command-line arguments, protocols, or connection behavior.

Treat connection secrets as sensitive information. Do not expose them in ordinary logs, terminal metadata, or exported configuration files.

## 10. WebShell — secure access from anywhere

WebShell must allow the user to access managed terminal sessions through a browser over the internet, including when the user is away from the Windows computer.

It must support:

- Accessing eligible existing sessions.
- Starting new authorized sessions remotely.
- Viewing terminal output.
- Sending terminal input.
- Opening multiple sessions.
- Disconnecting and reconnecting.
- Clearly displaying connection and session status.
- Revoking access and active remote sessions.

Disconnecting a browser must not automatically terminate the underlying process.

After Windows restarts, sessions that no longer exist must be reported as lost or terminated. Never pretend that a previous process survived a reboot.

### Architecture and security

Prefer a secure outbound tunnel architecture, such as Cloudflare Tunnel combined with Cloudflare Access, or an equivalent documented solution.

Do not expose a raw terminal port directly to the public internet.

The design must include:

- Strong authentication.
- Passkeys and/or MFA where supported by the chosen provider.
- Authorization checks for every session and operation.
- TLS-protected connections.
- Secure WebSocket handling.
- Origin validation.
- Session expiration and revocation.
- Rate limiting and protection against repeated authentication attempts.
- Device/session management.
- Clear handling of expired credentials.
- Minimal local-agent privileges.
- Safe storage of secrets.
- Audit logs that exclude passwords, tokens, and terminal secrets.
- Protection against unauthorized access to sessions.
- Explicit consent before enabling remote access.
- Remote access disabled by default until configured and tested.

The local agent should manage only sessions created by or explicitly registered with the application.

Do not run the application or remote agent as administrator by default.

Do not create hidden persistence, stealth access, or undocumented remote-control mechanisms.

Document the actual external dependencies and setup requirements. Do not pretend that remote access works before authentication, tunnel configuration, and an end-to-end test have succeeded.

## 11. SkyNetChat — integrated AI conversations

SkyNetChat is the integrated AI conversation environment.

Support two configurable integration modes:

1. An official API integration.
2. An officially supported or documented integration compatible with the user's existing premium account, when such an integration is actually available.

The user must be able to select the configured mode in settings.

Do not assume that an AI subscription includes API credits or API access.

Do not invent undocumented endpoints, impersonate unsupported clients, or claim that an integration is officially supported without verifying that claim.

If a requested integration is not technically or contractually available, explain the limitation and implement the available alternative without falsely reporting success.

### Chat capabilities

Implement:

- Create conversations.
- Rename conversations.
- Delete conversations with confirmation.
- Persistent history.
- Search conversations.
- Associate conversations with workspaces.
- Markdown rendering.
- Code blocks and copy actions.
- Streaming when supported.
- Cancel generation when supported.
- Model selection when supported.
- Connection testing.
- Error handling.
- Retry and reconnection behavior.
- Clear indicators for loading, generating, completed, failed, and cancelled responses.

Use a common adapter interface so providers can share the same UI without pretending their capabilities are identical.

Store conversation data locally unless the selected integration requires otherwise.

### Namastê integration

Namastê must appear naturally in the SkyNetChat interface:

- Header.
- Empty conversation state.
- Loading state.
- Generation state.
- Completion state.
- Relevant error states.

She may show a subtle thinking indicator while a response is being generated and react briefly when the response completes.

Animations must be collapsible or disableable. The chat must remain usable with animations turned off.

Use the same shared design system as the rest of the application.

## 12. Studies — Markdown knowledge vault

Studies is a lightweight local knowledge-management environment compatible with Markdown files and Obsidian-style vaults.

Store notes as actual `.md` files in a user-selected directory.

Implement:

- Create notes.
- Edit notes.
- Rename notes.
- Delete notes with confirmation.
- Create and organize folders.
- Search by title and content.
- Pin or favorite notes.
- Markdown preview.
- Markdown editing.
- Internal links using `[[note]]` syntax.
- Backlinks.
- Association with workspaces.
- Opening notes in workspace panels.
- Autosave.
- Unsaved-change indicators.
- Detection of external file modifications.
- Conflict resolution without silent data loss.

Do not silently overwrite changes made by an external editor.

Provide useful feedback when a file cannot be read, saved, renamed, or deleted.

Future enhancements may include a graph view, templates, flashcards, and AI-assisted note organization, but only after the core editing and persistence behavior is reliable.

## 13. Global search and Command Palette

Implement global search across:

- Workspaces.
- Sessions.
- Notes.
- Conversations.
- Shell configurations.
- Relevant settings.

Provide a Command Palette, preferably accessible through `Ctrl+K`, with searchable commands and keyboard navigation.

Support configurable shortcuts for common actions.

Shortcuts must not conflict unnecessarily with standard Windows or terminal behavior. Allow users to change shortcuts that conflict with their environment.

## 14. Activities, processes, and notifications

Provide a discreet activity area showing relevant ongoing operations.

Examples:

- Active terminal processes.
- AI generation.
- Remote connection status.
- File-saving operations.
- Failed or interrupted tasks.

Where safe, allow the user to open the relevant panel, cancel an operation, reconnect, or inspect an error.

Do not imply that an operation is running when the underlying process has stopped.

Avoid excessive notifications. Group repeated events and allow notification preferences to be configured.

## 15. Additional personal productivity features

Include, where practical:

- Exportable application profiles and settings.
- Saved workspace templates.
- A focus mode.
- Quick access to recent work.
- Optional performance monitoring.
- Activity history.
- Configurable notification behavior.
- Recoverable workspace layouts.
- Local diagnostic logs.
- A credential vault integration.

Use Windows Credential Manager or an appropriate secure OS-backed mechanism for secrets.

Secrets must not be stored as plaintext in ordinary settings files, SQLite records, debug logs, or exported profiles.

Backups and exports should exclude secrets by default.

## 16. Mandatory technology stack

Use this stack as the required project baseline. Do not replace a primary technology without identifying a concrete technical incompatibility and explaining the alternative before making the change.

### Required languages and technologies

- **Rust:** native backend, Windows integration, process management, ConPTY, monitor detection, local services, and security-sensitive operations.
- **TypeScript:** frontend application code, with strict type checking enabled.
- **HTML5:** frontend component structure.
- **CSS3:** layout, visual styling, animations, and responsive behavior.
- **SQL:** relational data access and queries.
- **Markdown:** Studies notes and project documentation.
- **Tauri 2:** desktop application framework.
- **React:** UI components and application interface.
- **Vite:** frontend development and build tooling.
- **Node.js:** frontend development tooling.
- **pnpm:** package manager, unless a documented compatibility issue requires a different choice.
- **Tailwind CSS:** utility styling, complemented by custom CSS where necessary.
- **SQLite:** local structured persistence.
- **xterm.js:** terminal rendering and interaction.
- **ConPTY:** actual Windows terminal integration.
- **Vitest:** TypeScript unit and component tests.
- **Playwright:** end-to-end UI tests where compatible with the chosen environment.
- **Cargo test:** Rust backend tests.

Verify compatible versions before installing dependencies. Prefer stable, maintained releases and avoid unnecessary packages.

### Architectural responsibilities

React and TypeScript handle:

- Navigation.
- Workspace interface.
- Panel layout.
- Chat interface.
- Studies editor.
- Namastê's in-app presentation.
- User-facing settings and state.

Rust handles:

- Native Windows operations.
- Terminal processes and ConPTY.
- Window and monitor management.
- Secure local services.
- File-system operations that require native access.
- Persistent backend operations.
- Remote-access agent functionality.
- Sensitive operations that must not be exposed directly to frontend code.

Tauri commands and events must have typed, explicit contracts.

Use minimal Tauri capabilities and permissions. Do not grant unrestricted shell execution or broad file-system access to the frontend without a demonstrated requirement and appropriate safeguards.

SQLite stores structured application metadata. Markdown notes remain real Markdown files.

Use Windows Credential Manager or an equivalent OS-backed secret store.

### Code quality requirements

- Enable TypeScript strict mode.
- Avoid `any` unless justified and contained.
- Use consistent formatting and linting.
- Separate UI, application logic, persistence, and native services.
- Keep interfaces and shared types explicit.
- Handle errors consistently.
- Avoid circular dependencies.
- Avoid oversized components and monolithic files.
- Keep configuration centralized and documented.
- Add tests for critical business logic.
- Verify dependency compatibility and licensing where relevant.
- Ensure builds can be reproduced from documented project instructions.

Do not use Electron as a replacement for Tauri without prior authorization.

Do not substitute a simulated JavaScript terminal for Rust-managed native processes and ConPTY.

## 17. Architecture, module contracts, and integration

Define a coherent architecture before implementing the full application.

Each major module must have documented responsibilities, inputs, outputs, dependencies, persistent data, error states, and public interfaces.

At minimum, define boundaries for:

- Application shell and navigation.
- Window and monitor management.
- Workspace and layout management.
- Terminal process management.
- OpenCode launching.
- Shell configuration.
- WebShell and authentication.
- SkyNetChat provider adapters.
- Studies and Markdown files.
- SQLite persistence.
- Credential storage.
- Namastê animation and interaction.
- Settings, shortcuts, and notifications.

### Module contracts

For every important interaction between modules, specify:

- Which module owns the operation.
- What data is exchanged.
- Which events or commands are available.
- How success is reported.
- How failures are represented.
- What happens when the operation is cancelled.
- How resource cleanup occurs.
- Which tests verify the integration.

Use typed interfaces and explicit error models instead of hidden dependencies or informal assumptions.

Separate UI state from durable application state and native process state.

Do not let individual modules implement conflicting versions of workspace identity, session status, authentication state, or settings.

### Process and session lifecycle

Define consistent states for managed sessions, including applicable states such as:

- Starting.
- Running.
- Disconnected.
- Exited.
- Failed.
- Interrupted.
- Lost after restart.

The UI must reflect the actual backend state.

Never report a process as running merely because its previous panel or metadata remains in the database.

## 18. Data integrity, persistence, and recovery

SQLite and file-based storage must remain reliable through application crashes, Windows restarts, and future changes to the data model.

### Database requirements

- Define a versioned database schema.
- Implement ordered database migrations.
- Track the current schema version.
- Make migrations safe to rerun or detect that they have already completed.
- Use transactions for related changes.
- Apply appropriate constraints and indexes.
- Handle database locks and failures.
- Avoid blocking the UI during longer operations.
- Avoid storing unnecessary copies of files or sensitive information.
- Document which data is authoritative in SQLite and which data belongs to the file system.

Never change the database schema without an accompanying migration.

### Recovery requirements

- Recover cleanly after unexpected shutdowns.
- Detect incomplete or interrupted operations.
- Preserve saved workspace layouts.
- Restore persistent settings and conversation history.
- Distinguish recoverable metadata from processes that no longer exist.
- Report unsaved or conflicting file changes where detectable.
- Avoid silent overwrites.
- Provide clear error messages when recovery is incomplete.

Implement a configurable local backup mechanism for important application metadata and settings.

Backups must be validated where practical, and the application should provide a documented recovery procedure.

Do not include credentials or other secrets in backups by default.

Backups must not be presented as successful until the destination and resulting data have been verified.

## 19. Measurable performance and resource management

The application must be lightweight enough to remain open for long periods while the user works.

Do not treat performance as a vague visual impression. Establish a baseline and measure the real application.

Use these initial targets as engineering goals, then validate them on the actual development machine:

- Cold startup to a usable main interface: target at or below 3 seconds.
- Warm startup to a usable interface: target at or below 1.5 seconds.
- Idle CPU usage: target below 2% average over a representative five-minute measurement, excluding active background work and external processes.
- Idle memory usage: target below approximately 300 MB for the main application process and its directly attributable idle UI components, measured and reported consistently.
- Ordinary navigation and panel interactions: target at or below 100 ms for perceived response, excluding necessary external operations.
- No unbounded growth in terminal scrollback, animation resources, event listeners, or retained conversation content.

These are targets, not permission to falsify results or sacrifice correctness.

If a target cannot be met, record the measurement, explain the cause, and prioritize meaningful improvements.

### Performance requirements

- Lazy-load expensive or rarely used modules where appropriate.
- Limit terminal output buffers.
- Avoid unnecessary React re-renders.
- Dispose of event listeners and native resources.
- Pause or reduce idle animations.
- Avoid polling when event-driven updates are practical.
- Prevent background loops from consuming CPU unnecessarily.
- Keep file operations and network requests from blocking the UI.
- Test with multiple workspaces, panels, terminal sessions, and conversations.
- Distinguish the application's resource usage from that of terminal processes and external AI tools.

Do not optimize solely for synthetic benchmarks. Measure realistic workflows.

## 20. Settings and configuration

Provide a dedicated settings interface covering relevant options, including:

- Theme and appearance.
- Monitor selection.
- Startup behavior.
- Fullscreen and window recovery.
- Namastê appearance and behavior.
- Floating companion mode.
- Voice and animation preferences.
- Default shell.
- OpenCode executable path.
- Default workspace directory.
- Keyboard shortcuts.
- Notification preferences.
- SkyNetChat provider and connection configuration.
- WebShell security and connection settings.
- Studies vault directory.
- Backup behavior.
- Performance preferences.
- Diagnostic logging.

Validate user input and show actionable errors.

Persist settings reliably and apply changes at the correct time.

Settings exports must exclude secrets by default.

## 21. Testing and verification requirements

Testing is mandatory throughout development, not a final optional step.

### Rust tests

Test critical backend logic, including:

- Process lifecycle management.
- Session-state transitions.
- Database operations and migrations.
- Path validation.
- Error handling.
- Configuration validation.
- Relevant authentication and authorization logic.

### TypeScript tests

Use Vitest for important frontend logic and components, including:

- Workspace state.
- Panel layout.
- Settings validation.
- Chat state transitions.
- Search behavior.
- Error and loading states.

### End-to-end tests

Use Playwright where compatible to verify important user flows.

Examples:

- Launch the application.
- Create and reopen a workspace.
- Resize and restore panels.
- Open a real terminal.
- Run a harmless test command.
- Detect process termination.
- Create and edit a Markdown note.
- Save and reopen a conversation.
- Change settings and confirm persistence.
- Handle a disconnected or failed service.
- Verify fullscreen recovery.

Test native Windows functionality on Windows. Browser-only tests cannot establish that ConPTY, native windows, monitor handling, or process management works correctly.

### WebShell tests

Before enabling remote access for normal use, verify:

- Authentication is required.
- Unauthorized users cannot access sessions.
- Expired and revoked access is rejected.
- A disconnected browser can reconnect when authorized.
- Unauthorized requests cannot attach to another session.
- Session termination is reported correctly.
- Logs do not reveal secrets.

Use controlled test environments and harmless commands.

### Completion rule

A feature is complete only when:

1. Its implementation exists.
2. Its success and failure states are handled.
3. Relevant tests have been executed.
4. The actual result has been checked.
5. Known limitations are documented.

If a test cannot be run, state why and do not mark it as passed.

## 22. Incremental implementation plan

Do not attempt to build the entire application in one enormous change.

Work in small, verifiable phases.

### Phase 1 — Repository inspection and technical foundation

- Inspect the repository and existing files.
- Identify the operating environment and available tools.
- Confirm the mandatory stack and compatible versions.
- Establish the directory structure and architectural boundaries.
- Create a minimal working Tauri, React, TypeScript, and Rust application.
- Add linting, formatting, and initial tests.
- Document how to run and test the project.

### Phase 2 — Visual foundation and application shell

- Implement the visual system.
- Create the main navigation and home screen.
- Implement fullscreen and window recovery.
- Add monitor detection and selection.
- Create reusable components and settings foundations.

### Phase 3 — Workspace and panels

- Implement workspace persistence.
- Add panel splitting, resizing, tabs, and saved layouts.
- Implement workspace restoration.
- Add global search and the Command Palette.

### Phase 4 — Native terminal foundation

- Integrate xterm.js and ConPTY.
- Implement process management and terminal lifecycle.
- Verify real input, output, resizing, and termination.
- Test multiple sessions and resource cleanup.

### Phase 5 — OpenCode and Shells

- Add OpenCode launching.
- Implement Shells management.
- Add configuration validation and secure secret handling.
- Verify local session workflows.

### Phase 6 — Studies

- Implement Markdown file operations.
- Add autosave, preview, internal links, and backlinks.
- Implement external modification detection and conflict handling.

### Phase 7 — SkyNetChat

- Build the common provider adapter interface.
- Implement supported integrations.
- Add history, streaming when available, cancellation when available, and error handling.
- Integrate Namastê into the chat UI.

### Phase 8 — Namastê and desktop companion

- Implement in-app expressions and animations.
- Add optional voice behavior.
- Implement the floating companion and native window interactions.
- Verify multi-monitor behavior and focus handling.

### Phase 9 — WebShell

- Implement the remote-access architecture.
- Configure the chosen authentication and tunnel mechanism.
- Add session authorization and revocation.
- Run security and end-to-end tests before enabling normal use.

### Phase 10 — Recovery, optimization, and final verification

- Verify database migrations and recovery.
- Test backups and restoration.
- Measure performance and fix significant bottlenecks.
- Run available test suites.
- Review security boundaries and error handling.
- Document how to run, configure, troubleshoot, and maintain the personal application.

A phase may be adjusted when a verified dependency requires it, but do not skip essential prerequisites simply to make visible progress.

## 23. AI development workflow and token efficiency

The development agent must work efficiently within the available context and usage limits.

Do not waste tokens repeatedly rewriting the full specification, re-reading unchanged files, or producing lengthy progress reports that do not advance implementation.

### Before starting work

1. Inspect the repository and current project state.
2. Identify existing functionality and unfinished work.
3. Read relevant project instructions and documentation.
4. Check the current plan and test status.
5. Identify the smallest useful next task.
6. Avoid replacing working code without a demonstrated reason.

### Implementation rules

- Work on one coherent task at a time.
- Prefer focused changes over broad rewrites.
- Inspect the relevant code before editing it.
- Reuse existing patterns when they are sound.
- Do not create duplicate modules or conflicting implementations.
- Do not replace functional components with placeholders.
- Do not remove existing functionality without authorization.
- Do not introduce unnecessary dependencies.
- Do not silently change the required technology stack.
- Do not claim that an external service is connected without testing it.
- Do not fabricate command output, test results, or screenshots.
- Do not execute destructive commands without explicit authorization.
- Do not expose credentials or secrets in logs, summaries, or source control.

### Persistent project tracking

Maintain concise project documentation in the repository, including:

- `README.md`: purpose, prerequisites, setup, run, and test instructions.
- `ARCHITECTURE.md`: module boundaries, data flow, and important design decisions.
- `IMPLEMENTATION_PLAN.md`: phases and task status.
- `TESTING.md`: test commands, verified results, and known gaps.
- `DECISIONS.md`: significant technical decisions and reasons.
- `CHANGELOG.md`: meaningful completed changes and behavior changes.

Use existing project files if equivalent documentation already exists; do not create duplicates unnecessarily.

The implementation plan should distinguish:

- Not started.
- In progress.
- Implemented but not tested.
- Tested and verified.
- Blocked.

Update the relevant records after meaningful work.

### Working with limited context or free-tier usage

- Keep summaries concise and factual.
- Record the exact files changed and tests run.
- Record the next actionable task and unresolved blockers.
- Avoid repeating the entire project specification in every progress update.
- Use the project documents as the durable source of implementation state.
- Reinspect relevant files when needed instead of relying on assumptions.
- Prioritize foundational features and high-risk integrations before cosmetic refinements.
- If the available context or usage limit is nearly exhausted, leave the repository in a coherent state and provide a concise handoff for the next session.

### Definition of done for each task

Before marking a task complete:

1. Inspect the changes.
2. Run relevant tests and checks.
3. Resolve failures caused by the changes.
4. Verify the intended behavior where possible.
5. Update documentation and task status.
6. Report what was actually implemented and what remains incomplete.

Never mark a feature complete just because code was generated.

## 24. Security, acceptance criteria, and final result

### Security principles

- Use least privilege.
- Validate file paths and user input.
- Keep Tauri permissions minimal.
- Separate frontend commands from privileged native operations.
- Require authorization for remote session access.
- Protect credentials through OS-backed storage.
- Avoid secrets in logs and backups.
- Confirm destructive operations.
- Avoid hidden persistence and undocumented remote access.
- Handle network failures and expired sessions safely.
- Never execute AI-generated commands automatically without the required user authorization.
- Treat imported files, terminal output, and remote responses as untrusted data.

### Acceptance criteria

The project is acceptable only when the implemented scope has been tested and verified against these requirements:

- The application launches and remains responsive.
- The visual design is consistent and usable.
- Fullscreen can be exited or recovered safely.
- Monitor selection and fallback work.
- Workspaces and layouts persist correctly.
- Real Windows terminal processes can be created and managed.
- OpenCode can be launched through a configured, real executable.
- Shell configurations can be managed and tested.
- Studies edits actual Markdown files and protects against silent data loss.
- SkyNetChat works through the integrations that have genuinely been implemented and verified.
- Namastê works in the application, with floating mode available when enabled.
- WebShell provides secure, authenticated remote access only after configuration and verification.
- Database migrations and recovery behavior are tested.
- Important settings and metadata survive a restart.
- Resource usage has been measured against the initial performance targets.
- Relevant automated tests have been executed.
- Documentation accurately describes setup, behavior, limitations, and troubleshooting.
- No feature is falsely described as completed.

### Final delivery

Deliver a working personal Windows application built with the required stack.

Prioritize reliability, low resource consumption, coherent design, secure native integration, and genuinely functional features over the number of features claimed.

Do not stop at a mockup, a static frontend, or a plan.

At each milestone, report:

- What was implemented.
- Which files were changed.
- Which tests were actually run.
- What passed and what failed.
- Known limitations.
- The next concrete task.

The final report must distinguish fully verified functionality from partially implemented or blocked functionality.

**The ultimate goal is a polished personal work environment in which terminals, workspaces, AI, notes, remote access, and Namastê operate together as one reliable application.**