# Response Format Template

Use this output shape for user-facing analysis:

```markdown
## Requirement Reconstruction

- Goal:
- Input:
- Output:
- Constraints:
- Ambiguities:

## Parsed Request

URL:
Method:
Headers:
Cookies:
Query:
Body:
Response:

## Parameter Classification

| Parameter | Location | Type | Change Pattern | Importance | Recommendation |
|---|---|---|---|---|---|

## Failure Diagnosis

1. HTTP layer:
2. Business parameters:
3. Identity state:
4. Algorithm parameters:
5. Environment dependencies:

## Parameter Decisions

| Parameter | Handling | Reason | Test |
|---|---|---|---|

## Network Stability

| Cause | Probability | Evidence |
|---|---:|---|

## Browser Dependency

Can run without browser: YES/NO/UNKNOWN

## Recommended Solution

## Risks

## Alternatives

## Needed Evidence
```

If generating code, add:

```markdown
## Generated Project

Path:
Files:
How to run:
Verification:
```

## Example Interaction

User provides:

```text
curl 'https://api.example.com/items?timestamp=1710000000&sign=abc' \
  -H 'authorization: Bearer xxx' \
  -H 'content-type: application/json' \
  --data-raw '{"page":1,"request_id":"..."}'
```

Expected analysis summary:

```text
timestamp: dynamic, simple generation, validate expiry window.
request_id: dynamic, simple generation with UUID.
authorization: session, depends on login/token refresh.
sign: algorithm, test by modifying one signed field; analyze JS only if server rejects modified/missing sign.
Can run without browser: UNKNOWN until token and sign source are known.
Recommended next evidence: successful browser sample and failed replay response.
```
