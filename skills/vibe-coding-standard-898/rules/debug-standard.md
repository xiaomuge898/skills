# Debug Standard

Apply these instructions for every error, failing test, traceback, crash, hang, incorrect output, flaky behavior, or unexpected result.

## Non-Negotiable Rule

Do not modify code immediately after seeing an error. Analyze the failure first.

## Debug Workflow

1. Capture the exact symptom:
   - command
   - exit code
   - stderr/stdout
   - traceback
   - input data
   - environment
2. Read the traceback from the bottom up:
   - identify the exception type
   - identify the failing line
   - identify the first project-owned frame
   - identify the call path that supplied bad state
3. Form a root-cause hypothesis:
   - what value or invariant is wrong?
   - where did it become wrong?
   - why did existing checks not catch it?
4. Prove or reject the hypothesis:
   - add a focused reproduction
   - inspect variables
   - run a narrow command
   - compare with a known-good case
5. Choose a fix:
   - fix the cause, not the symptom
   - keep the fix minimal
   - avoid broad catch-and-ignore changes
6. Verify:
   - run the reproduction
   - run related regression tests
   - run syntax/type checks when relevant
7. Document:
   - root cause
   - fix
   - verification evidence
   - remaining risk

## Traceback Checklist

- Is the traceback from current code or stale generated output?
- Is the first failing project frame the cause or only the crash site?
- Did the error come from bad input, missing dependency, wrong version, encoding, path, permission, network state, race condition, or API contract drift?
- Does the fix need a regression test?
- Does the error reveal a missing validation boundary?

## Anti-Patterns

| Anti-pattern | Required replacement |
|---|---|
| Change random code near the traceback | Identify the bad invariant and where it originates |
| Catch `Exception` and continue | Catch the specific expected exception or let it fail |
| Delete failing tests | Fix behavior or update tests only when requirements changed |
| Assume dependency behavior | Check installed version and official docs |
| Claim fixed without rerun | Rerun the failing command and report output |

## Debug Output Contract

Use this structure when reporting a bug fix:

```text
Symptom:
Root cause:
Fix:
Verification:
Risk:
Alternative:
```
