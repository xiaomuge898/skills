# Report And Completion Contract

## Complete Report Contract

For complete analysis reports, include:

1. Task goal.
2. Input materials.
3. Analysis process.
4. Key code locations.
5. Reproduction mode.
6. Parameter generation logic.
7. Key/iv/salt sources.
8. Algorithm judgment.
9. Experience notes applied when relevant.
10. Reproduction code or pseudocode.
11. VM, anti-debugger, Electron, or remote-debugging result when relevant.
12. Verification process.
13. Conclusion.
14. Risks and unknowns.
15. Attachment text.

Attachment template:

```text
[Key functions]
[Call chain] entry -> middle function -> crypto function -> request send
[Key parameters] name / source / generation method
[Reproduction mode] in-site extraction / local Node service / pure algorithm port
[Experience notes] branch drift / stale runtime / URL fidelity / browser parity / false hypotheses
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
Authorization, freshness, environment differences, and missing materials.

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
7. Were relevant field notes considered: public helper vs wrapper, length drift, stale runtime, URL exactness, browser parity, and false hypotheses?
8. Were recovered cookies, tokens, passwords, keys, accounts, device IDs, signed URLs, seed material, headers, payloads, and plaintext shown as-is?
9. Are risks and alternatives stated?
