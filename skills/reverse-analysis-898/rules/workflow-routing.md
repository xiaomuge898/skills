# Workflow And Routing Rules

Role: evidence intake and initial target routing.

Type: Support capability for `rules/orchestrator.md`, not the top-level execution engine.

Input: user request, supplied materials, observed errors, request samples, runtime evidence.

Output: material inventory, missing evidence, target classification, initial branch recommendation, hypothesis records when needed.

Returns control to: `rules/orchestrator.md` and the active main branch.

## Operating Workflow

Use this file for intake and routing. Use `orchestrator.md` for phase order, branch ownership, support-capability calls, verification gates, and report shape.

1. Reconstruct the request: goal, inputs, expected output, constraints, and ambiguities.
2. Inventory supplied materials and name missing evidence.
3. Classify the target output.
4. Recommend the narrowest main branch.
5. If evidence cannot determine the logic, enter Hypothesis-Driven Validation before deep static reading.
6. Return the intake result to the orchestrator.

## Hypothesis-Driven Validation

Trigger this mode when any of these are unknown or contradictory:

- Crypto or encoding algorithm.
- Parameter generation source or participating fields.
- Timestamp, random value, nonce, device fingerprint, cookie, localStorage, or server seed dependency.
- Obfuscation structure, VM dispatch, dynamic code generation, or environment detection.
- Static code path and runtime output do not match.

Use this loop:

```text
Observe -> propose hypotheses -> run minimal verification experiment -> judge result -> revise hypothesis -> continue
```

Process:

1. Observe the current evidence: code snippets, request samples, plaintext/ciphertext pairs, runtime values, output length/charset/format, and call locations.
2. Propose at least two plausible hypotheses when evidence permits. Examples: AES/RSA/Base64/XOR/custom bit operations, concat/sort/sign/hash, timestamp/random/device fingerprint/server-issued seed, environment check, VM opcode branch, dynamic function generation.
3. Choose the smallest experiment that can reject or support each hypothesis: controlled input, one-variable change, hook, breakpoint, monkey patch, crypto wrapper, replay diff, or before/after payload comparison.
4. Write the expected result before running the experiment.
5. Compare observed result to expectation. Mark the hypothesis as supported, rejected, or inconclusive.
6. If rejected, explain the mismatch and switch to the next likely mode. Do not continue static reading inside a rejected path.
7. Update the active reasoning path after every experiment.

Record every experiment in this shape:

```text
[Current hypothesis]
[Why plausible]
[Minimal verification experiment]
[Expected result]
[Observed result]
[Judgment] supported / rejected / inconclusive
[Next reasoning direction]
```

## Material Inventory

List what is present and what is missing:

- JavaScript files, source maps, webpack chunks, HTML, workers, wasm.
- Obfuscated JavaScript fingerprints: string arrays, decoder wrappers, object proxy tables, computed properties, `while/switch` dispatchers, `eval`/`Function`, anti-debugger fragments, AST transform scripts, and recovered output samples.
- Network captures, HAR, curl, URL, method, headers, cookies, query, body.
- Plaintext samples, ciphertext samples, response samples, error messages.
- Console logs, breakpoint screenshots, runtime variable snapshots.
- Electron install path, `app.asar`, unpacked directory, `package.json`, `main.js`, `preload.js`.
- Fingerprint-browser shortcut, launch script, process command line, disabled DevTools/F12/Ctrl+Shift+I symptoms.

If evidence is insufficient, say:

```text
Current evidence is insufficient for a conclusion.
Missing materials:
1. ...
Recommended next step:
1. ...
```

## Task Routing

| User intent | Branch |
|---|---|
| Find where `sign`, `token`, `data`, `payload`, or `X-Bogus` comes from | Parameter generation |
| Find `key`, `iv`, `salt`, password, nonce seed, or fixed salt | Key source analysis |
| Decrypt a parameter or restore a payload | Payload decryption |
| Identify an algorithm or library | Library and algorithm identification |
| Unknown crypto, unknown parameter source, unclear obfuscated logic, or conflicting evidence | Hypothesis-Driven Validation, then the narrow target branch |
| Write JS/Python request replay code | Request replay |
| Analyze VM, jsVMP, opcode, bytecode, handler tables | VM path restoration |
| Recognize JavaScript Obfuscator / obfuscator.io code, string arrays, object proxies, computed properties, dead code, or control-flow flattening | JavaScript obfuscation recognition and AST deobfuscation |
| Build or review AST-based JavaScript deobfuscation passes, visitors, transformers, or recovery order | AST based JavaScript deobfuscation |
| Remove or understand `debugger`, DevTools detection, anti-debugging | Anti-debugger analysis |
| Debug Electron asar, DevTools, F12, Ctrl+Shift+I | Electron debugging switch |
| Locate or enable `remote-debugging-port` | Remote debugging parameter discovery |

A task can match multiple branches. Start with the narrowest branch that can answer the user.
