# Project Generation

Generate a project only when analysis is sufficient to choose a stable replay strategy.

## Target Structure

```text
project/
|-- README.md
|-- config.py
|-- api.py
|-- client.py
|-- models.py
|-- utils/
|   |-- crypto.py
|   `-- generator.py
|-- tests/
|   |-- test_api.py
|   `-- test_params.py
`-- analysis_report.md
```

## Implementation Requirements

- Python 3.12.
- `httpx`.
- `asyncio`.
- `pytest`.
- Use a venv for dependencies when writing files in a local project.
- Use `pathlib`, `dataclass`, and typing where useful.
- Keep secrets in environment variables or a local ignored config file.
- Do not hardcode full cookies, tokens, passwords, or private keys into generated code.
- Keep request construction separate from parameter generation.
- Keep models separate from transport logic.
- Include timeouts, retry boundaries, and clear exception handling.

## Module Roles

| File | Responsibility |
|---|---|
| `config.py` | base URL, timeout, headers that are safe to keep, environment variable loading |
| `models.py` | dataclasses or typed structures for request params and parsed responses |
| `utils/generator.py` | timestamp, nonce, UUID, request ID, pagination helpers |
| `utils/crypto.py` | signing/encryption placeholders or recovered algorithm wrappers |
| `client.py` | reusable async HTTP client, session/cookie injection, request sending |
| `api.py` | endpoint-specific functions and business-level API calls |
| `tests/test_params.py` | parameter generation and classification tests |
| `tests/test_api.py` | interface smoke, mocked response, or optional live test |
| `README.md` | setup, usage, environment variables, verification, risks |
| `analysis_report.md` | structured request analysis report |

## Test System

Generate or require these tests:

1. Parameter tests:
   - Different parameter combinations.
   - Timestamp and nonce format.
   - Signature placeholder behavior or recovered algorithm behavior.
   - Fixed vs dynamic parameter handling.

2. Interface tests:
   - Successful request construction.
   - Expected status handling.
   - Error response parsing.
   - Optional live test gated by an environment variable.

3. Stability tests:
   - Continuous requests with controlled interval.
   - Timeout behavior.
   - Retry limits.
   - Rate/concurrency boundaries.

4. Regression tests:
   - Save sanitized successful request samples.
   - Compare generated requests against the sample shape.
   - Do not save raw sensitive cookies, tokens, or account data.

Testing pattern:

```text
Default tests must run offline.
Live tests must be opt-in through an environment variable such as RUN_LIVE_API_TESTS=1.
```
