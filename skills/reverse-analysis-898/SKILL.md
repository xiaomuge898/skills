---
name: reverse-analysis-898
description: Use when analyzing authorized JavaScript reverse engineering, protocol reverse analysis, frontend encryption, request signing, token/sign/X-Bogus parameters, key/iv/salt discovery, CryptoJS/WebCrypto/node:crypto usage, encoding/hash/HMAC/AES/RSA/SM2/SM4 identification, VM/jsVMP obfuscation, anti-debugger logic, browser runtime tracing, Electron asar DevTools/F12 debugging, remote-debugging-port discovery, request replay, or crawler parameter restoration.
---

# NX JS Reverse Analysis

## Core Scope

Use this skill for authorized JavaScript reverse analysis, protocol restoration, frontend encryption analysis, request parameter generation, local browser/runtime tracing, and local Electron or fingerprint-browser debugging support.

Only work on user-authorized targets, local labs, owned code, public test samples, or files/materials explicitly provided for analysis. If a real third-party target is involved and the authorization scope is unclear, ask one focused question before acting.

Do analysis, reproduction, debugging, and defensive explanation. Do not help with unauthorized intrusion, account attacks, credential theft, access-control bypass, paid-license bypass, bulk endpoint abuse, or leaking sensitive files.

## Operating Workflow

1. Reconstruct the request before analysis: goal, inputs, expected output, constraints, and ambiguities.
2. Inventory the supplied materials and name missing evidence.
3. Route the task to the smallest relevant branch: parameter generation, key discovery, payload decryption, algorithm identification, request replay, VM/jsVMP, anti-debugger, Electron, or remote debugging.
4. Build an evidence chain: code locations, call graph, inputs, return values, runtime variables, and request samples.
5. Prefer the smallest verifiable path. Restore only the target parameter path unless full deobfuscation is necessary.
6. Rebuild the execution context before reproducing code.
7. Verify by comparing sample input/output or original/replayed requests.
8. Answer with solution, evidence, rationale, risks, verification, and alternatives.

## Material Inventory

Before analysis, list what is present and what is missing:

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

## Evidence-First Rules

- Do not say "looks like AES" or "probably MD5" without evidence. State the code location, input/output shape, function arguments, and confidence.
- Do not trust obfuscated variable names as semantic proof. Prefer data flow, call relationships, arguments, return values, and output features.
- Do not skip encoding layers. Many parameters are layered, such as `JSON -> gzip -> AES -> Base64 -> URL encode`.
- Do not ignore runtime variables. Keys, salts, tokens, nonces, timestamps, random values, and device fingerprints often exist only at runtime.
- Mark every uncertain claim with its uncertainty and the evidence needed to close it.
- Redact cookies, tokens, passwords, private keys, device IDs, account IDs, and proxy credentials in notes and reports.

## Rebuild the Execution Context

When extracting code for protocol or frontend encryption analysis, restore the execution context layer by layer. Do not copy only the target function and assume it is enough. Browser and runtime dependencies often decide the final signature or ciphertext.

Rebuild in this order:

1. Function context: target function, closure variables, module imports, webpack runtime, global aliases.
2. Browser context: `window`, `document`, `navigator`, `location`, `screen`, `performance`, `crypto`, `localStorage`, `sessionStorage`, `cookie`.
3. Time and randomness: `Date.now()`, `new Date()`, `Math.random()`, `crypto.getRandomValues()`, nonce, millisecond/second timestamp precision.
4. Request context: URL path, query, body, headers, cookies, user-agent, referer, server-issued fields.
5. Dependency context: `CryptoJS`, `WebCrypto`, `node:crypto`, custom encoders, wasm, workers, dynamic imports.
6. Fingerprint context: language, timezone, platform, canvas/webgl, device ID, experiment flags, page bootstrap variables.
7. Verification context: real sample input, real sample output, packet capture comparison, breakpoint variable snapshots.

Use mocks and hooks only to reproduce authorized local behavior. Document every mocked value and explain whether it affects the output.

## Parameter Generation

Use for: "Where does this parameter come from?", "How is sign generated?", "How is token calculated?", "Restore request parameters."

Process:

1. Search target names: `sign`, `token`, `timestamp`, `nonce`, `data`, `payload`, `encrypt`, `signature`, `X-Bogus`.
2. Search request entry points: `fetch`, `XMLHttpRequest`, `axios`, `$.ajax`, `sendBeacon`, wrappers, interceptors.
3. Find assignment points, generator functions, callers, and return-value flow.
4. Identify participating fields: path, query, body, cookies, headers, user-agent, timestamp, nonce, device fingerprint, server return value, fixed salt.
5. Restore concatenation order, sorting, case normalization, separators, empty-value handling.
6. Restore encoding, compression, hash, HMAC, encryption, Base64, and URL encoding layers.
7. Rebuild the execution context and reproduce the generator.
8. Compare with the original request: format, length, timestamp precision, nonce shape, signature output, response status, and response keywords.

Output:

