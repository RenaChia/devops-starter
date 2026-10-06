# DevOps Starter: CI Security Pipeline

A small Python calculator project used to learn Continuous Integration (CI) with GitHub Actions. Every change is automatically linted, tested and scanned for security issues.

## Project structure

```
devops-starter/
├── .github/workflows/ci-security.yml   # CI workflow
├── calculator.py                       # Application code
├── test_calculator.py                  # Unit tests (pytest)
├── bandit.yaml                         # Bandit configuration
└── requirements.txt                    # Python dependencies
```

## What the workflow does

**Triggers**

| Event | When it runs |
|---|---|
| `push` to `main` | After code lands on the main branch |
| `pull_request` | When a PR is opened or updated, before merging |
| `workflow_dispatch` | Manually, via the "Run workflow" button in the Actions tab |

**Jobs** (run in parallel on separate machines)

- **test**: lints with flake8 (stops on syntax errors), runs unit tests with pytest, and uploads an HTML test report.
- **security**: scans the code with Bandit, checks dependencies for known vulnerabilities with pip-audit, and uploads an HTML Bandit report.

Security steps use `continue-on-error`, so findings are reported without blocking the workflow.

## Bandit configuration

Bandit flags every `assert` statement (check `B101`) because Python removes asserts when run in optimised mode (`-O`), which is dangerous if they guard security logic. In tests, asserts are expected, so `bandit.yaml` allows them only in test files:

```yaml
assert_used:
  skips: ["*test_*.py", "*_test.py"]
```

Test files are still scanned for every other issue (e.g. hardcoded passwords).
