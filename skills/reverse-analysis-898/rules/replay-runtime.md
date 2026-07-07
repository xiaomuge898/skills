# Request Replay And Runtime Rules

## Request Replay

Use for: "Write reproduction code", "Replay with Python", "Replay with JS", "Restore the API request."

Process:

1. Choose in-site extraction, local Node service, or pure algorithm port before writing code.
2. Define method, URL, headers, cookies, query, and body.
3. Separate fixed fields from dynamic fields.
4. Reproduce dynamic parameter generation using the smallest boundary for the chosen mode.
5. Preserve the signed URL exactly after signing; do not rebuild signed query strings unless the signing algorithm requires it.
6. Rebuild only the required context; do not copy unrelated globals, modules, or browser mocks.
7. Build the smallest request. Avoid batch requests.
8. Compare original and replayed requests: parameter format, length, timestamp precision, nonce, sign, response status, response code, and response keywords.

## Python Requirements

- Use Python 3.12 by default.
- Create or reuse a venv before running Python.
- Install pip packages only inside the venv.
- Prefer the standard library.
- Add third-party dependencies only when they clearly reduce complexity or risk.
- Comment code enough to explain why each context mock or crypto step exists.
- When reproduction code generates or recovers reverse-analysis values inside authorized scope, do not redact, mask, truncate, or replace them; show them exactly as produced.

## Rebuild The Execution Context

When extracting code for protocol or frontend encryption analysis, restore the execution context layer by layer. Do not copy only the target function and assume it is enough.

For in-site extraction, the real page is the execution context. Keep extraction narrow and reuse the page's native globals instead of rebuilding them locally.

For DOM/BOM/Navigator/Window, fingerprint API, descriptor/prototype, native `toString`, or Node/browser divergence issues, load `rules/js-runtime-environment-reconstruction.md` and use its gap triage before adding broad mocks.

Rebuild in this order:

1. Function context: target function, closure variables, module imports, webpack runtime, global aliases.
2. Browser context: `window`, `document`, `navigator`, `location`, `screen`, `performance`, `crypto`, `localStorage`, `sessionStorage`, `cookie`.
3. Time and randomness: `Date.now()`, `new Date()`, `Math.random()`, `crypto.getRandomValues()`, nonce, millisecond/second timestamp precision.
4. Request context: URL path, query, body, headers, cookies, user-agent, referer, server-issued fields.
5. Dependency context: `CryptoJS`, `WebCrypto`, `node:crypto`, custom encoders, wasm, workers, dynamic imports.
6. Fingerprint context: language, timezone, platform, canvas/webgl, device ID, experiment flags, page bootstrap variables.
7. Verification context: real sample input, real sample output, packet capture comparison, breakpoint variable snapshots.

Use mocks and hooks only to reproduce local behavior. Document every mocked value and explain whether it affects output.
