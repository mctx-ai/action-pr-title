# action-pr-title

Composite GitHub Action that validates pull request titles against the [Conventional Commits](https://www.conventionalcommits.org/) specification. Wraps [amannn/action-semantic-pull-request](https://github.com/amannn/action-semantic-pull-request) with mctx-ai org defaults baked in.

## What it does

- Enforces a fixed set of commit types across all mctx-ai repos (no per-repo drift)
- Accepts optional inputs for repo-specific configuration (scopes, ignore labels, subject pattern)
- Uses `github.token` — no secrets configuration required

### Allowed types

`feat`, `fix`, `chore`, `docs`, `refactor`, `test`, `ci`, `build`, `perf`, `style`, `revert`

## Inputs

| Input                   | Description                                                                                                | Default   |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------- | --------- |
| `scopes`                | Newline-delimited list of allowed scopes. When empty, any scope is accepted.                                 | `''`      |
| `require-scope`         | Require a scope in every PR title.                                                                            | `'false'` |
| `ignore-labels`         | Newline-delimited list of PR labels that skip validation. When empty, no labels are ignored.                 | `''`      |
| `subject-pattern`       | Regex for additional subject validation. When empty, no subject pattern is enforced.                         | `''`      |
| `subject-pattern-error` | Custom error message when `subject-pattern` does not match. Supports `{subject}` and `{title}` placeholders. | `''`      |

## Usage

### Minimal (no inputs)

Uses all defaults: any scope allowed, no subject pattern, no ignored labels.

```yaml
name: PR Title

on:
  pull_request:
    types: [opened, edited, synchronize, reopened]

permissions:
  pull-requests: read

jobs:
  validate:
    name: Validate PR Title
    runs-on: ubuntu-latest
    steps:
      - uses: mctx-ai/action-pr-title@v1.0.0
```

### Full (all inputs — mctx repo style)

Restricts to known scopes, enforces lowercase subject, and skips validation for bot/dependency PRs.

```yaml
name: PR Title

on:
  pull_request:
    types: [opened, edited, synchronize, reopened]

permissions:
  pull-requests: read

jobs:
  validate:
    name: Validate PR Title
    runs-on: ubuntu-latest
    steps:
      - uses: mctx-ai/action-pr-title@v1.0.0
        with:
          scopes: |
            api
            workers
            db
            shared
            web
            app
            e2e
            docs
            changelog
            ci
            test
            deps
            deps-dev
            scripts
          require-scope: "false"
          ignore-labels: |
            dependencies
            bot
          subject-pattern: "^(?![A-Z]).+$"
          subject-pattern-error: |
            The subject "{subject}" found in the pull request title "{title}"
            must start with a lowercase letter. Example: "feat: add user auth"
```
