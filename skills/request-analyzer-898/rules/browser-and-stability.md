# Browser And Stability

## Network Stability Analysis

When a request fails intermittently or behaves differently outside the browser, consider these classes without defaulting to IP replacement.

| Class | Check |
|---|---|
| A. Request frequency | interval, retry policy, burst rate, concurrency, server throttling |
| B. Source environment | IP changes, proxy, ASN, region, CDN routing, TLS/HTTP behavior |
| C. Device environment | browser parameters, client hints, fingerprint, device ID, app version |
| D. Business state | account status, permissions, order/data status, feature flags, risk state |

Always provide a probability ranking:

```text
Probable causes:
- Missing or wrong header: 80%
- Business parameter mismatch: 15%
- Network/source environment: 5%
```

Ranking rules:

- Base probabilities on the evidence, not habit.
- Prefer high-probability local request mismatches before network replacement.
- Mention proxy/IP only when evidence points to region, rate limit, risk control, or source binding.
- State uncertainty if the response body is missing or ambiguous.

## Browser Dependency Analysis

Decide whether the request can run outside a browser.

Check whether it depends on:

- Cookie generation.
- JS-computed parameters.
- WebSocket connection state.
- localStorage/sessionStorage/indexedDB.
- DOM/page state.
- Service worker state.
- Runtime config loaded by the page.
- Browser fingerprint APIs.
- CAPTCHA, challenge, or risk-control state.

Output:

```text
Can run without browser: YES | NO | UNKNOWN
Reason:
Missing dependencies:
Required browser artifacts:
Possible extraction point:
Recommended mode:
```

If the answer is `NO`, state exactly what is missing:

```text
Need to execute:
- xxx.js

Need to generate:
- token
- sign
- browser_id
```

Recommended modes:

- Pure HTTP replay: all required values can be fixed or generated locally.
- Hybrid browser seed: browser is used only to refresh cookies, tokens, or runtime seeds; HTTP replay happens in Python.
- Browser-executed request: request must be sent from page context due to hard browser/page state dependency.
- Algorithm reproduction: implement recovered signing/encryption logic in Python or JavaScript.
