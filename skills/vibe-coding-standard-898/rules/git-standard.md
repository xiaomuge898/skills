# Git Standard

Apply these instructions when creating branches, commits, commit messages, pull requests, or release notes.

## Branches

- Use short, descriptive branch names.
- Prefer the configured prefix when present, such as `codex/`.
- Use lowercase words separated by hyphens.
- Scope the branch to one logical change.

Examples:

```text
codex/add-vibe-coding-standard
codex/fix-login-timeout
codex/refactor-signature-tests
```

## Commits

- Commit only related files.
- Do not include unrelated working tree changes.
- Inspect `git status --short` before staging.
- Inspect staged diff before committing.
- Do not commit generated caches, secrets, local profiles, virtual environments, or transient logs unless explicitly required.

## Commit Message Format

Use this format:

```text
<type>(<scope>): <summary>

<body>

Verification:
- <command/result>
```

Allowed common types:

- `feat`: user-visible feature
- `fix`: bug fix
- `docs`: documentation only
- `test`: tests only
- `refactor`: behavior-preserving code change
- `chore`: tooling, config, or maintenance
- `perf`: performance improvement
- `security`: security hardening

Rules:

- Keep summary under 72 characters when practical.
- Use imperative mood: `add`, `fix`, `document`, `remove`.
- Explain why in the body when the change is not obvious.
- Include verification commands or state why verification was not possible.

## Pull Requests

PR description must include:

```text
Summary
Verification
Risk
Rollback
```

Do not mark a PR ready if required verification did not run.

## Safety

- Never use destructive Git commands unless the user explicitly requested them.
- Never revert user changes.
- If unrelated changes exist, leave them alone and mention them.
- If the same file contains user changes and task changes, inspect carefully and preserve both.
