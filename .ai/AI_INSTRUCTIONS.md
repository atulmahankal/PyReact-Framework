# AI Agent Instructions

Read order:

| File                                | Permission  | Remark                                                        | Updated by | Copilot    | Gemini     | Claude     | Antigravity | Other      |
| ----------------------------------- | ----------- | ------------------------------------------------------------- | ---------- | ---------- | ---------- | ---------- | ----------- | ---------- |
| ./.ai/PROJECT_CONTEXT.md           | read-only   |                                                               | Developer  | 1          | 4          | 3          | 2           | 1          |
| ./.ai/AI_INSTRUCTIONS.md            | read-only   |                                                               | -          | 2          | 2          | 2          | 2           | 2          |
| ./.ai/ARCHITECTURE_AND_DECISIONS.md | read-only   |                                                               | -          | 9          | 11         | 2          | 1           | 3          |
| ./.ai/CODING_GUIDELINES.md          | read-only   |                                                               | -          | 8          | 10         | 11         | 8           | 4          |
| ./.ai/HISTORY.md                    | editable    |                                                               | Memory     | AI & Human | AI & Human | AI & Human | AI & Human  | AI & Human |
| ./.ai/OPERATIONAL_GUIDELINES.md     | read-only   |                                                               | -          | 12         | 6          | 5          | 9           | 5          |
| ./.ai/PROJECT_MANAGEMENT.md         | read-only   |                                                               | -          | 10         | 3          | 1          | 6           | 6          |
| ./.ai/TESTING_GUIDELINES.md         | read-only   |                                                               | -          | 11         | 12         | 10         | 7           | 7          |
| ./.ai/TODO.md                       | editable    |                                                               | Internal   | AI & Human | AI & Human | AI & Human | AI & Human  | AI & Human |
| ./Implementation/CURRENT_TASK.md    | editable    |                                                               | Developer  | 4          | 7          | 8          | 4           | 2          |
| ./Implementation/PROJECT_CONTEXT.md | read-only   |                                                               | Developer  | 1          | 4          | 3          | 2           | 1          |
| ./Implementation/TODO.md            | append-only | Only tick [x] items which completed                           | Developer  | AI & Human | AI & Human | AI & Human | AI & Human  | AI & Human |
| ./README.md                         | editable    |                                                               | Developer  | 3          | 1          | 5          | 9           | 1          |
| ./CHANGELOG.md                      | editable    |                                                               | Developer  | 13         | 12         | 12         | 10          | 8          |
| ./Reference/\*                      | read-only   |                                                               | -          | 7          | 9          | 9          | 7           | 3          |
| ./docs/                             | editable    | Detailed documentation for developer, include marmaid diagram | Devs       | 5          | 5          | 6          | 3           | 4          |
| ./docs/API/*.rest                  | editable    |                                                               | Devs       | 6          | 6          | 7          | 5           | 5          |

Rules:

- Do NOT scan the entire repository; work only in files explicitly relevant to the active task.
- Never modify PROJECT_CONTEXT.md or ARCHITECTURE_AND_DECISIONS.md (they are read-only).
- TODO.md items must never be removed; append-only or mark completed only.

Execution contract (how to respond when performing tasks):

- Start responses with a brief plan (3–6 bullet points max).
- Ask a single clarifying question only if necessary to proceed.
- Modify the smallest possible surface area to implement a change.
- Prefer editing existing files over creating new ones.
- Do not refactor unrelated code.
- Stop when the specific task is complete.
- Assume PROJECT_CONTEXT.md is loaded and authoritative; do not ask about it.

Output rules:

- Show code or diffs first, explanations after.
- Be concise by default; elaborate only when asked or when ambiguity exists.
- When multiple files are changed, list them first.

Uncertainty rule:

- If requirements are unclear, ask one focused clarifying question; do NOT invent behavior.

Session resume template (on startup):

1. Summarize Implementation/CURRENT_TASK.md in 5 lines.
2. Ask: "Resume this task? (yes/no)"
3. If no → wait for new instructions.

## Purpose

This file contains provider-agnostic rules and the execution contract for the AI agent used in this repository. It is intended to be followed by any AI integration (Claude, OpenAI, or other providers) to ensure consistent behavior, low memory usage, and token protection.

## Read & Working Rules

- Always read .ai/AI_INSTRUCTIONS.md first (this file provides the authoritative agent instructions).
- Treat .ai/PROJECT_CONTEXT.md as read-only and authoritative for project-specific details.
- Work only on files explicitly relevant to the active task.
