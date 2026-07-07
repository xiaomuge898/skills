# Parameter And Crypto Rules

## Parameter Generation

Use for: "Where does this parameter come from?", "How is sign generated?", "How is token calculated?", "Restore request parameters."

Process:

1. Search target names: `sign`, `token`, `timestamp`, `nonce`, `data`, `payload`, `encrypt`, `signature`, `X-Bogus`.
2. Search request entry points: `fetch`, `XMLHttpRequest`, `axios`, `$.ajax`, `sendBeacon`, wrappers, interceptors.
3. Find assignment points, generator functions, callers, and return-value flow.
4. Identify participating fields: path, query, body, cookies, headers, user-agent, timestamp, nonce, device fingerprint, server return value, fixed salt.
5. Restore concatenation order, sorting, case normalization, separators, empty-value handling.
6. If several parameters appear in the same protected request, decide whether they are one runtime event or independent generators.
7. Restore encoding, compression, hash, HMAC, encryption, Base64, and URL encoding layers.
8. Rebuild the required execution context and reproduce the generator.
9. Compare with the original request: format, length, timestamp precision, nonce shape, signature output, response status, response code, and response keywords.

If the participating fields or generation mode are uncertain, stop broad static reading and run Hypothesis-Driven Validation:

- Hypothesis examples: query/body concat, sorted keys, fixed salt, timestamp precision, random nonce, cookie/session field, localStorage seed, device fingerprint, server-issued token, hash/HMAC/encryption wrapper.
- Minimal experiments: freeze time, freeze random, replace one request field, remove one header/cookie, hook the generator input, log pre-hash string, replay with one changed variable, or compare two requests that differ by one input.
- Reject any hypothesis whose output length, charset, timestamp behavior, field sensitivity, or replay result contradicts the expectation.

Output:

```text
[Parameter]
[Reproduction mode]
[Generation entry]
[Call chain]
[Participating fields]
[Concatenation/sorting rules]
[Encoding/hash/encryption layers]
[Runtime context dependencies]
[Reproduction code or pseudocode]
[Verification result]
[Unknowns]
```

## Key Source Analysis

Use for: "Find the key", "Find iv", "Find salt", "Find AES key."

Process:

1. Search hardcoded strings, config objects, constant pools, environment variables, server-issued fields.
2. Search `window.xxx`, `globalThis.xxx`, `localStorage`, `sessionStorage`, `cookie`.
3. Check whether timestamp, random value, user ID, device fingerprint, or server field derives the key.
4. Check obfuscation, concatenation, reversing, Base64, Hex, URL encoding.
5. Prove it enters an encryption/decryption function as `key`, `iv`, `salt`, `message`, or `password`.
6. Verify with sample input/output. Never call a string a key just because it looks like one.

When multiple candidate keys, IVs, salts, or passwords exist, validate them as competing hypotheses. The minimum acceptable proof is evidence that the candidate reaches the crypto call and reproduces or changes the sample output as expected.

## Payload Decryption

Use for: "Decrypt this parameter", "Restore payload", "What is inside data?", "Open this ciphertext."

Process:

1. Confirm the ciphertext sample and request location.
2. Identify outer encoding: URL encode, Base64, Hex, JSON stringify, gzip, protobuf.
3. Identify algorithm: AES, DES/3DES, RSA, SM2/SM4, XOR, or custom.
4. Confirm `key`, `iv`, `mode`, `padding`, AAD/tag for GCM-like flows.
5. Restore the decryption flow and record the exact failure point if it does not close.
6. Verify with a sample round trip.

If the algorithm is unknown, test hypotheses from cheap to expensive:

1. Outer encoding and compression: URL encode, Base64, Hex, JSON stringify, gzip/zlib, protobuf-like framing.
2. Hash/signature shapes: MD5/SHA/HMAC length and charset checks.
3. Symmetric crypto candidates: AES/DES/3DES/SM4 mode, padding, IV, and tag behavior.
4. Asymmetric crypto candidates: RSA/SM2 key markers, ciphertext block length, public-key call sites.
5. Custom logic: XOR, rotation, bit operations, table substitution, checksum, or VM-generated transform.

For each candidate, record the hypothesis, minimal verification experiment, expected result, observed result, judgment, and next reasoning direction.

## Library And Algorithm Identification

Search first:

- `CryptoJS`
- `crypto.subtle`
- `node:crypto`, `createHash`, `createHmac`
- `JSEncrypt`
- `node-forge`
- `jsrsasign`
- `sm-crypto`
- `crypto-js`

Common features:

- MD5: 32 hex characters.
- SHA1: 40 hex characters.
- SHA256: 64 hex characters.
- HMAC: both `key` and `message` are present.
- AES: key commonly 16/24/32 bytes; mode commonly CBC/ECB/GCM/CTR.
- RSA: PEM, public/private key markers, `BEGIN PUBLIC KEY`.
- Base64: alphabet commonly includes `A-Z a-z 0-9 + / =`.
- URL encoding: `%2F`, `%3D`, `%2B`, `%25`.
- SM2/SM3/SM4: often appears in national-crypto libraries or names.

Always report evidence, input structure, output features, whether the algorithm is standard or custom, and what still needs verification.

When evidence is ambiguous, do not name a final algorithm from appearance alone. Produce a ranked hypothesis list and validate each candidate with the smallest input/output or runtime experiment available.
