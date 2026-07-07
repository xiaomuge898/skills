# JS Runtime Environment Reconstruction

Role: Support capability for local/browser runtime parity and minimal browser-contract reconstruction.

Input: active main branch target, local exception, browser/local output mismatch, stack evidence, browser snapshots, accessed object paths, request samples.

Output: runtime gap record, gap classification, smallest contract patch, browser/local checkpoint comparison, mocked value sources, residual runtime risk.

Returns control to: the active main branch selected by `rules/orchestrator.md`.

## Skill Capsule

Name: JS Runtime Environment Reconstruction

Description: Use this branch when authorized JavaScript reverse analysis depends on rebuilding a browser-like runtime in Node, a local VM, or a replay harness. It covers DOM/BOM/Navigator/Window reconstruction, browser fingerprint APIs, challenge-cookie runtimes, signing parameter runtimes, and local/browser divergence.

Trigger this branch when any of these appear:

1. Local execution throws `ReferenceError: window/document/navigator/location is not defined`.
2. Local execution throws `TypeError: Cannot read properties of undefined`, `Illegal invocation`, or `Illegal constructor` inside DOM/BOM access.
3. Browser output and Node output differ for a signature, cookie, encrypted parameter, risk token, or device fingerprint.
4. The target checks `Object.getOwnPropertyDescriptor`, prototype chains, `instanceof`, `constructor`, `Object.keys`, `ownKeys`, `Symbol.toStringTag`, or `Function.prototype.toString`.
5. The target reads or tests `Window`, `Document`, `Navigator`, `Location`, `Screen`, `History`, `Storage`, `Canvas`, `WebGL`, `AudioContext`, `Crypto`, `Performance`, timers, events, cookies, or browser-only global aliases.
6. A first response returns dynamic HTML, meta content, inline scripts, VM code, or challenge scripts that must run locally to generate a second cookie or risk parameter.

Core principle:

```text
Reconstruct the smallest observed browser contract, not the browser.
```

Treat the browser as the oracle, the local runtime as a hypothesis, and every thrown exception or output mismatch as a failed contract checkpoint.

## Analysis Mind Model

Model the runtime as four layers:

1. Execution shell: `globalThis`, `window`, `self`, `top`, `parent`, webpack/module runtime, dynamic `eval`/`Function`, and closure imports.
2. Browser contract: DOM/BOM objects, descriptors, prototypes, constructors, native strings, events, timers, cookies, storage, and URL state.
3. Fingerprint surface: UA, language, platform, screen, viewport, timezone, canvas, WebGL, audio, crypto, performance, permissions, media, and feature probes.
4. Business output: signed parameter, cookie, encrypted payload, risk token, request header, or replayed response.

Never patch layer 4 directly when layer 1-3 evidence explains the drift. Patch the nearest missing contract, then re-check the nearest business checkpoint.

## Need-To-Reconstruct Signals

Use runtime reconstruction when:

1. The copied JS is not a pure algorithm and reads browser APIs before producing the target value.
2. The same inputs produce different output in browser and Node.
3. A challenge page returns temporary HTML/scripts and expects the client to execute them before the real request.
4. The code has anti-Node checks such as `typeof process`, `Buffer`, `global`, `__dirname`, `__filename`, or unusual global enumeration.
5. The code uses fingerprint APIs whose output enters the sign/cookie/token path.

Do not reconstruct when a pure extracted function plus its constants already reproduces the target output. In that case, prefer a pure algorithm port.

## Gap Classification

Classify the first failure before adding code:

| Symptom | Likely gap | First action |
|---|---|---|
| `ReferenceError: X is not defined` | Missing global binding | Add only that global or alias and re-run |
| `Cannot read properties of undefined (reading 'Y')` | Parent exists, child contract missing | Patch `parent.Y` with the minimal observed shape |
| `Illegal invocation` | Wrong receiver or native method binding | Bind method to the correct instance or rebuild prototype receiver |
| `Illegal constructor` expected but construction succeeds | Constructor semantics too loose | Throw unless called through the internal factory path |
| `instanceof` fails | Prototype chain wrong | Create constructor/prototype pair and instantiate through it |
| `Object.getOwnPropertyDescriptor` mismatch | Wrong owner or descriptor flags | Snapshot descriptor in browser and define it locally |
| `Function.prototype.toString.call(fn)` mismatch | Native detection | Use a native-string registry for selected functions only |
| `Object.keys`, `ownKeys`, `for...in` mismatch | Enumeration shape drift | Add enumerable keys only after observing enumeration |
| Output length/charset differs with no crash | Branch or fingerprint drift | Diff browser/local checkpoints around branch inputs |
| No output and no exception | Event/timer/callback not fired | Implement the smallest scheduler or dispatch path |
| Replay returns normal HTTP but business failure | Runtime value is plausible but wrong | Compare generated value, cookie flow, headers, and server-issued seeds |

