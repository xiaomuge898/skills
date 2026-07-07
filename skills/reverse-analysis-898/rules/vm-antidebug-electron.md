# VM, Obfuscation, Anti-Debugger, Electron Rules

Role: Main branch group for VM/jsVMP path restoration, anti-debugger analysis, Electron asar/DevTools work, and remote-debugging-port discovery.

Input: evidence inventory, selected reproduction mode, obfuscation fingerprints, runtime symptoms, Electron paths, process or shortcut evidence.

Output: VM path, anti-debugger trigger, Electron modification point, remote-debugging source, verification result, residual risks.

May call support capabilities: `js-obfuscation-ast-deobfuscation.md` before VM is proven, `js-runtime-environment-reconstruction.md` for browser/runtime gaps, and `workflow-routing.md` for Hypothesis-Driven Validation.

Returns control to: `rules/orchestrator.md` for verification and trace-driven reporting.

## VM / jsVMP Path Restoration

Use for: "Analyze VM", "Analyze jsVMP", "Restore opcode", "Restore bytecode."

If generic JavaScript Obfuscator / obfuscator.io patterns are encountered before a real VM is proven, load `js-obfuscation-ast-deobfuscation.md` first. Return to this rule only when bytecode, opcode, handler table, stack/register, or VM dispatch evidence exists.

Process:

1. Confirm whether it is actually VM/jsVMP: large arrays, bytecode, constant pool, `while/switch` dispatch, opcode, pc/sp/stack/context/register, handler table.
2. Locate VM entry: bytecode source, constant pool source, external parameter entry, return-value flow.
3. Map opcode to handlers, stack/register operations, jumps, calls, property access, string decoding.
4. If obfuscation blocks static reading, use the JavaScript obfuscation and AST deobfuscation rules for constant strings, object proxies, computed properties, control-flow flattening, and wrapper windows before attempting VM mapping.
5. Extract only the shortest path related to the target parameter and mark irrelevant branches.
6. Instrument dynamically when needed: print opcode, handler inputs/returns, stack changes, variables before/after target generation.

If the VM structure is unclear, use Hypothesis-Driven Validation before mapping the whole VM:

- Hypotheses: switch dispatcher, handler table, computed goto, stack VM, register VM, mixed control-flow flattening, dynamic bytecode decode, environment-gated branch.
- Minimal experiments: hook dispatcher input, log `pc/sp/registers`, replace one opcode/handler, freeze decoded bytecode, compare handler input/output around the target parameter, or bypass one environment check.
- Reject a VM hypothesis when opcode movement, stack/register changes, or target output sensitivity contradicts the expected behavior.

## AST Recovery Rules

For non-VM JavaScript Obfuscator / AST deobfuscation work, use `js-obfuscation-ast-deobfuscation.md`. The rules below are only the VM-adjacent subset.

- Preserve the original obfuscated file.
- Build a call graph from the target output backward.
- Recover string arrays, computed properties, wrapper functions, and control-flow flattening only along the target path.
- Apply one AST transform at a time and verify syntax after each transform.
- Do not beautify the whole bundle unless it helps the target path.

## Anti-Debugger Analysis

Use for: "Remove debugger", "Analyze anti-debugging", "DevTools freezes", "DevTools is detected."

Process:

1. Search `debugger`, `setInterval`, `setTimeout`, `Function("debugger")`, `constructor("debugger")`, `eval`, `console.clear`, `devtools`, `isOpen`.
2. Identify the trigger: timer, constructor, console detector, window-size delta, shortcut, context menu.
3. Identify side effects: pause, blank page, redirect, risk-control report, broken parameter generation.
4. For local samples, propose the smallest change: comment, no-op replacement, disable the specific timer, or hook the detector.
5. Verify the page works, parameter generation is intact, DevTools can stop in business code, and no secondary anti-debugger remains.

When the trigger is uncertain, test one detector hypothesis at a time. Disable or hook only one timer, constructor, console detector, shortcut handler, or window-size check, then observe whether the side effect disappears and business logic still runs.

## Electron / asar / DevTools

Use for: "Remove debugger from asar", "Fingerprint browser blocks F12/Ctrl+Shift+I", "Enable DevTools", "Debug Electron app.asar."

Back up the original `asar` or directory before modification.

Process:

1. Identify install path, `resources`, `app.asar`, and `app.asar.unpacked`.
2. Inspect `package.json`, `main.js`, `index.js`, `background.js`, `preload.js`, renderer bundle.
3. Search `openDevTools`, `devTools`, `webPreferences`, `BrowserWindow`, `globalShortcut`, `before-input-event`, `setMenu`, `F12`, `Control+Shift+I`, `preventDefault`.
4. Search `debugger`, `Function("debugger")`, `constructor("debugger")`, `setInterval`, `devtools-detect`.
5. Identify source: main process, renderer, preload, menu, bundled obfuscated code.
6. Use the smallest change: remove shortcut blocking, restore `webContents.openDevTools()`, set `webPreferences.devTools=true`, preserve business logic.
7. Repack or replace the unpacked directory and verify DevTools startup.

## remote-debugging-port Discovery

Use for: "Find remote-debugging-port", "Enable remote debugging port", "Fingerprint browser remote debugging."

Process:

1. Confirm launch mode: shortcut, script, Electron main process, child Chromium process, config file.
2. Search `remote-debugging-port`, `remoteDebuggingPort`, `debuggingPort`, `--remote-debugging-port`, `appendSwitch`, `commandLine.appendSwitch`, `spawn`, `execFile`, `child_process`, `chrome.exe`.
3. Identify source: code, config, database, command line, environment variable, random allocation.
4. Choose modification path: shortcut argument, config change, `app.commandLine.appendSwitch`, child-process argument.
5. Verify `http://127.0.0.1:<port>/json/version` and `/json/list`; confirm the listener is local-only when possible.
