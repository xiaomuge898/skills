# Failure Diagnosis

When the user provides a failed request, abnormal response, empty data, forbidden response, timeout, redirect loop, or mismatch against browser behavior, diagnose in this exact order.

## Step 1: HTTP Layer

Check:

- URL mismatch.
- Path or host error.
- Method error.
- Missing headers.
- Wrong `Content-Type`.
- Wrong `Accept`.
- Missing `Origin` or `Referer`.
- Compression or encoding mismatch.
- HTTP/2 or TLS behavior when relevant.

## Step 2: Business Parameters

Check:

- Missing parameters.
- Wrong parameter names.
- Wrong type.
- Wrong nested JSON shape.
- Wrong enum value.
- Expired timestamp.
- Pagination, cursor, region, language, or feature flag mismatch.

## Step 3: Identity State

Check:

- Cookie presence and validity.
- Token presence and validity.
- Session binding.
- Login state.
- CSRF token origin.
- Account permission.

## Step 4: Algorithm Parameters

Check:

- Missing `sign`, `signature`, `encrypt`, `hash`, checksum, or anti-bot parameter.
- Signature computed over different URL/query/body/header order.
- Timestamp or nonce included in signature.
- Key, salt, IV, or server-issued seed missing.
- Encoding mismatch before signing.

## Step 5: Environment Dependencies

Check:

- Browser JS generation logic.
- localStorage/sessionStorage/indexedDB state.
- WebSocket pre-handshake or page state.
- Device/browser fingerprint.
- Runtime APIs such as `crypto`, canvas, WebGL, timezone, language, or UA-CH.
- Frontend route state, hidden config, or bootstrap response.

## Output

```text
Failure Diagnosis:
1. HTTP layer:
2. Business parameters:
3. Identity state:
4. Algorithm parameters:
5. Environment dependencies:

Most likely cause:
Evidence:
Next verification:
```