## Browser Detection Dimensions

Check these dimensions in order of effect on the target output:

1. Global identity: `window === globalThis`, `self`, `top`, `parent`, `frames`, `global`, `process`, `Buffer`, `module`, `require`, `__dirname`, `__filename`.
2. URL state: `location.href`, `origin`, `protocol`, `host`, `hostname`, `port`, `pathname`, `search`, `hash`, `document.referrer`.
3. Navigator profile: `userAgent`, `appVersion`, `platform`, `language`, `languages`, `webdriver`, `plugins`, `mimeTypes`, `userAgentData`, `deviceMemory`, `hardwareConcurrency`, `maxTouchPoints`, `connection`.
4. Viewport and screen: `innerWidth`, `innerHeight`, `outerWidth`, `outerHeight`, `devicePixelRatio`, `screen`, `visualViewport`, scroll offsets.
5. DOM access: `createElement`, selectors, tag collections, `head`, `body`, `documentElement`, attributes, style nodes, script/meta/base tags.
6. State APIs: `document.cookie`, `localStorage`, `sessionStorage`, `history`, cache-like element stores.
7. Scheduling and events: `setTimeout`, `setInterval`, `requestAnimationFrame`, `addEventListener`, `removeEventListener`, `dispatchEvent`.
8. Fingerprints: canvas, WebGL, audio, crypto randomness, performance timing, timezone, `Intl`, permissions, media devices.
9. Native shape: descriptors, prototype ownership, constructor behavior, `Symbol.toStringTag`, native `toString`, `length`, `name`, and exception text.

## Object Reconstruction Strategy

### Window

1. Start with identity aliases: `window`, `self`, `top`, `parent`, `globalThis`.
2. Hide Node leakage only when the target checks for it. Removing `process` or `Buffer` can break dependencies.
3. Add constructors and globals lazily after stack evidence: `XMLHttpRequest`, `HTMLElement`, `HTMLCanvasElement`, `HTMLScriptElement`, `HTMLIFrameElement`, `Event`, `MouseEvent`, `TouchEvent`.
4. Do not fake a full `window` key list unless the target enumerates it. Large static `ownKeys` lists are slow and stale.

### Document And Elements

1. Implement element factories by tag name. `canvas`, `script`, `meta`, `style`, `div`, `form`, `iframe`, and `a` usually need different shapes.
2. Return real-looking collections for `getElementsByTagName`, `querySelectorAll`, and related APIs only for selectors observed in the target.
3. Put shared methods on element prototypes when the browser does; keep element-specific values on instances.
4. `document.cookie` should preserve browser-like set/get semantics for the current task, not just store a string blindly.
5. Style-loader and challenge scripts often require `document.head.appendChild`, `createElement("style")`, `script.parentNode.removeChild`, and `meta.getAttribute("r")`.

### Navigator

1. Keep UA, platform, language, viewport, and plugins coherent. A Chrome UA with impossible plugin/mime/platform values creates branch drift.
2. Put common navigator properties on `Navigator.prototype` when descriptor checks are present.
3. `Navigator`, `PluginArray`, and `MimeTypeArray` constructors should usually throw `Illegal constructor` unless called by an internal factory.
4. Use `Symbol.toStringTag` for `[object Navigator]`, `[object PluginArray]`, and similar checks.
5. Set `webdriver` from browser evidence. Defaulting to `false` is common, but it is still a claimed fingerprint value.

### Location

1. Build `location` from the exact request URL.
2. Keep `href`, `origin`, `protocol`, `host`, `hostname`, `port`, `pathname`, `search`, and `hash` coherent.
3. If `href` is assigned, recompute the dependent fields.
4. Implement `toString()` as `href` when observed.

### Screen And Viewport

1. Use one coherent profile for `screen`, `visualViewport`, `inner*`, `outer*`, scroll offsets, and `devicePixelRatio`.
2. Avoid random dimensions. If values affect output, record them from the browser session.

### History

1. Start with `length`, `state`, `pushState`, `replaceState`, `back`, `forward`, and `go`.
2. Only mutate URL/history state if the target observes it after navigation-like calls.

