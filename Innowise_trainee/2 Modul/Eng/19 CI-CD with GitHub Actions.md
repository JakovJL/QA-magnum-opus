# CI/CD with GitHub Actions

## Table of Contents

- [[#What CI/CD Is]]
- [[#Pipeline Basics]]
- [[#GitHub Actions]]
- [[#AQA Pipeline Example]]
- [[#Common Problems]]
- [[#Interview Questions]]
	- [[#Beginner Questions]]
	- [[#Intermediate Questions]]
	- [[#Advanced Questions]]
	- [[#Code Questions]]

**Related notes:** [[AQA Java eng]]

---

## What CI/CD Is

**CI (Continuous Integration)** means that code is merged often and checked automatically.

**CD (Continuous Delivery or Deployment)** means that a successful build can be prepared for release or deployed automatically.

**Goal:** find problems early and make builds repeatable.

---

## Pipeline Basics

A **pipeline** is an ordered set of automated steps.

Typical steps:

1. Checkout code
2. Set up Java
3. Download dependencies
4. Compile the project
5. Run tests
6. Publish reports

Common CI/CD tools:

- GitHub Actions
- GitLab CI
- Jenkins
- TeamCity
- Azure Pipelines

---

## GitHub Actions

GitHub Actions uses workflow files in `.github/workflows`.

Basic workflow:

```yaml
name: Java tests

on:
  pull_request:
  push:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'
      - run: mvn test
```

Important terms:

| Term | Meaning |
|---|---|
| workflow | whole automation file |
| job | group of steps on one runner |
| step | one command or action |
| runner | machine that executes the job |
| artifact | file saved after a run, such as a report |

---

## AQA Pipeline Example

For an AQA project, a pipeline often runs:

- unit tests
- API tests
- UI smoke tests
- static checks
- Allure report generation

Good practice:

- run fast checks on every pull request
- run slower regression tests by schedule or manual trigger
- keep test data independent
- save screenshots, logs, and reports as artifacts

---

## Common Problems

| Problem | Typical cause |
|---|---|
| Test passes locally but fails in CI | different browser, OS, timing, or test data |
| Pipeline is too slow | too many UI tests or no parallelization |
| Report is missing | artifact upload step is absent |
| Red builds are ignored | flaky tests are not fixed |

> [!caution] CI must be trusted
> A flaky pipeline loses value quickly. Fix unstable tests or quarantine them with a clear reason.

---

## Interview Questions

### Beginner Questions

**1. What is CI?**
CI is automatic checking of code changes after push or pull request.

**2. What is a pipeline?**
A pipeline is a set of automated steps such as build, test, and report.

### Intermediate Questions

**3. Why should tests run in CI?**
CI catches problems early and gives the team quick feedback.

**4. What should an AQA pipeline save as artifacts?**
Reports, screenshots, logs, and sometimes test result files.

### Advanced Questions

**5. Why can a test fail only in CI?**
The CI environment can differ by browser version, screen size, timing, data, or parallel execution.

### Code Questions

**6. What does this workflow step do?**

```yaml
- run: mvn test
```

It runs Maven tests in the CI runner.
