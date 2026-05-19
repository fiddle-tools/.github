# fiddle-tools/.github

Org-wide GitHub configuration and reusable workflows.

## Reusable Workflows

### Claude Code Review

AI-powered PR review using [GLM-5.1](https://api.z.ai) that flags bugs, security issues, data integrity problems, and breaking contract changes — not style.

**Source:** `.github/workflows/claude-code-review.yml`

#### Usage

Add this file to any repo at `.github/workflows/code-review.yml`:

```yaml
name: Claude Code Review

on:
    pull_request:
        types: [opened, synchronize, reopened, ready_for_review]
        branches: [master, main]

jobs:
    review:
        uses: fiddle-tools/.github/.github/workflows/claude-code-review.yml@main
        secrets: inherit
```

#### Required Secrets

| Secret | Where to set |
|--------|-------------|
| `PR_REVIEW_ZAI_API_KEY` | Org-level secret (inherited by all repos) or per-repo secret |

#### Per-repo Configuration (optional)

Add a `.claude-review.yml` at the repo root to customize behavior:

```yaml
# Extra files/patterns to ignore (on top of lock files, images, markdown)
ignore:
    - "**/*.generated.ts"
    - "src/mocks/**"

# Repo context injected into the review prompt
context: "React + TypeScript SPA. State managed with Zustand."

# Minimum severity to post as a comment: "warning" (default) or "critical"
min_severity: warning

# Patterns the reviewer should never flag (established conventions)
accepted_patterns:
    - "Direct state mutation via Immer produce()"
```

#### What gets reviewed

- Bugs: reachable conditions that produce wrong results, crashes, or hangs
- Security: concrete exploit paths (injection, auth bypass, SSRF, secret leaks)
- Data integrity: silent data loss, missing rollbacks, race conditions
- Breaking contracts: return type changes, removed required fields, changed API behavior

Style, formatting, naming, comments, and hypothetical issues are never flagged.

## Repos using this workflow

- `testlify-ui`
