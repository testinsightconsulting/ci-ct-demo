# ci-ct-demo
![CI](https://github.com/testinsightconsulting/ci-ct-demo/actions/workflows/ci-python.yml/badge.svg) ![Coverage](https://img.shields.io/badge/coverage-unknown-yellowgreen.svg)

About
This repository demonstrates a minimal, opinionated setup for Continuous Integration (CI) and Continuous Testing (CT) for a Python project using pytest and GitHub Actions. It shows how to run tests, produce machine-readable test reports, collect coverage, and publish artifacts useful for pull-request validation.

Repository layout
.github/workflows/ — example CI workflows (e.g., ci-python.yml)
src/ or package_name/ — Python package source
tests/ — pytest test files
requirements.txt or pyproject.toml/poetry.lock — dependency spec
README.md — this file
Quick start (local)
Clone the repo: git clone https://github.com/testinsightconsulting/ci-ct-demo.git cd ci-ct-demo

Create and activate a virtual environment: python -m venv .venv source .venv/bin/activate # macOS / Linux .venv\Scripts\activate # Windows (PowerShell: .venv\Scripts\Activate.ps1)

Install dependencies: pip install -r requirements.txt

or, if using pyproject.toml/poetry:
poetry install
Run tests: pytest

Recommended pytest commands (for CI and local parity)
Run tests with verbose output: pytest -q

Run tests and produce JUnit XML (for test reporting): pytest --junitxml=reports/junit.xml

Run tests with coverage and produce XML and HTML reports: pytest --cov=your_package --cov-report=xml:reports/coverage.xml --cov-report=html:reports/htmlcov

Fail fast on first error (useful while developing): pytest -x

Notes:

Replace your_package with the import name of your package (e.g., src.my_package or my_package).
Create the reports directory prior to running if your test runner or workflow expects it: mkdir -p reports
Continuous Integration (GitHub Actions)
Place workflows in .github/workflows/ (e.g., ci-python.yml).
Typical CI steps:
Checkout code
Set up Python (use actions/setup-python)
Cache pip dependencies
Install dependencies
Run linting (optional)
Run pytest with --junitxml and coverage flags
Upload test results and coverage reports as artifacts
Use badges to show CI status. After creating your workflow, the CI badge at the top will become active.
Test reporting & coverage
Use JUnit XML (pytest --junitxml=...) so CI systems and code review tools can show test results.
Use pytest-cov to generate coverage.xml (for Codecov/other services) and an HTML report for local inspection.
Upload reports as artifacts in CI: uses: actions/upload-artifact@v4 with: name: test-reports path: reports/
Best practices
Keep unit tests small, fast, and deterministic. Mock external services.
Group slower integration tests into a separate workflow or run them on a schedule.
Run tests locally before opening a PR to reduce iteration time.
Require CI green and coverage checks (if applicable) before merging.
Example local troubleshooting
If tests fail due to missing deps: ensure virtualenv is active and requirements are installed.
If imports fail: verify PYTHONPATH or package layout (use src/ layout with proper packaging or install the package in editable mode: pip install -e .)
Contributing
Contributions are welcome. Suggested workflow:

Fork the repo and create a feature branch: git checkout -b feat/my-change
Make changes and add/modify tests in tests/
Run tests locally (pytest)
Open a pull request and ensure CI passes
When opening PRs:

Ensure all tests pass in CI
Add or update tests for bug fixes and new features
Document noteworthy changes in the PR description
