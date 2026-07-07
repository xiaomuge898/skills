# Reverse Analysis Orchestrator

This file is the execution controller for `reverse-analysis-898`. It decides phase order, branch ownership, support-capability calls, verification gates, and report shape. Do not use the branch rules as a flat checklist.

## Core Principle

Run a state machine, not a pile of modules.

Every task moves through these states:

```text
Scope gate
 -> Evidence intake
 -> Target definition
 -> Reproduction mode decision
 -> Main branch selection
 -> Support capability calls as needed
 -> Hypothesis validation loop when evidence is uncertain
 -> Verification
 -> Trace-driven report
```

## Mandatory Entry Order

1. Load `rules/scope-and-evidence.md`.
2. If authorization is unclear for a real third-party target, ask one focused authorization question before technical analysis.
3. Preserve the Raw Disclosure Contract exactly as written in `scope-and-evidence.md`.
4. Load `rules/workflow-routing.md` for material inventory and target classification.
5. Load `rules/reproduction-modes.md` before extracting or porting code.
6. Select one main branch.
7. Call support capabilities only when evidence triggers them.
8. Load `rules/report-contract.md` before finalizing.

## Main Branches

Main branch rules own the user's target output. Choose the narrowest branch that can answer the target.

| Main branch | Rule file | Owns |
|---|---|---|
| Parameter, key, payload, algorithm | `rules/parameter-crypto.md` | `sign`, `token`, `payload`, `data`, key/iv/salt, hash/HMAC/encryption identification |
| Request replay and reproduction code | `rules/replay-runtime.md` | JS/Python replay, final request shape, generated request comparison |
| VM/jsVMP path restoration | `rules/vm-antidebug-electron.md` | VM entry, bytecode, opcode handlers, stack/register path |
| Anti-debugger, Electron, remote debugging | `rules/vm-antidebug-electron.md` | debugger traps, DevTools/F12, asar, remote-debugging-port |

## Support Capabilities

Support capabilities do not own the final answer by default. They return evidence, restored code paths, runtime patches, or rejected hypotheses to the active main branch.

| Support capability | Rule file | Trigger | Returns control to |
|---|---|---|---|
| Obfuscation and AST deobfuscation | `rules/js-obfuscation-ast-deobfuscation.md` | string arrays, decoder wrappers, object proxies, computed properties, control-flow flattening, dead code, `eval`/`Function` | active main branch |
| Runtime environment reconstruction | `rules/js-runtime-environment-reconstruction.md` | browser/Node mismatch, DOM/BOM/Navigator/Canvas/WebGL/Audio/Crypto/Performance gaps, descriptor/prototype/native-shape checks | active main branch |
| Hypothesis-Driven Validation | `rules/workflow-routing.md` | unclear algorithm, parameter source, runtime dependency, VM structure, obfuscation role, or conflicting evidence | active main branch |
| Field notes | `rules/field-notes.md` | X-Bogus/X-Gnarly/BSID style drift, stale CDP dependency, signed URL fidelity, browser parity, false hypotheses | active main branch or report |

## Execution State Machine

### 1. Scope Gate

Input:

- User request.
- Target description or supplied files.

Actions:

- Apply `scope-and-evidence.md`.
- Confirm the task is authorized, local, owned, public test material, or explicitly supplied by the user.
- Keep recovered reverse-analysis evidence unmasked inside authorized scope.

Output:

- Authorized / blocked / needs one focused authorization question.

### 2. Evidence Intake

Input:

- JavaScript, HTML, HAR, curl, request/response samples, runtime logs, Electron files, browser symptoms, or user-provided artifacts.

Actions:

- Use `workflow-routing.md` for the inventory.
- Name present and missing evidence.
- Do not deep-read every file before defining the target output.

Output:

- Evidence inventory.
- Missing material list.
- Initial target candidates.

### 3. Target Definition

Define exactly one primary target before selecting a branch:

- generated parameter
- key/iv/salt source
- decrypted payload
- algorithm identification
- replayed request
- VM target path
- anti-debugger behavior
- Electron/remote-debugging switch

If several protected parameters appear together in one request, treat them as one runtime event until evidence proves they are independent.

### 4. Reproduction Mode Decision

Load `reproduction-modes.md` and choose:

- in-site extraction
- local Node service
- pure algorithm port

Do not claim pure algorithm restoration if the solution still runs copied SDK/runtime code, VM bytecode, wasm, page-derived seeds, or browser state.

### 5. Main Branch Selection

Route by target ownership:

- Parameter/key/payload/algorithm -> `parameter-crypto.md`.
- Replay code or request acceptance -> `replay-runtime.md`.
- VM/opcode/bytecode/handler table -> `vm-antidebug-electron.md`.
- debugger, DevTools, F12, asar, remote-debugging-port -> `vm-antidebug-electron.md`.

Only one main branch leads at a time. Other branch rules can be consulted, but the active branch owns the final target and verification.

### 6. Support Capability Calls

Call support capabilities from the active branch when evidence requires them:

- Obfuscated JS before a real VM is proven -> AST support first.
- Browser object gap or local/browser output mismatch -> runtime reconstruction.
- Conflicting or insufficient evidence -> Hypothesis-Driven Validation.
- Known hard reverse failure pattern -> field notes.

Each support call must produce a short record:

```text
[Support capability]
[Trigger evidence]
[Action taken]
[Output returned to main branch]
[Residual risk]
```

### 7. Hypothesis Loop

Use the hypothesis loop when evidence cannot choose a path:

```text
Observe -> propose hypotheses -> run minimal verification experiment -> judge result -> revise hypothesis -> continue
```

Rejected hypotheses must stop influencing the active path unless new evidence revives them.

### 8. Verification

Verification must match the chosen mode and target:

- sample pair comparison
- browser/local checkpoint comparison
- generated value format, length, charset, timestamp, nonce, randomness policy
- original/replayed request comparison
- server or business response check
- AST transform syntax and runtime equivalence
- VM opcode/path evidence
- DevTools or remote-debugging URL check

### 9. Trace-Driven Report

Load `report-contract.md`. Include required report sections and only the conditional sections for modules actually used.

Do not emit a full-module report just because a rule file exists.

## Stop Conditions

Stop and ask or report insufficiency when:

- Authorization is unclear.
- The target output is not defined.
- Required samples or runtime evidence are missing.
- A branch cannot be verified with current material.
- A support capability would require broad guessing instead of observed evidence.

## Common Architecture Mistakes

| Mistake | Correct behavior |
|---|---|
| Adding every new topic to `SKILL.md` Quick Routing | Add the topic to this orchestrator as a main branch or support capability |
| Treating AST deobfuscation as the final answer | Return restored target path evidence to the active main branch |
| Building a browser mock before choosing reproduction mode | Choose mode first, then patch only observed runtime gaps |
| Entering VM rules for ordinary JavaScript Obfuscator code | Use AST support until opcode/bytecode/handler evidence proves VM |
| Reporting every possible section | Report the actual execution trace |
