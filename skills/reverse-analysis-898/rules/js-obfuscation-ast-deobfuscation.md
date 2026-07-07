# JavaScript Obfuscation And AST Deobfuscation Rules

Role: Support capability for obfuscation recognition and target-path AST deobfuscation.

Input: active main branch target, obfuscated JavaScript, parser errors, decoder fingerprints, runtime-generated code, transform scripts, sample outputs.

Output: obfuscation fingerprint, chosen recovery method, transform order, changed counts, restored target path evidence, verification result, residual obfuscation.

Returns control to: the active main branch selected by `rules/orchestrator.md`.

Use this rule when the material contains obfuscated JavaScript, JavaScript Obfuscator / obfuscator.io patterns, AST transform scripts, string-array decoders, object proxy wrappers, computed properties, control-flow flattening, dead code, anti-debugger fragments, or code recovery cases.

Core principle: deobfuscation is a decision process, not beautification. Restore the smallest target path first. Expand to full-file restoration only when the target value cannot be reached otherwise.

## Module 1: JavaScript Obfuscation Recognition And Deobfuscation

### Fast Obfuscation Triage

Treat JS as likely obfuscated when several of these signals appear together:

- Large string or numeric arrays referenced through small decoder functions.
- IIFE wrappers that rotate arrays, pass array length/checksum, or hide a decoder closure.
- Calls like `_0xabc(123)`, `obj[fn(12)]`, `String.fromCharCode(...)`, `atob(...)`, `decodeURI(...)`, or custom XOR/Base64/RC4-like decoders.
- Many computed properties: `obj['name']`, `window['document']`, `a[b(c)]`.
- Object proxy tables whose members are tiny functions returning binary/logical/call expressions.
- `while(true)` or `while(!![])` containing `switch(order[index++])` or state variables.
- Excessive comma expressions, ternary expressions, unary boolean tricks such as `!![]`, `![]`, `!!![]`.
- Dead branches with constant tests, unreachable loops, `debugger`, `Function('debugger')`, `constructor('debugger')`, or timer-triggered traps.
- Very short meaningless variable names across the whole file without a minifier-compatible structure.
- `eval`, `Function`, dynamic script injection, or generated code returned from a string builder.

Do not conclude from formatting alone. Minified code is not necessarily obfuscated. Require structural evidence such as decoder functions, proxy wrappers, control-flow flattening, or dynamic code generation.

### Recognition And Recovery Matrix

| Pattern | Recognition rule | Preferred recovery | Needs dynamic? | Needs browser? |
|---|---|---|---|---|
| String array obfuscation | Array of strings plus decoder function; repeated decoder calls with literal indexes | Extract decoder prelude, evaluate only the decoder, replace calls with literals by AST | Yes when rotated/encrypted | Only if decoder reads DOM/BOM/fingerprint |
| String encryption | Base64/RC4/XOR/charCode/fromCharCode/decodeURI chains; encrypted constants | Identify algorithm, build input/output pairs, replace pure decoder calls | Often | Browser only for environment-dependent keys |
| Control-flow flattening | `while(true)` + `switch` + dispatch array/state variable + `continue/break` | Resolve dispatch order, concatenate matching case bodies, remove dispatcher | Usually no | No, unless state depends on runtime |
| Dead code injection | `if(false)`, `if(!![])`, impossible comparisons, unused declarations | AST constant folding, `evaluateTruthy`, binding/reference checks | No | No |
| Variable renaming | High entropy short identifiers, no semantic names | Do not rename blindly; recover names from call graph, API usage, and target role | No | No |
| Flower instructions | Tiny wrappers for `+`, `==`, `&&`, function call, string constants | Merge object/proxy table, replace calls/member reads with original operation or literal | No | No |
| IIFE wrapper | Top-level self-invoking function around array init/decoder/runtime checks | Preserve wrapper until decoder and side effects are understood; then unwrap only safe body | Sometimes | Sometimes |
| Proxy/object wrapper | `obj['k']=function(a,b){return a+b}` and later `obj['k'](x,y)` | Merge object assignments, replace references, remove object only after all uses are covered | No | No |
| `eval` obfuscation | `eval(stringBuilder(...))`, `Function(code)`, generated script text | Hook or evaluate in sandbox to capture generated code; parse generated code as a new stage | Yes | Browser if generated code touches DOM/BOM |
| Computed property obfuscation | `obj['prop']`, `obj[decoder(n)]`, nested member strings | Normalize to dot form when identifier-safe; decode property strings first | Sometimes | No |
| Infinite-loop control flow | `while(1)` with state changes, anti-debug loops, history spam | Classify: flattening dispatcher vs anti-debugger trap; restore dispatcher or neutralize trap | Sometimes | Browser when side effects are page APIs |

