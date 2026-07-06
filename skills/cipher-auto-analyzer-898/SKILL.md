---
name: cipher-auto-analyzer-898
description: Use when analyzing unknown ciphertext, encoded text, tokens, opaque parameters, nested encodings, Base64/Hex/URL/JWT/XOR-like data, or when Codex needs to identify likely codec or cryptographic layers, try reversible decoding safely, score hypotheses, and produce a reproducible cipher analysis report.
---

# Cipher Auto Analyzer 898

## Overview

Analyze unknown ciphertext or opaque text by detecting structural features, generating ranked hypotheses, trying reversible decoding paths, scoring evidence, and reporting a reproducible analysis trail.

Core principle: treat every decode as a hypothesis until it is verified by readable output, structure, entropy change, known protocol shape, or sample round trip.

## Scope Rules

- Work only on user-authorized samples, local labs, owned code, public test data, or materials explicitly provided for analysis.
- Do not claim successful decryption without evidence.
- Do not brute-force passwords, private keys, accounts, services, or online endpoints.
- Do not run unbounded recursion, unbounded key search, or high-cost cracking.
- Redact secrets in reports: cookies, tokens, passwords, private keys, account IDs, device IDs, and proxy credentials.
- Prefer reversible, low-risk transformations first: URL decode, Base64, Hex, JWT parsing, Unicode repair, and simple XOR analysis.

## Quick Reference

| Signal | Likely candidate | First checks |
|---|---|---|
| `A-Z a-z 0-9 + / =` and length multiple of 4 | Base64 | Repair padding, decode bytes, test UTF-8/JSON |
| `A-Z a-z 0-9 - _` | URL-safe Base64 | Add padding, use URL-safe alphabet |
| only `0-9a-fA-F`, even length | Hex | Decode bytes, test text or binary header |
| contains `%2F`, `%3D`, `%7B` | URL encoding | URL decode once, then re-run detection |
| three dot-separated segments | JWT or JWS | Base64url-decode header and payload; do not forge signature |
| starts with `{` or `[` | JSON | Parse JSON and inspect nested fields |
| repeated delimiters `:`, `.`, `_`, `-` | Structured token | Split fields and analyze each field separately |
| high entropy, fixed block size | Encryption or compressed data | Identify outer encoding, block size, IV/salt/header |
| readable text after XOR with one byte | Simple XOR | Score printable ratio and keywords |

## Analysis Workflow

1. Reconstruct the task:
   - Goal: identify, decode, decrypt, explain, or produce a report.
   - Input: exact sample text, origin if known, expected plaintext if any.
   - Constraints: offline only, max iterations, allowed algorithms, no online brute force.
   - Ambiguities: missing key, missing IV, missing salt, unknown charset, unknown context.
2. Preserve the original input exactly. Never overwrite or normalize the only copy.
3. Extract features:
   - length, character set, delimiters, padding, segment count
   - printable ratio, entropy, repeated patterns, block-size hints
   - known wrappers: JSON, query string, JWT, PEM, gzip/zlib magic, protobuf-like bytes
4. Generate ranked hypotheses from observable evidence.
5. Run the hypothesis loop with a bounded depth.
6. For every attempt, record method, input preview, output preview, status, evidence, confidence, and reason for rejection.
7. If output becomes structured or readable, run post-decode analysis.
8. If output still looks encoded, re-enter the loop only when there is new evidence and depth remains.
9. Cross-validate the best result before finalizing.
10. Produce a report with the full attempt trail and reproducible steps.

## Hypothesis Loop

Use bounded breadth and depth. Default limits:

- max depth: 3 nested layers
- max attempts per layer: 20
- max output preview: 500 characters
- max single-byte XOR keys: 256
- no dictionary or password brute force unless the user explicitly provides a small local key list and confirms authorization

Candidate transformations:

1. URL decode once.
2. Base64 decode: standard, URL-safe, missing padding repair.
3. Hex decode.
4. JSON parse or query-string parse.
5. JWT parse: decode header and payload only; verify signature only if key and authorization are supplied.
6. Unicode repair: escaped sequences, UTF-8, UTF-16LE/BE if byte pattern suggests it.
7. Reverse string, then retry high-confidence decoders.
8. Gzip/zlib inflate only when magic bytes or container evidence exists.
9. Single-byte XOR or short repeating-key XOR only for small offline samples.

Reject a path immediately when:

