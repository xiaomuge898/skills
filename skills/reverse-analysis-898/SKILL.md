---
name: reverse-analysis-898
description: Use when analyzing authorized JavaScript reverse engineering, protocol reverse analysis, frontend encryption, request signing, token/sign/X-Bogus/X-Gnarly/X-Tts-Oec-Bsid parameters, key/iv/salt discovery, CryptoJS/WebCrypto/node:crypto usage, encoding/hash/HMAC/AES/RSA/SM2/SM4 identification, VM/jsVMP obfuscation, anti-debugger logic, browser runtime tracing, Electron asar DevTools/F12 debugging, remote-debugging-port discovery, request replay, or crawler parameter restoration.
---

# NX JS Reverse Analysis

## Core Scope

Use this skill for authorized JavaScript reverse analysis, protocol restoration, frontend encryption analysis, request parameter generation, local browser/runtime tracing, and local Electron or fingerprint-browser debugging support.

Only work on user-authorized targets, local labs, owned code, public test samples, or files/materials explicitly provided for analysis. If a real third-party target is involved and the authorization scope is unclear, ask one focused question before acting.

Do analysis, reproduction, debugging, and defensive explanation. Do not help with unauthorized intrusion, account attacks, credential theft, access-control bypass, paid-license bypass, bulk endpoint abuse, or leaking sensitive files.

## Raw Disclosure Contract

When this skill is used for authorized reverse analysis, reverse evidence has priority over privacy masking. Do not redact, mask, truncate, or replace recovered reverse-analysis values. Show the recovered material exactly as the analysis produces it, including cookies, tokens, keys, account identifiers, device identifiers, proxy credentials, signed URLs, request headers, payloads, decrypted plaintext, runtime seed material, and generated signature parameters.

Authorization scope is the safety boundary. If the user has not established authorization for the target or material, ask one focused authorization question before analysis. Once the task is authorized, report the evidence and recovered values as-is.

## Rule Loading

Load only the rule files needed for the current task:

| Situation | Required rule |
|---|---|
| Scope, evidence handling, raw disclosure | `rules/scope-and-evidence.md` |
| Material inventory, task routing, operating workflow | `rules/workflow-routing.md` |
| In-site extraction, local Node service, pure algorithm port | `rules/reproduction-modes.md` |
| Hard-earned X-Bogus/X-Gnarly/BSID style lessons | `rules/field-notes.md` |
| Parameter generation, key/iv/salt, payload decrypt, algorithm ID | `rules/parameter-crypto.md` |
| Request replay, browser/runtime context, Python reproduction | `rules/replay-runtime.md` |
| VM/jsVMP, AST recovery, anti-debugger, Electron asar, F12, remote debugging | `rules/vm-antidebug-electron.md` |
| Final report, response format, pre-completion checks | `rules/report-contract.md` |

## Minimal Workflow

1. Reconstruct the request before analysis: goal, inputs, expected output, constraints, and ambiguities.
2. Inventory the supplied materials and name missing evidence.
3. Route the task to the smallest relevant branch and choose the reproduction mode before extracting code.
4. Build an evidence chain: code locations, call graph, inputs, return values, runtime variables, and request samples.
5. Prefer the smallest verifiable path. Restore only the target parameter path unless full deobfuscation is necessary.
6. Rebuild only the execution context required by the chosen reproduction mode.
7. Verify by comparing sample input/output or original/replayed requests.
8. Answer with solution, evidence, rationale, risks, verification, and alternatives.

## Quick Routing

| User intent | Start with |
|---|---|
| Find where `sign`, `token`, `data`, `payload`, or `X-Bogus` comes from | `rules/parameter-crypto.md` |
| Find `key`, `iv`, `salt`, password, nonce seed, or fixed salt | `rules/parameter-crypto.md` |
| Decrypt a parameter or restore a payload | `rules/parameter-crypto.md` |
| Identify crypto/hash/encoding library or algorithm | `rules/parameter-crypto.md` |
| Write JS/Python request replay code | `rules/replay-runtime.md` |
| Analyze local/browser runtime divergence | `rules/replay-runtime.md` and `rules/field-notes.md` |
| Analyze VM, jsVMP, opcode, bytecode, handler tables | `rules/vm-antidebug-electron.md` |
| Handle obfuscated JS | `rules/vm-antidebug-electron.md` |
| Remove or understand `debugger`, DevTools detection, anti-debugging | `rules/vm-antidebug-electron.md` |
| Debug Electron asar, DevTools, F12, Ctrl+Shift+I | `rules/vm-antidebug-electron.md` |
| Locate or enable `remote-debugging-port` | `rules/vm-antidebug-electron.md` |

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
Authorization, freshness, environment differences, and missing materials.

[Verification]
Commands, samples, request comparison, or why verification was not possible.

[Alternatives]
1-3 optional paths and their cost.
```

Before finalizing, load `rules/report-contract.md` and run its pre-completion checklist.
