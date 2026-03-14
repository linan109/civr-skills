# harness-inform

Make sure AI understands the project before it starts guessing.

## On session start

If no CLAUDE.md exists in the project root, suggest: "This project has no CLAUDE.md. Want me to scan and generate one?"

If CLAUDE.md exists, read it silently. Do NOT summarize it back to the user.

## Project scan (triggered by: "scan project" or "initialize context")

Execute this scan sequence:

1. Read directory structure (depth 3, skip .git/node_modules/venv/__pycache__/.next/dist/build)
2. Identify tech stack and package manager from config files:
   - `package.json` → Node.js, check for npm/yarn/pnpm lock files
   - `pyproject.toml` / `requirements.txt` → Python, check for uv/pip/poetry
   - `go.mod` → Go
   - `Cargo.toml` → Rust
   - `pom.xml` / `build.gradle` → Java
3. Extract build, test, and start commands from config and scripts
4. Detect existing lint/format configuration (.eslintrc, .prettierrc, ruff.toml, etc.)
5. Generate a CLAUDE.md draft with these sections:

```markdown
# Project: [name]

## Tech Stack
[language, framework, package manager, runtime version]

## Commands
- Build: [command]
- Test: [command]
- Lint: [command]
- Start: [command]

## Project Structure
[key directories and their purpose, 10 lines max]

## Rules
[only rules that prevent AI mistakes — "without this, AI will do the wrong thing"]
```

6. Present the draft to the user for review. Do NOT write it without confirmation.

## Context injection rules

- When the user starts a task involving existing code, read the relevant files FIRST before generating any code.
- When the user pastes an error, read it completely. Do NOT summarize or skip parts — the detail you skip might be the cause.
- When working in a subdirectory that has its own README or CLAUDE.md, read that too.

## Knowledge crystallization loop

When the user corrects a mistake:
1. Fix the mistake
2. Ask: "Want me to add this as a rule to CLAUDE.md so it doesn't happen again?"
3. If yes, append the rule with a brief "why" explanation

Format for crystallized rules:
```markdown
- [Rule]. Reason: [why, from the correction that triggered this]
```

## Context hygiene

<!-- CUSTOMIZE: adjust thresholds for your setup -->

- If the conversation exceeds ~40 turns, suggest starting a fresh session with a clean CLAUDE.md reload
- When giving AI files to read, prefer specific functions over entire files — saves tokens, improves focus
- Do NOT load all project files upfront. Load on demand, guided by the task.

## Anti-patterns to watch for

- AI using `pip install` when the project uses `uv` → check package manager in CLAUDE.md
- AI using deprecated/old API versions → check framework version in CLAUDE.md
- AI creating duplicate implementations of existing functionality → read project structure first
- AI using REST when project uses GraphQL (or vice versa) → check API style in CLAUDE.md

## When this skill activates

- On every session start (CLAUDE.md check)
- When user says "scan project" or "initialize context"
- After every user correction (knowledge crystallization prompt)
