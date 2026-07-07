# JavaScript Reverse Engineering Standard

Apply these instructions after applying `reverse-analysis-898` scope rules and raw disclosure contract. Do not redefine, narrow, or override that skill's authorization, evidence, or reporting behavior here.

## Operating Principles

- Use `reverse-analysis-898` as the source of truth for scope and raw disclosure before analysis.
- Preserve raw evidence: original JS, HAR, request, response, stack trace, cookie source, runtime values, and timestamps according to the active `reverse-analysis-898` contract.
- Do not guess cryptographic algorithms from names alone. Require code, runtime arguments, constants, output shape, or sample pairs.
- Prefer evidence from the real runtime over static assumptions.
- Keep reproduction mode explicit: in-site execution, local Node service, or pure algorithm port.

## Interface Analysis Flow

1. Identify the target endpoint:
   - URL
   - method
   - headers
   - query
   - body
   - response status and business code
2. Identify dynamic fields:
   - timestamp
   - nonce
   - token
   - signature
   - device/browser fingerprint
   - encrypted payload
3. Compare at least two successful requests:
   - stable fields
   - changing fields
   - request order
   - encoding differences
4. Remove or alter one field at a time to determine necessity.
5. Record exact evidence for each conclusion.

## Packet Capture Flow

- Capture with browser DevTools, CDP, proxy, or local logs.
- Save raw request and response before transforming them.
- Track initiator stack, source map availability, script URL, and timing.
- Preserve original query encoding and parameter order when signatures depend on canonicalization.
- Do not rebuild signed URLs with generic query serializers unless you have proven canonicalization rules.

## Parameter Location Flow

1. Search static code for field names and nearby literals.
2. Hook request APIs:
   - `fetch`
   - `XMLHttpRequest`
   - `sendBeacon`
   - WebSocket send
3. Hook encoding and crypto APIs:
   - `btoa`, `atob`, `TextEncoder`, `TextDecoder`
   - `crypto.subtle`
   - `CryptoJS`
   - `WebAssembly`
4. Set breakpoints on request construction and assignment points.
5. Walk backward from final request to the generator function.
6. Extract the minimum dependency chain required for the target parameter.

## Encryption Algorithm Analysis

- Identify library or primitive from imports, constants, block sizes, output length, and runtime calls.
- Capture key, IV/nonce, salt, mode, padding, auth tag, input bytes, and output bytes.
- Distinguish encoding, compression, hashing, signing, and encryption.
- Require sample pairs before claiming a pure port is correct.
- Verify with round-trip tests or request acceptance.

## Automation Reproduction Modes

Choose one mode and state the trade-off:

| Mode | Use when | Rule |
|---|---|---|
| In-site execution | The page already has the full runtime state | Call the target function inside the browser context and avoid unnecessary environment completion |
| Local Node service | JS can run locally with limited environment shims | Expose a narrow HTTP/CLI wrapper and test against captured samples |
| Pure algorithm port | The algorithm and inputs are fully understood | Port to Python/JS with sample-pair verification |

Do not claim pure algorithm recovery when the solution still executes extracted SDK/runtime code.

## Obfuscation Handling

- Preserve the original obfuscated file.
- Build a call graph from the target output backward.
- Recover string arrays, computed properties, wrapper functions, and control-flow flattening only along the target path.
- If obfuscation is encountered, try using AST to recover the obfuscated code.
- Apply one AST transform at a time and verify syntax after each transform.
- Do not beautify the whole bundle unless it helps the target path.

## AST Analysis Rules

- Use Babel, Acorn, Recast, SWC, or an existing project parser.
- Keep transformations reversible or checkpointed.
- Validate generated code with syntax checks.
- Compare runtime outputs before and after transformation.
- Rename symbols only after the call path is stable.
- Record each transformation: purpose, input pattern, output pattern, verification.

## JS VM / jsVMP Flow

- Locate dispatcher, opcode table, handler table, constant pool, stack model, and virtual registers.
- Trace only opcodes used by the target parameter first.
- Log opcode sequence, operands, stack changes, and external calls.
- Recover semantics incrementally.
- Stop full devirtualization if a smaller extraction reproduces the target correctly.

## Output Contract

Report:

```text
Target endpoint:
Dynamic parameters:
Evidence chain:
Generator location:
Algorithm or runtime mode:
Reproduction steps:
Verification:
Risks:
Alternatives:
```
