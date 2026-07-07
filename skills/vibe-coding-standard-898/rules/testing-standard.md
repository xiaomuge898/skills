# Testing Standard

Apply these instructions when adding, modifying, or validating behavior.

## Required Coverage

- Normal input tests: expected valid use cases.
- Invalid input tests: malformed data, missing fields, wrong types, unauthorized or unsupported states.
- Boundary tests: empty, one item, maximum reasonable size, duplicate values, Unicode, time boundaries, path separators, and timeout edges.
- Core failure tests: dependency failure, network timeout, subprocess failure, parse failure, permission failure, and cleanup behavior where relevant.

## Test Design

- Test behavior, not implementation details.
- Keep tests deterministic.
- Prefer real code paths over mocks. Use mocks only for external systems, time, randomness, network, and expensive IO.
- Give tests names that state the behavior.
- Use fixtures sparingly and keep them obvious.
- Add regression tests for bugs before or while fixing them.

## Python Tests

- Use the repository's existing test framework.
- If none exists, prefer `unittest` from the standard library for minimal dependency impact, or `pytest` only when already used.
- Run `python -m py_compile` for changed Python files when a full test run is not available.
- Run the narrow test first, then related broader tests.

## JavaScript Tests

- Use the repository's existing runner.
- If none exists, use `node --check` for syntax and a small Node test script for pure functions.
- For browser code, test pure helpers in Node when possible and verify DOM/browser behavior with the available browser automation stack.

## Verification Matrix

For every implementation, decide and run the strongest feasible checks:

| Change type | Minimum verification |
|---|---|
| Python logic | unit test plus `py_compile` |
| JavaScript logic | unit test plus `node --check` |
| CLI | command smoke test with exit code |
| API route | request/handler test with normal and error cases |
| UI | interaction test or screenshot/manual checklist |
| Docs only | existence, required headings, links/commands if present |
| Config | parser/validator or application dry run |

## Completion Gate

Do not claim completion until fresh verification has run in the current task.

Report:

- exact commands
- pass/fail result
- any warnings
- any tests not run and why
- residual risk