- decoded bytes are empty or invalid for the claimed format
- printable ratio remains low and no binary format evidence appears
- JSON or JWT parsing fails after valid outer decoding
- entropy and structure do not improve
- output size expands suspiciously without explanatory format evidence
- the path repeats a previous state

## Scoring

Score every candidate from 0.0 to 1.0.

| Evidence | Suggested score impact |
|---|---|
| Valid format parse: JSON, JWT header, query string, PEM | +0.25 |
| Mostly readable UTF-8 text | +0.25 |
| Business keywords: id, user, token, time, nonce, sign, payload | +0.15 |
| Entropy decreases after decoding | +0.10 |
| Output matches expected length, block, or protocol shape | +0.15 |
| Verified sample round trip | +0.30 |
| Requires unsupported assumptions | -0.20 |
| Key/IV/salt missing for claimed encryption | cap at 0.50 |
| Only visual similarity with no parse or round trip | cap at 0.40 |

Confidence labels:

- `0.80-1.00`: strong, verified by structure or round trip
- `0.60-0.79`: likely, multiple evidence points but not fully proven
- `0.40-0.59`: plausible, useful lead but missing key evidence
- `0.00-0.39`: weak or rejected

## Post-Decode Analysis

When an attempt succeeds or partially succeeds, inspect the result:

- Is it JSON, HTML, XML, query string, CSV, protobuf-like bytes, or binary?
- Does it contain keys such as `id`, `uid`, `user`, `time`, `timestamp`, `nonce`, `sign`, `token`, `key`, `iv`, `salt`, `data`, `payload`?
- Does it contain another nested encoded field?
- Does it expose sensitive values that must be redacted in the final answer?
- Is the result plaintext, compressed data, encrypted data, or a structured token?
- What exact steps reproduce it from the original input?

If another layer exists, analyze only that layer and keep the parent trail.

## Encryption Boundary

Distinguish encoding from encryption:

- Encoding is reversible without secret material: Base64, Hex, URL encoding, Unicode escapes.
- Hashing is one-way: MD5, SHA1, SHA256. Do not call it decryption.
- Encryption needs key material: AES, DES, RSA, SM2, SM4, custom ciphers.
- Compression is not encryption: gzip, zlib, deflate, brotli.

If an encrypted layer is suspected but key material is missing, stop at identification and state what is missing:

```text
Current evidence supports encrypted data, but decryption is not possible yet.
Missing: key, IV/nonce, mode, padding, authentication tag, or plaintext/ciphertext pair.
Next check: locate key source or capture runtime arguments.
```

## Output Contract

When answering directly, include:

```text
[Best guess]
[Confidence]
[Why this is the answer]
[Successful decode path]
[Rejected paths]
[Recovered content or safe preview]
[Missing evidence]
[Risks]
[Alternatives / next checks]
```

When generating a report, use:

```json
{
  "input_summary": {
    "length": 0,
    "charset": [],
    "segments": 0,
    "entropy": 0.0
  },
  "hypotheses": [
    {
      "type": "base64",
      "confidence": 0.85,
      "evidence": []
    }
  ],
  "attempts": [
    {
      "depth": 0,
      "method": "base64_decode",
      "status": "success",
      "confidence": 0.0,
      "output_preview": "",
      "evidence": [],
      "rejection_reason": ""
    }
  ],
  "final_result": {
    "status": "success|partial|fail",
    "best_guess": "",
    "decoded_data_preview": "",
    "reproducible_steps": []
  },
  "risks": [],
  "next_checks": []
}
```

## Common Mistakes

| Mistake | Fix |
|---|---|
| Treating Base64 as encryption | Call it encoding and decode without implying secrecy |
| Declaring AES from high entropy alone | Require key/IV/mode evidence or code evidence |
| Printing full sensitive token values | Redact and show only length, prefix/suffix, or field names |
| Running unlimited nested decoders | Use depth and attempt limits |
| Accepting unreadable output as success | Require parseability, readability, or binary format evidence |
| Ignoring padding repair | Try standard and URL-safe Base64 with deterministic padding repair |
| Forgetting nested layers | Re-run detection on successful structured output |
| Confusing hash with encryption | State that hashes cannot be decrypted |

## Reverse Verification Before Final Answer

Check:

- Does the answer satisfy the user's requested output?
- Did every success claim have evidence?
- Did rejected paths include a reason?
- Were environment and authorization limits respected?
- Were sensitive values redacted?
- Is there a better low-cost alternative, such as asking for key/IV or one known plaintext sample?
- Are risks and next checks explicit?