### Static, Dynamic, Browser Decision

Use regex/text search only for triage and locating candidates: arrays, decoder names, `while/switch`, `eval`, `debugger`, `constructor`, `setInterval`, `String.fromCharCode`, `Proxy`, and computed properties. Do not use regex to rewrite JavaScript unless the replacement is local and syntax-invariant.

Use AST static analysis when the transform depends on syntax shape: literal cleanup, computed-property normalization, constant folding, sequence splitting, object proxy replacement, dead code removal, control-flow flattening, and code generation.

Use dynamic execution when the value cannot be derived safely from syntax alone: encrypted string decoder, rotated array, runtime-generated code, opaque predicate depending on a helper, or a wrapper whose return value must be proven with sample input.

Use browser execution when the decoder or branch depends on `window`, `document`, `navigator`, `location`, `cookie`, storage, canvas, WebGL, audio, performance, native `toString`, event APIs, timers, or fingerprint checks. If only a small browser contract is missing, use JS Runtime Environment Reconstruction first; if parity remains unclear, verify in Chrome/CDP.

Use manual analysis when the transform would change business semantics: variable renaming, state-machine recovery with data-dependent transitions, VM/jsVMP handler mapping, or code whose side effects cannot be isolated.

### Deobfuscation Order

Default order for unknown obfuscated JS:

1. Preserve the original file and define the target output or target parameter path.
2. Parse and generate once to confirm syntax and parser options (`sourceType`, plugins, module/script mode).
3. Normalize literals: remove `extra` from string/numeric literals, expand hex/unicode escapes, normalize computed properties only when identifier-safe.
4. Recover string decoders before broad control-flow work. Decode literal-index calls and computed property strings first.
5. Merge object proxy tables and replace wrapper calls/member reads: binary/logical expressions, call forwarding, string constants.
6. Fold constants and split sequence/comma expressions after decoder replacement so newly exposed literals are simplified.
7. Expand trivial conditionals and remove constant dead branches. Keep branches whose predicate depends on runtime state.
8. Restore control-flow flattening: dispatch array/state variable -> ordered case bodies -> remove dispatcher variables.
9. Remove anti-debugger and obfuscator scaffolding only after confirming it is not part of target parameter generation.
10. Reparse generated code and rerun low-risk passes until no new literals/proxies/control-flow candidates appear.
11. Verify with syntax check and sample input/output, or compare original runtime output to restored output.

If a later pass exposes a new decoder or proxy table, loop back to the earliest affected phase instead of stacking unrelated transforms.

## Module 2: AST Based JavaScript Deobfuscation

### Framework Shape

Design AST deobfuscation as a staged pipeline:

```text
Source
 -> Parser
 -> AST
 -> Obfuscation fingerprint
 -> Transformer registry
 -> Restore passes
 -> Reparse checkpoint
 -> Generator
 -> Syntax/runtime verification
```

Each transformer must declare:

- `name`: what pattern it handles.
- `recognize`: AST shape and evidence required before transforming.
- `transform`: exact node rewrite or deletion.
- `risk`: what semantic assumption it makes.
- `verify`: syntax, changed-count, and sample comparison.
- `rollback`: how to disable the pass when it overmatches.

### Visitor Design Rules

- Prefer `exit` visitors for bottom-up transforms such as constant folding, string concatenation, computed member normalization, and wrapper replacement.
- Use `scope.getBinding`, `binding.constant`, `binding.referencePaths`, and `scope.crawl()` before deleting or renaming declarations.
- Use `path.evaluate()` and `evaluateTruthy()` only for pure expressions. Do not trust them for expressions with calls, member access, random/time, or environment reads.
- Use `replaceWithMultiple` for flattening control-flow or sequence expressions into statements; use `replaceWith` for expression-to-expression replacement.
- Use `remove()` only after confirming the binding has no needed references or the replacement consumed all references.
- Reparse after large structural rewrites. Some AST paths and scopes become stale after bulk replacement.
- Keep transform passes narrow. A pass should recognize one pattern and report how many nodes it changed.

### Core Transformer Strategies

Literal cleanup:
- Delete `extra` from string and numeric literals to reveal actual values and reduce noise.
- Convert `obj['validName']` to `obj.validName` only when the property is a valid identifier and semantics are unchanged.

Constant folding:
- Fold binary/unary expressions only when Babel reports confidence and the value is serializable.
- Avoid folding `Infinity`, `NaN`, `void`, date/random, calls, and expressions that rely on coercion side effects unless verified.

