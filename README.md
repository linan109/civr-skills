# CIVR Skills — Harness Skill Pack for OpenClaw

CIVR (Constrain, Inform, Verify, Redirect) is a methodology for working effectively with AI coding assistants. This Skill Pack turns that methodology into installable, enforceable rules for [OpenClaw](https://github.com/openclaw/openclaw).

> From the book *《驾驭AI编程》(Harness AI Programming)* by Li Nan.

## What problem does this solve?

AI coding assistants are powerful but structurally flawed: they hallucinate, they over-engineer, they "helpfully" modify files you didn't ask them to touch, and they confidently declare "done" when tests haven't been run.

Methodologies help — if you remember to follow them. At 2am fixing a production bug, you won't. **CIVR Skills remember for you.**

## The Four Skills

| Skill | What it does | Key rules |
|-------|-------------|-----------|
| **harness-constrain** | Draws red lines AI cannot cross | Permission tiers, dangerous operation interception, scope limits |
| **harness-inform** | Feeds AI the context it needs | Project scanning, AGENTS.md generation, knowledge crystallization loop |
| **harness-verify** | Forces verification before "done" | Auto lint/test/security scan, no deleting tests to pass, evidence-based completion |
| **harness-redirect** | Catches failure patterns early | Patch tower detection, hallucination spiral, auto-checkpoint & rollback |

## Quick Start

### Option 1: Install via ClawHub

```bash
clawhub install harness-constrain
clawhub install harness-inform
clawhub install harness-verify
clawhub install harness-redirect
```

### Option 2: Manual install

```bash
git clone https://github.com/linan109/civr-skills.git
cp -r civr-skills/harness-*/ ~/.openclaw/skills/
```

### Option 3: Project-level install

```bash
# Copy into your project's skills directory
cp -r civr-skills/harness-*/ your-project/skills/
```

### Option 4: Cherry-pick rules

Each SKILL.md is self-contained. Read through them and copy the rules that matter most to your project directly into your `AGENTS.md`.

## Customization

Each skill has `<!-- CUSTOMIZE -->` markers at the sections you should adjust:

- **harness-constrain**: Add your project's protected files/directories, adjust permission tiers
- **harness-inform**: Configure tech stack detection, add project-specific context rules
- **harness-verify**: Add project-specific test/lint commands, configure security scan keywords
- **harness-redirect**: Add custom failure patterns from your own experience, adjust retry thresholds

## Design Principles

1. **Skills are signposts, not firewalls.** SKILL.md rules are natural language instructions — the model *usually* follows them, but may ignore them under context pressure. For hard guarantees, layer with Docker isolation and hooks.
2. **Default to minimal friction.** Start with read-only mode. Escalate permissions only when needed.
3. **Every correction becomes a rule.** The knowledge crystallization loop: you correct AI → it suggests adding the correction to AGENTS.md → next session starts smarter.
4. **Less is more.** Fewer, sharper rules beat a wall of text. Each SKILL.md stays under 5K tokens.

## The CIVR Framework

```
C — Constrain: What can't AI do? (before it starts)
I — Inform:    What does AI need to know? (context engineering)
V — Verify:    How do we confirm it's right? (not just "it runs")
R — Redirect:  What do we do when it's wrong? (pattern recognition + rollback)
```

For the full methodology, see *《驾驭AI编程》* (publishing 2026).

## License

MIT
