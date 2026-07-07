# AI Coding Standard

Apply these instructions when planning, generating, editing, or reviewing code with AI assistance.

## Requirement Understanding

- Translate the user's request into a normalized requirement before implementing.
- List goal, inputs, outputs, constraints, unknowns, and acceptance criteria.
- If information is missing, provide at least two plausible interpretations and identify which one you will implement.
- Do not hide assumptions. Write them down before relying on them.
- Check existing docs and code before proposing architecture.

## Task Decomposition

- Split work into small, ordered tasks.
- Separate discovery, design, implementation, testing, documentation, and final verification.
- Identify the smallest slice that proves the design.
- Keep each task independently reviewable and reversible.
- Avoid parallel edits to unrelated modules.

## Code Generation

- Generate code only after the design and target files are clear.
- Prefer modifying existing code paths over adding new parallel systems.
- Keep generated code idiomatic for the repository.
- Use deterministic helpers or scripts for repetitive transformations.
- Do not paste large blocks when a focused patch is sufficient.
- Do not invent APIs, file paths, or behavior. Verify them from the repository or official docs.

## Self-Check

Before considering code complete, check:

- Requirement coverage: every stated requirement maps to code, tests, docs, or an explicit non-goal.
- Behavior: normal, invalid, boundary, and failure paths are handled.
- Integration: imports, exports, routes, CLI options, and configuration names match existing code.
- Data safety: secrets are not logged or committed.
- Portability: Windows paths, encodings, shell behavior, and line endings are considered when relevant.
- Maintainability: the code can be understood, tested, and changed later.

## Review Procedure

- Review the diff, not memory.
- Lead with defects and risks.
- Check for behavioral regressions, missing tests, hidden state, broad exception handling, unbounded loops, missing timeouts, and unnecessary abstractions.
- Verify the implementation matches the design. If the design changed, update the explanation.
- Treat "works on my machine" as insufficient without reproducible commands.

## Bug, Performance, and Security Detection

- Look for off-by-one errors, empty inputs, null/undefined/None values, duplicate keys, timezone issues, encoding issues, concurrency races, and cleanup leaks.
- Check algorithmic complexity on expected input sizes.
- Add timeouts, limits, and backpressure for IO and automation.
- Check injection boundaries: shell, SQL, HTML, JS, regex, YAML, pickle, deserialization, and template rendering.
- Check authorization boundaries before network, scraping, reverse engineering, or automation work.

## Communication

- State the solution first, then the reason.
- Include risks and alternatives in every substantial answer.
- When blocked, explain the exact missing evidence and the next command or artifact needed.
