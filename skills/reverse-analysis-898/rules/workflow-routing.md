# Workflow And Routing Rules

## Operating Workflow

1. Reconstruct the request: goal, inputs, expected output, constraints, and ambiguities.
2. Inventory supplied materials and name missing evidence.
3. Route to the smallest relevant branch and choose the reproduction mode before extracting code.
4. Build an evidence chain: code locations, call graph, inputs, return values, runtime variables, and request samples.
5. Prefer the smallest verifiable path. Restore only the target parameter path unless full deobfuscation is necessary.
6. Rebuild only the execution context required by the selected reproduction mode.
7. Verify by comparing sample input/output or original/replayed requests.
8. Answer with solution, evidence, rationale, risks, verification, and alternatives.

## Material Inventory

List what is present and what is missing:

- JavaScript files, source maps, webpack chunks, HTML, workers, wasm.
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
| Write JS/Python request replay code | Request replay |
| Analyze VM, jsVMP, opcode, bytecode, handler tables | VM path restoration |
| Remove or understand `debugger`, DevTools detection, anti-debugging | Anti-debugger analysis |
| Debug Electron asar, DevTools, F12, Ctrl+Shift+I | Electron debugging switch |
| Locate or enable `remote-debugging-port` | Remote debugging parameter discovery |

A task can match multiple branches. Start with the narrowest branch that can answer the user.