```text
[Parameter]
[Generation entry]
[Call chain]
[Participating fields]
[Concatenation/sorting rules]
[Encoding/hash/encryption layers]
[Runtime context dependencies]
[Reproduction code or pseudocode]
[Verification result]
[Unknowns]
```

## Key Source Analysis

Use for: "Find the key", "Find iv", "Find salt", "Find AES key."

Process:

1. Search hardcoded strings, config objects, constant pools, environment variables, server-issued fields.
2. Search `window.xxx`, `globalThis.xxx`, `localStorage`, `sessionStorage`, `cookie`.
3. Check whether timestamp, random value, user ID, device fingerprint, or server field derives the key.
4. Check obfuscation, concatenation, reversing, Base64, Hex, URL encoding.
5. Prove it enters an encryption/decryption function as `key`, `iv`, `salt`, `message`, or `password`.
6. Verify with sample input/output. Never call a string a key just because it looks like one.

Output:

```text
[Candidate key]
[Source]
[Type] fixed / dynamic / server-issued / user-scoped / derived / obfuscated
[Related algorithm]
[Argument-passing evidence]
[Runtime required]
[Verification method]
[Confidence]
```

## Payload Decryption

Use for: "Decrypt this parameter", "Restore payload", "What is inside data?", "Open this ciphertext."

Process:

1. Confirm the ciphertext sample and request location.
2. Identify outer encoding: URL encode, Base64, Hex, JSON stringify, gzip, protobuf.
3. Identify algorithm: AES, DES/3DES, RSA, SM2/SM4, XOR, or custom.
4. Confirm `key`, `iv`, `mode`, `padding`, AAD/tag for GCM-like flows.
5. Restore the decryption flow and record the exact failure point if it does not close.
6. Verify with a sample round trip.

Output:

```text
[Ciphertext parameter]
[Encoding layers]
[Encryption algorithm]
[Mode/padding]
[Key/iv/salt]
[Decryption flow]
[Decryption result]
[Failure reason]
[Materials needed next]
```

## Library and Algorithm Identification

Search first:

- `CryptoJS`
- `crypto.subtle`
- `node:crypto`, `createHash`, `createHmac`
- `JSEncrypt`
- `node-forge`
- `jsrsasign`
- `sm-crypto`
- `crypto-js`

Common features:

- MD5: 32 hex characters.
- SHA1: 40 hex characters.
- SHA256: 64 hex characters.
- HMAC: both `key` and `message` are present.
- AES: key commonly 16/24/32 bytes; mode commonly CBC/ECB/GCM/CTR.
- RSA: PEM, public/private key markers, `BEGIN PUBLIC KEY`.
- Base64: alphabet commonly includes `A-Z a-z 0-9 + / =`.
- URL encoding: `%2F`, `%3D`, `%2B`, `%25`.
- SM2/SM3/SM4: often appears in national-crypto libraries or names.

Output:

```text
[Candidate library]
[Candidate algorithm]
[Evidence]
[Input structure]
[Output features]
[Standard or custom]
[Modification signs]
[Still needs verification]
```

## Request Replay

Use for: "Write reproduction code", "Replay with Python", "Replay with JS", "Restore the API request."

Process:

1. Define method, URL, headers, cookies, query, and body.
2. Separate fixed fields from dynamic fields.
3. Reproduce dynamic parameter generation.
4. Rebuild context; do not copy only one function.
5. Build the smallest request. Avoid batch requests.
6. Compare original and replayed requests: parameter format, length, timestamp precision, nonce, sign, response status, response keywords.

Python requirements:

- Use Python 3.12 by default.
- Create or reuse a venv before running Python.
- Install pip packages only inside the venv.
- Prefer the standard library.
- Add third-party dependencies only when they clearly reduce complexity or risk.
- Comment code enough to explain why each context mock or crypto step exists.
- Redact sensitive values.

## VM / jsVMP Path Restoration

Use for: "Analyze VM", "Analyze jsVMP", "Restore opcode", "Restore bytecode."

Process:

1. Confirm whether it is actually VM/jsVMP: large arrays, bytecode, constant pool, `while/switch` dispatch, opcode, pc/sp/stack/context/register, handler table.
2. Locate VM entry: bytecode source, constant pool source, external parameter entry, return-value flow.
3. Map opcode to handlers, stack/register operations, jumps, calls, property access, string decoding.
4. Extract only the shortest path related to the target parameter and mark irrelevant branches.
5. Instrument dynamically when needed: print opcode, handler inputs/returns, stack changes, variables before/after target generation.

Output:

```text
[Is VM/jsVMP]
[Evidence]
[VM entry]
[Bytecode/constant pool source]
[Dispatcher location]
[Key opcode/handler]
[Target parameter path]
[Restorable part]
[Still needs dynamic verification]
```

## Anti-Debugger Analysis

Use for: "Remove debugger", "Analyze anti-debugging", "DevTools freezes", "DevTools is detected."

Process:

