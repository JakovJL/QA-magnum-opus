# Logging Reporting and Metrics

## Table of Contents

- [[#Logging]]
- [[#Test Reporting]]
- [[#Allure Reports]]
- [[#Metrics]]
	- [[#Stability Metrics]]
	- [[#Speed Metrics]]
	- [[#Coverage And Value Metrics]]
	- [[#Quality Outcome Metrics]]
	- [[#Maintenance Metrics]]
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

**Metrics** are numbers used to understand test work, product quality, and the real value of automation.

**Goal:** Understand whether automation makes feedback faster, regression more stable, and maintenance cheaper.

It is better to look at several groups of metrics, not only one number.

### Stability Metrics

These metrics show how reliable the test suite is.

| Metric | Meaning |
|---|---|
| pass rate | passed tests / executed tests |
| failure rate | failed tests / executed tests |
| flaky test count | tests that fail without real product bug |
| rerun rate | how often tests need to be rerun |

### Speed Metrics

These metrics show how quickly the team gets feedback.

| Metric | Meaning |
|---|---|
| execution time | how long the suite runs |
| feedback time in CI | how long it takes from commit to result |
| time to detect failure | how quickly the team notices a problem |

### Coverage And Value Metrics

These metrics help show whether automation covers what really matters.

| Metric | Meaning |
|---|---|
| critical flow coverage | whether key user flows are covered by automated tests |
| regression coverage | how much of important regression is automated |
| API/UI/unit balance | whether the suite is too heavy on expensive UI tests |

### Quality Outcome Metrics

These metrics show the final product impact of automation.

| Metric | Meaning |
|---|---|
| defect leakage | bugs found after release |
| escaped critical defects | how many critical bugs reached production |
| bug detection before release | how many defects automation found before release |

### Maintenance Metrics

These metrics show the cost of keeping the suite healthy.

| Metric | Meaning |
|---|---|
| false positive rate | how many failures are not real product defects |
| time to fix broken tests | how long it takes to repair tests after changes |
| maintenance effort | how much team effort goes to supporting the suite |

**Metrics that are especially useful for automation effectiveness:**
- flaky test count or false positive rate
- execution time and feedback time in CI
- regression coverage of critical flows
- defect leakage after release
- time to fix broken tests

> [!tip] Metrics that are easy to misunderstand
> A high `pass rate` alone does not mean good automation. You can have 98% passed tests and still have weak coverage, many flaky tests, or slow feedback. Metrics are useful only in context.

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

**6. Which metrics help evaluate automation effectiveness?**
Teams usually look at stability (`flaky test count`, `false positive rate`), speed (`execution time`, `feedback time in CI`), coverage of important regression, and final quality outcomes (`defect leakage`). Good automation is not only often executed; it gives fast, stable, and useful feedback.

### Code Questions

**7. What is dangerous here?**

```java
log.info("Token: " + token);
```

It logs a secret token. Sensitive data must be masked or not logged.