### Storage

1. `getItem` returns `null` for missing keys, not `undefined`.
2. `setItem` stringifies values.
3. Implement `removeItem`, `clear`, `key(index)`, and `length`.
4. Add property-style access only if the target uses it.

### Canvas

1. If the target only checks method existence, no-op 2D methods are enough.
2. If canvas output enters a hash/sign/fingerprint, do not invent output. Capture a deterministic `toDataURL`, `getImageData`, or hash from the browser profile and reproduce that contract.
3. Implement the observed context methods only: common calls include `fillRect`, `fillText`, `measureText`, `arc`, `isPointInPath`, `getImageData`, `putImageData`, `toDataURL`.

### WebGL

1. Start with `getContext("webgl")` and `getContext("webgl2")` only when called.
2. Implement `getSupportedExtensions`, `getExtension`, `getParameter`, `getContextAttributes`, `getShaderPrecisionFormat`, shader/program no-ops, and vendor/renderer values only as observed.
3. Keep `WEBGL_debug_renderer_info` behavior consistent with the chosen browser profile.

### AudioContext

1. Reconstruct only when audio output affects the target value.
2. Provide coherent `sampleRate`, `destination`, `createOscillator`, `createAnalyser`, `createDynamicsCompressor`, `createGain`, and `OfflineAudioContext` behavior as observed.
3. For audio fingerprints, replay a captured deterministic fingerprint instead of approximating DSP.

### Crypto

1. Prefer native `crypto.webcrypto` or `node:crypto` for standard APIs.
2. `crypto.getRandomValues` must respect typed-array mutation semantics.
3. If randomness affects a compared output, control or record entropy; otherwise every verification run will drift.

### Performance

1. `performance.now()` must be monotonic and coherent with `Date`.
2. Add `timing`, `navigation`, `getEntries`, `getEntriesByType`, `mark`, and `measure` only when observed.
3. If anti-bot code checks timing jitter, record browser values or use a deterministic timing profile.

### Events And Timers

1. A no-op timer is acceptable only when callback execution is irrelevant.
2. If output depends on async completion, implement a small task scheduler and drain it deterministically.
3. `addEventListener` should store listeners by type; `dispatchEvent` should call them with a minimal event object.

## Prototype, Descriptor, Getter/Setter, And Native Shape Rules

1. Before patching a property that is inspected, snapshot in the browser:

```text
Object.getOwnPropertyDescriptor(owner, key)
Object.getPrototypeOf(obj)
obj instanceof Constructor
Object.prototype.toString.call(obj)
Function.prototype.toString.call(fn)
Object.keys(obj)
Reflect.ownKeys(obj)
```

2. Put a property on the same owner as the browser: own object vs prototype matters.
3. Use getters and setters when the browser exposes accessors, especially for `location.href`, `document.cookie`, storage length, and computed element fields.
4. Use `Object.defineProperty` for enumerability/configurability/writability instead of plain assignment when checks exist.
5. Native-string masking should use a registry such as `WeakMap<Function, string>` and should be applied only to selected functions. A global fake can break debugging and create new detections.
6. Constructors for DOM/BOM native classes often have browser-specific error behavior. Reproduce it only when the target tests it.

## Node Browser-Simulation Architecture

Prefer a modular runtime over one large environment file:

```text
runtime/
  core-native        native string registry, descriptors, global aliases
  profiles           browser URL, UA, screen, language, timezone, plugins
  dom                document, element factory, collections, cookie
  navigator          navigator, plugin/mime arrays, userAgentData
  location-history   location, URL recomputation, history state
  storage            localStorage/sessionStorage/cookie state
  graphics           canvas, WebGL, audio fingerprints
  scheduling         events, timers, animation frames
  time-random        Date, performance, Math.random, crypto entropy
  trace              proxy logger, stack capture, browser/local diff
```

For small tasks, collapse modules into one file, but keep the conceptual boundaries. This prevents duplicate implementations and makes later target-specific profiles replaceable.

## Priority And Minimal Implementation

Patch in this order:

1. Hard crashes that block execution.
2. Branch conditions that decide whether target logic runs.
3. Values that directly enter sign/cookie/token/encryption output.
4. Descriptor, prototype, constructor, and native-string checks.
5. Enumeration and global-shape checks.
6. Cosmetic APIs that do not affect the target output.

Stop when the target value and nearest browser checkpoint match. More environment code is usually more attack surface, more performance cost, and more stale fingerprint risk.

## Performance Controls

