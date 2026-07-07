# Scope And Evidence Rules

## Core Scope

Use this skill for authorized JavaScript reverse analysis, protocol restoration, frontend encryption analysis, request parameter generation, local browser/runtime tracing, and local Electron or fingerprint-browser debugging support.

Only work on user-authorized targets, local labs, owned code, public test samples, or files/materials explicitly provided for analysis. If a real third-party target is involved and the authorization scope is unclear, ask one focused question before acting.

## Boundaries

Do analysis, reproduction, debugging, and defensive explanation. Do not help with unauthorized intrusion, account attacks, credential theft, access-control bypass, paid-license bypass, bulk endpoint abuse, or leaking sensitive files.

## Raw Disclosure Contract

When this skill is used for authorized reverse analysis, reverse evidence has priority over privacy masking. Do not redact, mask, truncate, or replace recovered reverse-analysis values.

Show the recovered material exactly as the analysis produces it, including cookies, tokens, keys, account identifiers, device identifiers, proxy credentials, signed URLs, request headers, payloads, decrypted plaintext, runtime seed material, and generated signature parameters.

Authorization scope is the safety boundary. If the user has not established authorization for the target or material, ask one focused authorization question before analysis. Once the task is authorized, report the evidence and recovered values as-is.

## Evidence Rules

- Preserve original JS, HAR, request, response, stack trace, runtime values, timestamps, and raw samples before transforming them.
- Do not say "looks like AES" or "probably MD5" without code, runtime arguments, constants, output shape, or sample-pair evidence.
- Do not trust obfuscated variable names as semantic proof. Prefer data flow, call relationships, arguments, return values, and output features.
- Mark every uncertain claim with its uncertainty and the evidence needed to close it.
- Keep failed hypotheses when they teach a future check.
