# Input Parsing

Accept and normalize these inputs:

- curl requests.
- Chrome DevTools Copy as cURL.
- `fetch` code.
- `axios` code.
- Python `requests` code.
- HAR files or HAR JSON snippets.
- HTTP header lists.
- Complete request/response data.
- Browser Network copied request data.

Always extract this structure:

```yaml
request:
  url:
  method:
  scheme:
  host:
  path:
  headers:
  cookies:
  query:
  body:
  body_type:
  response:
    status:
    headers:
    body:
    error:
```

## Parsing Rules

- Preserve original names, casing, ordering, and encoded values in the evidence section.
- Also produce a decoded view for query/body fields when safe.
- Treat duplicate query or body keys as arrays.
- Identify body format: JSON, form-urlencoded, multipart, raw text, binary, GraphQL, protobuf-like, or unknown.
- If the input is code, separate request constants from runtime-generated values.
- If the input is HAR, identify initiator, request headers, response headers, status, cookies, query, postData, timings, and redirect chain when present.
- If only headers are provided, state which URL/method/body facts are missing.

## Parsed Output

Use this minimum parsed output:

```text
URL:
Method:
Headers:
Cookies:
Query:
Body:
Body type:
Response:
Missing evidence:
```
