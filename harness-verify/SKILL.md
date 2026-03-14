# harness-verify

Code that hasn't been verified is not "done." Period.

## Iron rules

1. **Never claim completion without evidence.** "I've made the changes" is not evidence. Test output, lint results, or a successful build is evidence.
2. **Never delete or modify existing tests to make new code pass.** If tests fail, fix the code, not the tests. If a test is genuinely wrong, explain why to the user and get explicit approval before changing it.
3. **Never skip verification because "it's a small change."** Small changes cause production outages too.

## Verification checklist (after every code change)

Execute in order. Stop at the first failure.

### 1. Syntax check
Run the project's lint/compile command:
<!-- CUSTOMIZE: replace with your project's actual commands -->
- Python: `ruff check` or `flake8` or `python -m py_compile`
- TypeScript: `tsc --noEmit`
- Go: `go vet ./...`
- Rust: `cargo check`

### 2. Test execution
Run existing test suite. Confirm zero regressions.
<!-- CUSTOMIZE: replace with your project's test command -->
- `pytest`, `npm test`, `go test ./...`, `cargo test`

Report: X passed, Y failed, Z skipped. If any failed, fix before proceeding.

### 3. Security quick-scan
Check the changed code for these 9 items:

- [ ] No hardcoded secrets (API keys, passwords, tokens)
- [ ] No SQL string concatenation (use parameterized queries)
- [ ] No `eval()`, `exec()`, or dynamic code execution with user input
- [ ] No user input rendered without sanitization (XSS)
- [ ] No sensitive data in logs (passwords, tokens, PII)
- [ ] No overly permissive CORS or file permissions
- [ ] No disabled SSL/TLS verification
- [ ] No temporary "debug" code left in (console.log with sensitive data, TODO bypasses)
- [ ] No new dependencies with known vulnerabilities

### 4. Change summary
Generate a one-paragraph natural language summary of what changed and why. Present to user for confirmation.

## Escalated verification (for high-risk changes)

<!-- CUSTOMIZE: adjust trigger keywords for your domain -->

When changes touch files matching these patterns, add extra verification:
- `*payment*`, `*billing*`, `*charge*` → verify idempotency, verify error rollback
- `*auth*`, `*login*`, `*permission*`, `*token*` → verify no privilege escalation, verify token expiry
- `*user*data*`, `*pii*`, `*personal*` → verify no data leakage in logs or responses
- `*migration*`, `*schema*` → verify rollback path exists
- `*delete*`, `*remove*`, `*purge*` → verify soft-delete or confirm hard-delete intent

For these files, use the double-translation check:
1. Before writing code: restate your understanding of the requirement
2. After writing code: describe what the code does (without looking at the requirement)
3. Compare. Differences = potential bugs.

## Coverage quality check

When tests pass, check what they actually test:
- Are there edge case tests (empty input, null, overflow, timeout)?
- Are there error path tests (network failure, invalid data, permission denied)?
- Does high coverage come from testing happy paths many times? (coverage% can lie)

If edge cases are missing, suggest specific test cases — not "add more tests" but "add a test for: user submits empty form while session is expired."

## Failure protocol

- Fix attempt 1: Fix the issue directly
- Fix attempt 2: Explain root cause analysis to user before fixing
- Fix attempt 3: **STOP.** Report to user: "I've failed to fix this 3 times. Here's what I've tried and why each failed. Suggest: revert to last working state and try a different approach."

Do NOT enter a patch-on-patch spiral.

## When this skill activates

- After every code modification (automatic checklist)
- When user says "verify", "check", or "is this right?"
- Before any commit or PR creation
