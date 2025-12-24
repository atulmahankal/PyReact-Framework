## Docs:

- Prepare and maintain README.md (Features, Prerequisites, Getting Started, Project Structure, etc).
- Maintain CHANGELOG.md and HISTORY.md for agent activity.

### File Ownership

Read-only (never modify):

- .ai/ARCHITECTURE_AND_DECISIONS.md
- .ai/PROJECT_CONTEXT.md

May update:

- Implementation/CURRENT_TASK.md
- Implementation/TODO.md (append only)
- .ai/HISTORY.md
- CHANGELOG.md

Create/update when needed:

- README.md
- docs/API/*.rest

## Scope guardrails (forbidden unless explicitly requested):

- Adding new major libraries or frameworks.
- Large architectural rewrites.
- Background workers beyond the project's existing choices.
- Broad UI/system redesigns or design-system changes.
- Scanning or modifying unrelated parts of the repo.

## Task awareness:

- Assume Implementation/CURRENT_TASK.md defines the active work; summarize it at session start and ask to resume.
- Only append to TODO.md or mark items completed; do not reorder existing tasks.