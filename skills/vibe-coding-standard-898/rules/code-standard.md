# Code Standard

Apply these instructions whenever writing or modifying code.

## Python Baseline

- Use Python 3.12+ syntax by default.
- Create or reuse a project-local virtual environment before installing dependencies.
- Install packages only inside the virtual environment.
- Prefer `pathlib`, `dataclasses`, `typing`, comprehensions, generator expressions, context managers, and standard-library solutions.
- Use `async`/`await` for IO-bound concurrency when the surrounding stack supports it.
- Do not introduce a dependency unless it removes meaningful complexity or is already standard in the project.

## Typing

- Add type hints to public functions, methods, dataclasses, and non-trivial helpers.
- Use precise types: `Path`, `Mapping`, `Sequence`, `Iterable`, `Callable`, `Literal`, `TypedDict`, `Protocol`, or dataclasses when they improve clarity.
- Avoid `Any` unless the boundary is genuinely dynamic. If using `Any`, isolate it at the edge.
- Prefer returning structured objects over ad hoc tuples for multi-field results.

## Design

- Enforce single responsibility per function, class, and module.
- Keep functions short enough to understand without scrolling heavily. Split when a function mixes parsing, validation, IO, transformation, and reporting.
- Keep side effects at the boundary. Put pure transformation logic in testable helpers.
- Preserve existing public API and behavior unless the change explicitly requires otherwise.
- Prefer explicit data flow over hidden global state.
- Avoid speculative frameworks, plugin layers, factories, registries, or inheritance trees.

## Naming

- Use names that reveal intent and domain meaning.
- Python: `snake_case` for functions and variables, `PascalCase` for classes, `UPPER_CASE` for constants.
- JavaScript/TypeScript: `camelCase` for values and functions, `PascalCase` for classes and components, `UPPER_CASE` for constants.
- Avoid vague names: `data`, `info`, `tmp`, `obj`, `manager`, `handler` unless their scope makes the meaning obvious.

## Error Handling

- Validate inputs at module boundaries.
- Raise specific exceptions with actionable messages.
- Do not catch broad exceptions unless re-raising with context or safely converting to a documented result.
- Preserve original exceptions with `raise ... from exc` when wrapping.
- Do not silently ignore failures. If a fallback is used, log or return evidence that it happened.
- Keep retry behavior bounded and explain backoff, timeout, and stop conditions.

## Logging

- Use structured, level-appropriate logs.
- Log events, decisions, and failure context. Do not log full secrets, cookies, tokens, private keys, or credentials unless the active reverse-engineering skill explicitly requires raw authorized evidence.
- Use `debug` for diagnostic detail, `info` for lifecycle events, `warning` for recoverable anomalies, `error` for failed operations, and `exception` when stack traces are useful.
- Avoid `print` in libraries. Use `print` only for CLI output or deliberate user-facing scripts.

## Security

- Treat file paths, URLs, shell arguments, JSON, YAML, HTML, SQL, and JavaScript as untrusted unless proven otherwise.
- Use parameterized APIs instead of string-built commands or queries.
- Do not evaluate user-controlled text with `eval`, `exec`, `Function`, or dynamic imports.
- Keep secrets out of source, docs, tests, logs, and screenshots.
- Add timeouts to network and subprocess operations.

## Maintainability

- Keep code easy to delete and easy to test.
- Prefer small helpers with clear inputs and outputs over large stateful objects.
- Co-locate tests near the behavior or follow repository convention.
- Update docs when behavior, setup, API, or operational assumptions change.
- Leave unrelated refactors for a separate task.
