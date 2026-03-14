# CIVR Skills — Harness Skill Pack for Claude Code

CIVR (Constrain, Inform, Verify, Redirect) is a methodology for working effectively with AI coding assistants. This Skill Pack turns that methodology into installable, enforceable rules for [Claude Code](https://claude.com/claude-code).

> From the book *《驾驭AI编程》(Harness AI Programming)* by Li Nan.

## What problem does this solve?

AI coding assistants are powerful but structurally flawed: they hallucinate, they over-engineer, they "helpfully" modify files you didn't ask them to touch, and they confidently declare "done" when tests haven't been run.

Methodologies help — if you remember to follow them. At 2am fixing a production bug, you won't. **CIVR Skills remember for you.**

## The Four Skills

| Skill | What it does | Key rules |
|-------|-------------|-----------|
| **harness-constrain** | Draws red lines AI cannot cross | Permission tiers, dangerous operation interception, scope limits |
| **harness-inform** | Feeds AI the context it needs | Project scanning, CLAUDE.md generation, knowledge crystallization loop |
| **harness-verify** | Forces verification before "done" | Auto lint/test/security scan, no deleting tests to pass, evidence-based completion |
| **harness-redirect** | Catches failure patterns early | Patch tower detection, hallucination spiral, auto-checkpoint & rollback |

## Quick Start

### Option 1: Copy into your project

```bash
# Clone this repo
git clone https://github.com/linan109/civr-skills.git

# Copy the skills you want into your project
cp -r civr-skills/harness-constrain/ your-project/.claude/skills/
cp -r civr-skills/harness-verify/ your-project/.claude/skills/
# ... etc
```

### Option 2: Use as a git submodule

```bash
cd your-project
git submodule add https://github.com/linan109/civr-skills.git .claude/skills/civr
```

### Option 3: Cherry-pick rules into CLAUDE.md

Each SKILL.md is self-contained. Read through them and copy the rules that matter most to your project directly into your `CLAUDE.md`.

## Customization

Each skill has clearly marked sections you should customize:

- **harness-constrain**: Add your project's protected files/directories, adjust permission tiers
- **harness-inform**: Configure tech stack detection, add project-specific context rules
- **harness-verify**: Add project-specific test/lint commands, configure security scan keywords
- **harness-redirect**: Add custom failure patterns from your own experience, adjust retry thresholds

See the `<!-- CUSTOMIZE -->` markers in each SKILL.md.

## Design Principles

1. **Skills are signposts, not firewalls.** SKILL.md rules are natural language instructions — the model *usually* follows them, but may ignore them under context pressure. For hard guarantees, layer with Docker isolation and hooks.
2. **Default to minimal friction.** Start with read-only mode. Escalate permissions only when needed.
3. **Every correction becomes a rule.** The knowledge crystallization loop: you correct AI → it suggests adding the correction to CLAUDE.md → next session starts smarter.
4. **Less is more.** Fewer, sharper rules beat a wall of text. Keep each SKILL.md under 5K tokens.

## The CIVR Framework

```
C — Constrain: What can't AI do? (before it starts)
I — Inform:    What does AI need to know? (context engineering)
V — Verify:    How do we confirm it's right? (not just "it runs")
R — Redirect:  What do we do when it's wrong? (pattern recognition + rollback)
```

For the full methodology, see *《驾驭AI编程》* or visit the book's companion site.

## License

MIT
