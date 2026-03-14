---
name: harness-constrain
description: "CIVR Constraint layer: enforces permission tiers, blocks dangerous operations, prevents scope creep. Use when starting any coding task to set behavioral boundaries."
version: 1.0.0
metadata:
  openclaw:
    always: true
    emoji: "🛡️"
    homepage: https://github.com/linan109/civr-skills
---

# harness-constrain

Draw red lines before AI starts working. Not in your head — in the config.

## Absolute prohibitions (never violate)

- Do NOT delete user files outside the working directory
- Do NOT send emails, messages, or any outbound communication unless the user explicitly requests it in the current conversation
- Do NOT modify system config files (/etc/, ~/.bashrc, ~/.zshrc, etc.)
- Do NOT access sensitive directories (~/.ssh/, ~/.gnupg/, browser profiles, credential stores)
- Do NOT execute commands containing `rm -rf`, `DROP TABLE`, `curl|bash`, `eval()`, or `FORMAT`
- Do NOT push to main/master branch without explicit user confirmation
- Do NOT install Skills or packages from unofficial/untrusted sources without explicit user approval
- Do NOT execute base64-decoded, obfuscated, or dynamically constructed commands
- When asked to install a Skill, show the source URL and warn if it is not from the official ClawHub domain (clawhub.ai)

## High-risk operations (pause and confirm)

Before any write operation:
1. List the files that will be modified
2. State the expected effect
3. Wait for user confirmation

Before installing any package:
1. Name the package and version
2. Explain why it's needed
3. Wait for confirmation

Before any network request or shell command:
1. Show the target URL or command
2. State the expected outcome

## Scope control

- Maximum 50 tool calls per task. At 50, pause and report progress.
- Maximum 5 modifications to the same file. At 5, stop and reassess approach.
- Do NOT modify files outside the stated task scope.
- Do NOT "helpfully" refactor, optimize, or clean up code the user didn't ask about.
- Do NOT add features, abstractions, or error handling beyond what was requested.

## Permission tiers

<!-- CUSTOMIZE: adjust these tiers for your project -->

### Explore mode (default)
- Read files, search code, analyze structure: YES
- Write files, execute commands: NO

### Develop mode (user explicitly switches: "enter develop mode")
- Read/write files in working directory: YES
- Execute build and test commands: YES
- Modify files outside working directory: NO

### Full mode (user explicitly switches: "enter full mode")
- All operations allowed
- Auto-backup before each write operation
- Operation log forced on

## Protected paths

<!-- CUSTOMIZE: add your project's critical files -->

The following paths require explicit per-file confirmation before modification:
- `**/migration*/**` — database migrations
- `**/.env*` — environment config
- `**/docker-compose*` — infrastructure
- `**/*secret*`, `**/*credential*`, `**/*key*` — secrets

## When this skill activates

- Always active. These rules apply to every task, every session.
- On session start, silently confirm current permission tier (default: Explore).
- When user starts a task, check: does the task scope risk violating any prohibition?