1. Use proxy tracing during discovery, then disable or narrow it for production replay.
2. Log sampled access paths, not every repeated hot-loop call.
3. Lazy-create elements and fingerprint objects.
4. Avoid JSDOM when only a few APIs are needed; use it when the target is DOM-heavy and does not rely on exact fingerprint shape.
5. Avoid huge `ownKeys` lists unless enumeration is a proven target check.
6. Freeze deterministic profiles for Date/random/screen/navigator values during verification.

## Verification Rules

A runtime reconstruction is not verified until these pass:

1. The original exception is gone.
2. The nearest browser/local checkpoint matches: branch result, intermediate variable, generated cookie, sign, token, or encrypted payload.
3. Output format matches: length, charset, prefix/suffix, timestamp precision, and randomness policy.
4. Request replay matches server behavior or expected business response.
5. Mocked values are documented with their source: browser snapshot, response material, controlled seed, or deliberate placeholder.
6. No hidden dependency on live browser/CDP remains if the claimed mode is local Node replay.

## Stack-To-Fix Triage

Use stack and error text to choose the next patch:

```text
ReferenceError: document is not defined
  -> create global document, then re-run.

Cannot read properties of undefined (reading 'createElement')
  -> parent object exists but document/createElement owner path is wrong.

document.createElement(...).getContext is not a function
  -> tag-specific element factory missing canvas contract.

Illegal invocation
  -> method called with wrong receiver; bind it or rebuild prototype/instance.

Illegal constructor
  -> target expects native constructor guard; add internal factory path.

Function.prototype.toString.call(fn) mismatch
  -> register native-like string for that function.

Object.getOwnPropertyDescriptor(navigator, "userAgent") mismatch
  -> property is on wrong owner or descriptor flags are wrong.

Object.prototype.toString.call(obj) mismatch
  -> set Symbol.toStringTag or prototype chain correctly.

No crash, output mismatch
  -> diff branch inputs and fingerprint values before crypto/sign call.
```

## Common Reconstruction Templates

### Runtime Gap Record

```text
[Gap]
error / branch drift / output mismatch

[Evidence]
stack frame, accessed path, browser snapshot, local value

[Classification]
global / property / descriptor / prototype / native string / event / fingerprint / seed

[Patch]
smallest contract added

[Verification]
nearest checkpoint before/after, target output before/after

[Residual risk]
stale fingerprint, incomplete API, target-specific placeholder, async not modeled
```

### Proxy Discovery Loop

Use a proxy only as an instrument:

1. Wrap `window`, `document`, `navigator`, `location`, `history`, `screen`, storage, and target-specific globals.
2. Trap `get`, `set`, `has`, `deleteProperty`, `ownKeys`, `getOwnPropertyDescriptor`, `defineProperty`, `getPrototypeOf`, `apply`, and `construct` as needed.
3. Log object path, operation, key, value type, and stack when the log volume is manageable.
4. Convert high-frequency observed paths into stable mocks.
5. Remove broad proxy tracing from the final replay harness unless it is still needed for diagnostics.

### Browser Snapshot To Local Descriptor

1. In browser, snapshot descriptor/prototype/native string for the inspected property.
2. In local runtime, define the property on the same owner with the same descriptor class: data vs accessor.
3. Verify descriptor equality for only the checked fields.

### Native Mask Registry

Use a selected-function registry:

```text
realToString = Function.prototype.toString
nativeMap.set(fn, "function name() { [native code] }")
Function.prototype.toString = function () {
  return nativeMap.get(this) || realToString.call(this)
}
```

Do not replace every function string blindly.

### Browser-Local Parity Checkpoint

For every important branch:

```text
[Expression] navigator.webdriver
[Browser] false
[Local] false
[Affects] anti-automation branch before sign()
```

Checkpoint near the target output first, then move outward only if it differs.

## Extracted Good Practices

The reviewed environment examples contain reusable practices:

1. Proxy-first discovery: trace accesses before guessing the full browser surface.
2. Descriptor/prototype awareness: own property vs prototype property changes detection results.
3. Native-string preservation: save the original `Function.prototype.toString` and return native-like strings only for registered functions.
4. Illegal-constructor guards: native DOM/BOM constructors should not be freely constructible unless evidence says otherwise.
5. URL-coherent `location`: derive fields from one URL source instead of hardcoding unrelated fields.
6. Challenge material workflow: fetch first response, extract meta/script/VM material, execute locally with a minimal environment, then replay with generated cookie.
7. JSDOM as a middle path: useful for DOM-heavy code, but insufficient for precise fingerprint or anti-automation checks.
8. Deterministic time/random: freeze or control seeds during verification when output depends on them.

