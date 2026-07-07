# Browser Environment Completion Standard

Apply these instructions when extracted browser JavaScript fails outside the browser or requires environment shims.

## Priority Dependencies

Analyze these browser dependencies first:

- `window`
- `document`
- `navigator`
- `location`
- `cookie`
- `localStorage`
- `sessionStorage`
- `canvas`
- `crypto`
- `WebSocket`
- `XMLHttpRequest`
- `fetch`
- `performance`
- `screen`
- `history`
- `indexedDB`
- `crypto.subtle`
- WebGL
- AudioContext
- TouchEvent and pointer APIs

## Completion Workflow

1. Run the target code in the closest available real browser context.
2. Capture missing globals, thrown errors, branch decisions, and accessed properties.
3. Classify each dependency:
   - required for target output
   - fingerprint noise
   - anti-debugging
   - network side effect
   - storage side effect
4. Stub only what the target path actually reads.
5. Match browser behavior, not only property names.
6. Keep shims narrow and documented.
7. Validate with captured sample inputs and expected outputs.

## Environment Parity Rules

- Prefer real browser values captured by CDP over guessed constants.
- Keep native-like function shape only when the target checks source strings or prototypes.
- Preserve property descriptors when code checks enumerability, configurability, getters, setters, or prototypes.
- Match error behavior. Some browser APIs throw in specific environments.
- Do not add `navigator.connection`, plugins, WebGL, touch, or canvas behavior unless evidence shows the target reads it.
- Avoid over-completing the environment. Extra properties can trigger different fingerprint branches.

## Cookie and Storage

- Distinguish `document.cookie` string behavior from cookie jar APIs.
- Preserve exact cookie names and separators when signatures depend on them.
- For storage, implement `getItem`, `setItem`, `removeItem`, `clear`, `key`, and `length` only if used.
- Avoid logging full cookie values unless the active reverse-engineering output contract explicitly permits raw evidence.

## Canvas and WebGL

- Determine whether canvas is used for fingerprinting, rendering, hashing, or feature checks.
- Implement only accessed methods first.
- For WebGL, capture vendor, renderer, supported extensions, precision formats, and parameter values from the real browser when needed.
- Verify output length and hash/signature changes after each shim.

## Crypto

- Identify whether code uses WebCrypto, Node crypto, CryptoJS, wasm, or custom primitives.
- Preserve byte encoding exactly: UTF-8, UTF-16, ArrayBuffer, WordArray, Base64, Hex, URL-safe variants.
- Do not replace cryptographic operations with placeholder values unless the task is explicitly a local scaffold.

## WebSocket and Network APIs

- Hook send and receive paths when WebSocket data affects signatures or tokens.
- Preserve message order and binary/text distinction.
- Add timeouts to automation scripts.
- Avoid uncontrolled external requests during local reproduction unless the active `reverse-analysis-898` scope rules permit them.

## Verification

Verify environment completion with:

- syntax check
- local reproduction sample
- captured browser output comparison
- real request acceptance when permitted by the active `reverse-analysis-898` scope rules
- negative check proving no stale CDP or stale seed was used
