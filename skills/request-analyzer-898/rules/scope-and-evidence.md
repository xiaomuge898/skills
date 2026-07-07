# Scope And Evidence

## Suitable Use

Use this skill for:

- API data collection and authorized crawler request replay.
- Automated API testing and regression fixtures.
- Data synchronization and internal system integration.
- Debugging failed HTTP requests.
- Converting browser/API evidence into a maintainable Python project.

Do not use this skill for:

- Unauthorized access.
- Credential theft.
- Bypassing access control.
- Destroying, degrading, or abusing systems.
- Bulk abuse of third-party APIs.

If authorization, target ownership, or allowed test scope is unclear, ask one focused authorization question before executing live requests.

## Evidence Inventory

Record available evidence before analysis:

```yaml
evidence:
  source_type:
  successful_sample_available:
  failed_sample_available:
  browser_context_available:
  login_state_known:
  frontend_code_available:
  har_available:
  response_available:
  live_execution_allowed:
```

## Sensitive Value Rules

- Do not expose full sensitive cookie, token, authorization, password, secret, private key, account identifier, or proxy credential values in ordinary reports.
- Show field names, dependency type, prefixes, lengths, expiry observations, and source conclusions.
- Save sanitized samples for regression tests.
- Keep secrets in environment variables or ignored local config files when generating code.
- Do not write raw sensitive values to README, analysis reports, logs, committed fixtures, or generated tests unless the user explicitly authorizes local analysis that requires exact values.

## Insufficient Information Rules

If information is insufficient, ask targeted questions before code generation. Prioritize questions that change implementation logic.

Common questions:

- Is the browser request successful while the replay request fails?
- Do you have one successful sample and one failed sample?
- Are you already logged in when capturing this request?
- What is the exact failed response status, headers, and body?
- Is there an available browser environment or CDP session?
- Are we allowed to inspect the frontend JavaScript?
- What is the target interface used for: data collection, testing, synchronization, or internal integration?
- Does the request need to run repeatedly, and at what frequency?

Do not ask all questions at once. Ask only the highest-impact missing questions, or list them in a short "Needed Evidence" section when producing a report.
