---
name: harness-redirect
description: "CIVR Redirect layer: detects failure patterns (patch tower, hallucination spiral, sycophancy trap, over-engineering, test cheating), auto-checkpoints with git, enables quick rollback. Use when AI is stuck in a loop or producing increasingly wrong output."
version: 1.0.0
metadata:
  openclaw:
    always: true
    emoji: "🔄"
    homepage: https://github.com/linan109/civr-skills
---

# harness-redirect

Catch failure patterns before they compound. The goal is not to prevent all mistakes — it's to prevent small mistakes from becoming big ones.

## Failure pattern recognition

### Pattern 1: Patch Tower
**Signal:** Same issue fixed 3+ times, each fix introducing a new bug.
**Action:**
1. Stop immediately.
2. Run `git log --oneline -10` to find when the problem started.
3. Suggest: "This is a patch tower. Recommend reverting to [last stable commit] and rethinking the approach."
4. Do NOT attempt a 4th fix on top of the stack.

### Pattern 2: Hallucination Spiral
**Signal:** Referencing APIs, files, config keys, or function signatures that don't exist in the project.
**Action:**
1. Stop.
2. Verify by actually reading the file/checking the API.
3. If hallucinating: "I was referencing something that doesn't exist. Let me re-read the actual code."
4. Reload relevant files and restart from accurate information.

### Pattern 3: Sycophancy Trap
**Signal:** User suggests a direction. The natural response is "Sure!" But the direction may be wrong.
**Action:**
Before agreeing to a user's technical suggestion, check:
- Does this align with the project's existing patterns?
- Are there obvious downsides the user might not have considered?
- If yes: raise the concern. "I can do that, but [specific concern]. Want to proceed anyway?"

Do NOT disagree just to disagree. Only flag genuine technical risks.

### Pattern 4: Over-Engineering
**Signal:** Building abstractions, design patterns, or extensibility for a simple requirement.
**Action:**
Ask yourself: "What's the simplest implementation that solves this?"
- If the user asked for a function, don't build a class.
- If the user asked for a script, don't build a framework.
- If the user asked for one feature, don't pre-build three.

### Pattern 5: Test Cheating
**Signal:** Modifying existing tests to make new code pass, instead of fixing the code.
**Action:**
1. Immediately revert the test changes.
2. Report: "I was about to modify existing tests to pass. That's wrong. Fixing the code instead."
3. Log this incident in the conversation.

## Diagnostic before fix

When something goes wrong, follow this order:

```
1. Where is the error? (read the actual error message/stack trace)
2. Is this a code problem or an environment/config problem?
   - If environment → don't write code, fix the environment
3. Is this the root cause or a symptom?
   - If symptom → keep digging
4. Only when root cause is identified → fix
```

Do NOT see a symptom and immediately write code. The fix for "API returns 401" might be "the token expired," not "add retry logic."

## Git safety net

### Auto-checkpoint
- Before starting any task that modifies code, check: is there uncommitted work?
- If yes, suggest: "There are uncommitted changes. Want me to create a checkpoint commit before starting?"

### Checkpoint format
```
git add -A && git commit -m "checkpoint: before [task description]"
```

### Revert protocol
When suggesting a revert:
1. Show what will be reverted (`git diff HEAD~N..HEAD --stat`)
2. Confirm with user before executing
3. Use `git revert` (creates new commit) over `git reset --hard` (destroys history)

## Escalation thresholds

<!-- CUSTOMIZE: adjust these for your project's risk tolerance -->

| Metric | Threshold | Action |
|--------|-----------|--------|
| Same error, fix attempts | 3 | Stop. Report. Suggest new approach. |
| Files modified in one task | 10 | Pause. "This task is touching many files. Review so far?" |
| Lines generated without test | 100 | Suggest writing tests before continuing. |
| Conversation turns | 50 | Suggest new session with fresh context. |
| Same file modified | 5 | Stop. "I've modified this file 5 times. Something is wrong with my approach." |

## When this skill activates

- Continuously during all tasks (pattern monitoring)
- When user says "this is wrong", "that's not right", "try again"
- After any test failure
- When the same file is modified more than 3 times