## Duplicate Implementations To Unify

When maintaining a reusable environment harness, collapse these repeated patterns:

1. Multiple proxy loggers -> one `RuntimeTraceProxy` with selectable traps and stack sampling.
2. Multiple `document.createElement` stubs -> one `ElementFactory` keyed by tag and profile.
3. Multiple navigator/plugin literals -> one `NavigatorProfile` plus `PluginArray`/`MimeTypeArray` factories.
4. Multiple hardcoded `location` objects -> one `LocationProfile.fromUrl(url)`.
5. Multiple storage/cookie snippets -> one `StateStore` with browser-like string/null semantics.
6. Multiple no-op timer/event snippets -> one deterministic `TaskScheduler`.
7. Repeated challenge environment prefixes -> one `ChallengeRuntimeProfile` that accepts meta content, script tags, URL, UA, and cookie state.

## Higher-Level Improvements

Prefer these upgrades for future tooling:

1. Browser snapshot generator: collect descriptors, prototypes, native strings, own keys, toString tags, UA/screen/location/storage state for selected paths.
2. Access-log-to-stub generator: convert proxy traces into a minimal environment skeleton.
3. Differential runner: evaluate the same checkpoint expressions in browser and local Node, then report drift.
4. Lazy runtime profiles: load heavy Canvas/WebGL/Audio only when those APIs are actually accessed.
5. Native masking registry: centralize all native-like function strings and constructor guards.
6. Deterministic seed manager: record Date, performance, Math.random, and crypto entropy policy.
7. Mode switch: choose pure algorithm port, local Node environment, or in-browser extraction before adding mocks.

## Missing Future Modules

If a target uses these surfaces, add dedicated modules instead of one-off stubs:

1. `AudioContext` and `OfflineAudioContext` fingerprint pipelines.
2. WebGL2 and WebGPU/GPU adapter fingerprint surfaces.
3. Permissions, Notification, Clipboard, Battery, NetworkInformation, and MediaDevices.
4. IndexedDB, CacheStorage, ServiceWorker, Worker, SharedWorker, Blob, and `URL.createObjectURL`.
5. FontFace, CSSStyleSheet, computed style, DOMParser, XMLSerializer, Range, Selection, MutationObserver, ResizeObserver, IntersectionObserver.
6. `Intl`, timezone, locale, calendar, and number/date formatting profiles.
7. RTCPeerConnection/WebRTC network fingerprint APIs.
8. PerformanceObserver, resource timing, navigation timing, long tasks, and memory.
9. Exact `MimeTypeArray`/`PluginArray` behavior, iterator shape, and named properties.
10. Fetch/XHR wrappers with header/cookie/referrer behavior.
11. WebAssembly imports and JS/wasm bridge tracing.

## Practical Workflow

Use this workflow for real signing, risk parameter, and challenge-cookie cases:

1. Define the target output: sign, token, encrypted payload, cookie, header, or replay success.
2. Capture browser evidence: request sample, response material, target output, stack frames, breakpoint variables, URL, UA, cookies, and relevant fingerprint values.
3. Select reproduction mode:
   - In-site extraction when the page already has the correct environment.
   - Local Node service when browser APIs are needed but can be reconstructed.
   - Pure algorithm port when no browser contract affects output.
4. Run the local code with tracing and record the first gap.
5. Classify the gap and patch the smallest contract.
6. Verify the nearest checkpoint before adding another API.
7. Repeat until target output matches browser or replay succeeds.
8. Remove broad tracing, keep deterministic profiles, and document mocked values.
9. Report remaining uncertainty: stale fingerprint values, target-specific placeholders, unmodeled async behavior, or missing browser samples.

For challenge-cookie flows:

1. First request obtains server seed material: HTML, meta content, inline script, external challenge script, and first cookie.
2. The local runtime injects only the seed material and observed browser contract needed by the challenge VM.
3. The generated cookie is compared to browser format and used in the second request.
4. If the second request fails, diff cookie format, URL fidelity, UA, referrer, first-cookie continuity, and dynamic timestamp/random sources before adding more DOM APIs.

For signature/risk-parameter flows:

1. Hook the function boundary closest to the generated value.
2. Compare browser/local inputs to that boundary.
3. Patch only the runtime values that differ and affect the boundary.
4. Verify with a fixed sample request before generalizing.
