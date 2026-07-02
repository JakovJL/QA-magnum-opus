# Logging Reporting and Metrics

## Table of Contents

- [[#Logging]]
- [[#Test Reporting]]
- [[#Allure Reports]]
- [[#Metrics]]
- [[#Good Practices]]
- [[#Interview Questions]]
	- [[#Beginner Questions]]
	- [[#Intermediate Questions]]
	- [[#Advanced Questions]]
	- [[#Code Questions]]

**Related notes:** [[AQA Java eng]]

---

## Logging

**Logging** records what happened during a test run.

**Goal:** help debug failures after the run is finished, especially in CI.

Useful logs include:

- test start and finish
- environment and browser
- important test data ids
- API request and response status
- error messages

Common Java logging tools:

- SLF4J
- Logback
- Log4j2

> [!caution] Do not log secrets
> Never write passwords, tokens, cookies, or personal data into logs.

---

## Test Reporting

A **test report** shows test results in a readable form.

Typical report data:

- passed, failed, skipped tests
- failure reason
- screenshots
- logs
- execution time
- links to requirements or issues

Reports help QA engineers, developers, and managers understand the state of quality.

---

## Allure Reports

**Allure** is a popular reporting tool for Java AQA projects.

It can show:

- test suites
- steps
- attachments
- screenshots
- history and trends
- severity and tags

Example idea:

```java
Allure.step("Open login page");
```

**When to use:** UI and API automation where readable failure analysis is important.

---

## Metrics

**Metrics** are numbers used to understand test work and product quality.

Useful automation metrics:

| Metric | Meaning |
|---|---|
| pass rate | passed tests / executed tests |
| failure rate | failed tests / executed tests |
| flaky test count | tests that fail without real product bug |
| execution time | how long the suite runs |
| defect leakage | bugs found after release |

Metrics are helpful only when the team understands their context.

---

## Good Practices

- log enough to reproduce and debug failures
- attach screenshots for UI failures
- attach API request/response details without secrets
- keep report names and test names readable
- track flaky tests separately
- do not use metrics to blame people

> [!tip] Good report rule
> A good report should answer: what failed, where it failed, why it probably failed, and what evidence exists.

---

## Interview Questions

### Beginner Questions

**1. Why do automated tests need logs?**
Logs help understand what happened during a failed run.

**2. What is a test report?**
A test report is a readable summary of test results.

### Intermediate Questions

**3. What should be attached to a UI failure?**
A screenshot, logs, and sometimes page source or browser console logs.

**4. What is Allure used for?**
Allure creates rich test reports with steps, attachments, and history.

### Advanced Questions

**5. Why is pass rate not enough as a quality metric?**
Pass rate can look good even if important areas are not covered or tests are weak.

### Code Questions

**6. What is dangerous here?**

```java
log.info("Token: " + token);
```

It logs a secret token. Sensitive data must be masked or not logged.
