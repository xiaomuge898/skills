# Field Notes From Hard Reverse Work

Use these as judgement prompts, not as a fixed template.

- A public signing API may be a decoy or partial path. First prove whether the target request uses a public helper, an XHR/fetch wrapper, an interceptor, or a VM path.
- Multiple parameters that appear together should be treated as one runtime event until evidence proves otherwise. `X-Bogus`, `X-Gnarly`, `X-Tts-Oec-Bsid`, `msToken`, body hash, and cookies can fail when produced from mismatched snapshots.
- Length drift is a real clue. A local signature length that differs from page runtime length usually means a branch, seed, padding, runtime config, or environment probe diverged.
- A successful local VM run is not automatically pure algorithm restoration. If it executes copied SDK, VM bytecode, unisec runtime, wasm, or page-derived seed, label it as local runtime execution.
- CDP is an oscilloscope: capture runtime variables, compare page and local VM checkpoints, export runtime seed material, or abort wrapper requests before the network. It is not proof of pure local restoration.
- Do not keep stale CDP defaults in paths that are supposed to be local. A stale `DEFAULT_CDP_WS`, `CDP_WS`, or hidden `--seed-from-cdp` branch can make a local result secretly browser-dependent.
- Signed URLs are fragile byte strings. Re-parsing and rebuilding them after signing can corrupt `+`, `/`, `=`, `%`, custom alphabets, ordering, or empty values.
- Browser parity bugs are often tiny and decisive: `document.createEvent('TouchEvent')` errors, missing Canvas 2D drawing APIs, exposed Node globals, or a guessed `navigator.connection` can change branches.
- Do not add browser mocks by guessing. Add a field only when static code or runtime tracing proves it is read, then compare page and local values at the nearest internal checkpoint.
- HTTP 200 can still be a failed replay. Treat business code, risk code, empty data, slider state, and response keywords as verification evidence.
- When a false hypothesis appears, keep it as a rule. Example: not every newer cookie value belongs inside every saved seed or closure.
- When a working scheme depends on a local seed package, record how to refresh it and what script consumes it, but keep seed export and signing as different jobs.
- When this skill is used for authorized reverse analysis, reverse evidence is more important than privacy cleanup. Preserve and show exact recovered values when they prove the chain.
