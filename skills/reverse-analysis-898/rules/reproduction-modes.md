# Reverse Reproduction Modes

Role: mode decision gate for `rules/orchestrator.md`.

Input: target output, evidence inventory, expected deliverable, runtime dependency evidence.

Output: selected reproduction mode, extraction boundary, deliverable shape, verification method.

Returns control to: `rules/orchestrator.md` before main branch selection.

Choose the final execution mode before extracting code. Do not default to full browser-environment restoration when the requested deliverable can be verified with a smaller boundary.

| Mode | Use when | Extraction boundary | Default deliverable | Verification |
|---|---|---|---|---|
| In-site extraction | The final method must run inside the original page or app runtime. | Extract only the target method plus directly used local helpers, local variables, and closure values. Keep page globals as page-provided. | Console/CDP snippet, userscript, or small pasted function. | Call the method in the real context and compare the returned value or generated request. |
| Local Node service | Extracted JS must be consumed by external tools but is not pure math. | Extract the target path and replace browser dependencies with explicit inputs or minimal deterministic mocks. | Node.js runner plus a small local HTTP/CLI interface. | Call the local endpoint with samples and compare signature, ciphertext, payload, or request fields. |
| Pure algorithm port | The target is pure calculation: codec, hash, HMAC, encryption, decryption, sorting, or numeric/string transformation. | Re-implement with standard primitives where possible. | Pure Python/JS function or CLI with no browser, DOM, page SDK, or live runtime dependency. | Verify sample pairs, round trips, and edge cases. |

## Mode Rules

- If the deliverable runs inside the target website or app, use in-site extraction. Do not rebuild `window`, `document`, browser globals, webpack runtime, or global variables unless the target method directly consumes them and the page does not already provide them.
- If external tools need to call extracted JS behavior, use local Node service. Default to Node.js and expose one narrow interface.
- If the logic is pure math, encoding, hashing, signing, encryption, or decryption and can be proven with samples, use a pure algorithm port.
- Do not claim pure algorithm recovery when the solution still executes copied SDK/runtime code, VM bytecode, wasm, page-derived seed, or browser state.
- If the correct mode is unclear, state the competing modes and the evidence needed to choose one.