1. Search `debugger`, `setInterval`, `setTimeout`, `Function("debugger")`, `constructor("debugger")`, `eval`, `console.clear`, `devtools`, `isOpen`.
2. Identify the trigger: timer, constructor, console detector, window-size delta, shortcut, context menu.
3. Identify side effects: pause, blank page, redirect, risk-control report, broken parameter generation.
4. For authorized local samples, propose the smallest change: comment, no-op replacement, disable the specific timer, or hook the detector.
5. Verify the page works, parameter generation is intact, DevTools can stop in business code, and no secondary anti-debugger remains.

Output:

```text
[Anti-debugger type]
[Trigger location]
[Trigger method]
[Side effects]
[Handling plan]
[Modification points]
[Verification result]
[Risks]
```

## Electron / asar / DevTools

Use for: "Remove debugger from asar", "Fingerprint browser blocks F12/Ctrl+Shift+I", "Enable DevTools", "Debug Electron app.asar."

Boundary: only handle local, owned, or user-authorized Electron/fingerprint-browser clients. Back up the original `asar` or directory before modification.

Process:

1. Identify install path, `resources`, `app.asar`, and `app.asar.unpacked`.
2. Inspect `package.json`, `main.js`, `index.js`, `background.js`, `preload.js`, renderer bundle.
3. Search `openDevTools`, `devTools`, `webPreferences`, `BrowserWindow`, `globalShortcut`, `before-input-event`, `setMenu`, `F12`, `Control+Shift+I`, `preventDefault`.
4. Search `debugger`, `Function("debugger")`, `constructor("debugger")`, `setInterval`, `devtools-detect`.
5. Identify source: main process, renderer, preload, menu, bundled obfuscated code.
6. Use the smallest change: remove shortcut blocking, restore `webContents.openDevTools()`, set `webPreferences.devTools=true`, preserve business logic.
7. Repack or replace the unpacked directory and verify DevTools startup.

Output:

```text
[Electron app path]
[asar path]
[Entry file]
[DevTools disable location]
[Shortcut blocking location]
[debugger trigger location]
[Smallest modification]
[Backup file]
[Verification method]
[Risks and rollback]
```

## remote-debugging-port Discovery

Use for: "Find remote-debugging-port", "Enable remote debugging port", "Fingerprint browser remote debugging."

Process:

1. Confirm launch mode: shortcut, script, Electron main process, child Chromium process, config file.
2. Search `remote-debugging-port`, `remoteDebuggingPort`, `debuggingPort`, `--remote-debugging-port`, `appendSwitch`, `commandLine.appendSwitch`, `spawn`, `execFile`, `child_process`, `chrome.exe`.
3. Identify source: code, config, database, command line, environment variable, random allocation.
4. Choose modification path: shortcut argument, config change, `app.commandLine.appendSwitch`, child-process argument.
5. Verify `http://127.0.0.1:<port>/json/version` and `/json/list`; confirm the listener is local-only.

## Report Contract

For complete analysis reports, include:

1. Task goal.
2. Input materials.
3. Analysis process.
4. Key code locations.
5. Parameter generation logic.
6. Key/iv/salt sources.
7. Algorithm judgment.
8. Reproduction code or pseudocode.
9. VM, anti-debugger, Electron, or remote-debugging result when relevant.
10. Verification process.
11. Conclusion.
12. Risks and unknowns.
13. Attachment text.

Attachment template:

```text
[Key functions]
[Call chain] entry -> middle function -> crypto function -> request send
[Key parameters] name / source / generation method
[Candidate key] key / iv / salt / source / confidence
[Algorithm judgment] algorithm / library / evidence
[Runtime context] window/document/navigator/localStorage/time/random/dependencies
[VM/jsVMP] entry / dispatcher / opcode / handler / target parameter path
[debugger/anti-debugger] trigger / method / handling plan / verification
[Electron asar] asar path / entry file / modification / backup
[remote-debugging-port] location / source / verification URL
[Reproduction code] JS or Python
[Verification record] input / output / comparison
```

## Response Contract

Reply in the user's language unless the user asks for English. Keep technical identifiers in English.

Prefer this order:

```text
Conclusion:

[Solution]
Concrete steps or performed changes.

[Key evidence]
Code locations, call chain, sample comparison, runtime variables.

[Why this is the answer]
Data flow, algorithm evidence, context dependencies.

[Risks and unknowns]
Authorization, freshness, environment differences, missing materials, sensitive-data risk.

[Verification]
Commands, samples, request comparison, or why verification was not possible.

[Alternatives]
1-3 optional paths and their cost.
```

## Pre-Completion Reverse Verification

Before giving a final conclusion, check:

1. Does the answer satisfy the user's goal?
2. Is the work inside the authorized scope?
3. Were function context and runtime context both considered?
4. Were encoding, compression, hash, and encryption layers checked?
5. Can sample input generate sample output?
6. Does the reproduction align with real request flow?
7. Were cookies, tokens, passwords, keys, accounts, and device IDs redacted?
8. Are risks and alternatives stated?