String decoder restoration:
- Locate decoder prelude and decoder call sites.
- Evaluate the smallest decoder environment, not the whole bundle.
- Replace only calls whose arguments are literals and whose result is stable.

Object proxy restoration:
- Merge object literal plus following assignment expressions into one table.
- Classify values as string constant, binary/logical operator wrapper, or call-forwarding wrapper.
- Replace references using the classification, then delete the table only when all references are handled.

Control-flow flattening:
- Match dispatcher shape: `while(true)` / `while(!![])`, first statement `switch(array[index++])`, terminal `break`, case bodies ending in `continue` or state update.
- Resolve dispatch order from a split string or decoded state table.
- Concatenate case consequents in dispatch order and remove `continue` / dispatcher mutation statements.
- If dispatch state is data-dependent, switch to dynamic tracing instead of static flattening.

Dead-code cleanup:
- Remove constant false branches and unused constant bindings after all decode/proxy passes.
- Do not remove try/catch, environment checks, or side-effect branches until their role in target generation is proven.

Instrumentation:
- For unclear target values, inject hooks at return statements, decoder returns, or target function exits.
- Prefer hook insertion over broad console logging when output volume would hide the signal.

### AI Decision Tree For Unknown JS

```text
If parser fails:
  adjust sourceType/plugins or capture generated code first.

If string array + decoder/IIFE exists:
  recover strings first, then reparse and rerun fingerprinting.

If many computed properties remain after string recovery:
  normalize member expressions and replace decoded property reads.

If object proxy wrappers exist:
  merge object assignments and replace binary/logical/call wrappers.

If many literal-only expressions or boolean tricks exist:
  run constant folding and dead-branch cleanup.

If `while` + `switch` + dispatch array/state exists:
  classify as control-flow flattening; static restore if dispatch is fixed, dynamic trace if state is runtime-dependent.

If `eval` / `Function` / generated script exists:
  hook code-generation output, parse the generated code, and restart the pipeline on the generated layer.

If `debugger`, constructor traps, or timer loops exist:
  classify anti-debugger vs business timer; disable only the specific trap and verify business logic.

If browser globals or native checks affect decoder output:
  rebuild minimal runtime contract or execute in Chrome/CDP.

If VM/opcode/handler tables appear:
  route to VM/jsVMP rules; do not flatten it as ordinary control flow.
```

### Failure Troubleshooting

- Parse error: try `sourceType: "module"`, relevant parser plugins, or capture the code after dynamic generation.
- Decoder returns wrong strings: check array rotation, checksum loop, argument offset, key derivation, and whether the decoder was evaluated before all helper functions were loaded.
- Dynamic evaluation hangs: isolate decoder code, disable timers, set execution timeout, and avoid evaluating anti-debugger scaffolding.
- AST pass deletes needed code: inspect binding references and scope; disable broad deletion of object tables or unused bindings until after sample verification.
- Control-flow output order is wrong: verify dispatch array, index increment location, terminal break condition, and whether cases mutate the next state dynamically.
- Browser and Node output differ: inspect DOM/BOM/fingerprint dependencies and use runtime reconstruction or CDP.
- Generated output is syntactically valid but behavior differs: compare original and restored target function on controlled inputs before doing more beautification.

### Best Practices

- Work from target output backward; full beautification is a fallback, not the default.
- Keep every pass reversible by running one pass at a time and recording changed counts.
- Prefer AST for syntax-aware rewrites, dynamic execution for decoder values, and browser/CDP for environment-dependent values.
- Never evaluate an untrusted full bundle when a decoder prelude is enough.
- Verify after each risky phase with syntax generation and at least one runtime or sample comparison.
- Treat comments like "this may be unsafe" in old transformer scripts as real risk: object deletion, unused-function deletion, and broad dead-code removal commonly overmatch.

## Optimization And Future Extensions

- Extract repeated AST logic into a transformer registry: `literalCleanup`, `safeConstantFold`, `stringDecoder`, `objectProxy`, `computedProperty`, `controlFlowFlattening`, `deadCode`, `antiDebugger`, `instrumentation`.
- Add a fingerprint scanner that reports pattern counts before changing code: decoder arrays, computed members, object proxy wrappers, `while/switch`, dynamic code, anti-debugger, browser globals.
- Add snapshot tests for each transformer using small artificial samples plus one real recovered target path.
- Add a safe evaluator for decoder-only execution with timeout, blocked timers, blocked network, and explicit allowed globals.
- Add dynamic tracing helpers for data-dependent dispatchers and VM-like structures.
- Add a browser parity checklist that decides when to use Node VM, mocked runtime, or Chrome/CDP.
- Add a transform manifest written beside recovered output: pass order, changed counts, assumptions, and verification evidence.
