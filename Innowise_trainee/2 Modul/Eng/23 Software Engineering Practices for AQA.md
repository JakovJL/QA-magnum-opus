# Software Engineering Practices for AQA

## Table of Contents

- [[#Why Engineering Practices Matter]]
- [[#Clean Code for Tests]]
- [[#Code Review and Team Standards]]
- [[#Test Reliability]]
- [[#Maintainability Practices]]
- [[#Interview Questions]]
	- [[#Beginner Questions]]
	- [[#Intermediate Questions]]
	- [[#Advanced Questions]]
	- [[#Code Questions]]

**Related notes:** [[AQA Java eng]]

---

## Why Engineering Practices Matter

AQA code is production code for testing. If it is messy, the team loses trust in automation.

**Goal:** make test code easy to read, change, review, and debug.

Important practices:

- readable names
- small methods
- clear structure
- code review
- stable tests
- simple design

---

## Clean Code for Tests

Good test code shows intent.

```java
loginPage.loginAs(admin);
dashboard.shouldShowUserName(admin.name());
```

Poor test code hides intent in technical details.

```java
driver.findElement(By.id("u")).sendKeys("admin");
driver.findElement(By.id("p")).sendKeys("123");
```

Good rules:

- one test checks one main behavior
- test names describe the scenario
- helpers are simple and reusable
- assertions have clear messages
- duplicated setup is extracted carefully

---

## Code Review and Team Standards

Code review improves test quality before changes reach the main branch.

Reviewers check:

- readability
- reliable waits
- no hardcoded secrets
- good test data handling
- useful assertions
- no unnecessary duplication

Team standards can include naming rules, package structure, branch rules, and report requirements.

---

## Test Reliability

A reliable test fails only when there is a real problem.

Common causes of unreliable tests:

- fixed sleeps instead of waits
- shared test data
- order dependency
- environment dependency
- weak locators
- tests that depend on local state

> [!danger] Core rule
> A flaky test is a project problem, not a normal state. Fix it, quarantine it, or remove it.

---

## Maintainability Practices

Maintainable AQA projects:

- keep framework code separated from test scenarios
- reuse page objects and API clients
- keep configuration outside test logic
- update dependencies deliberately
- document non-obvious decisions
- delete dead tests and unused helpers

Simple design is usually better than a complex framework that the team does not understand.

---

## Interview Questions

### Beginner Questions

**1. Why should test code be clean?**
Clean test code is easier to read, maintain, and debug.

**2. What is code review?**
Code review is checking changes before merging them.

### Intermediate Questions

**3. What makes a test flaky?**
Timing issues, shared data, weak locators, order dependency, or environment differences.

**4. Why are fixed sleeps bad?**
They make tests slow and still do not guarantee that the page is ready.

### Advanced Questions

**5. How can an AQA engineer reduce maintenance cost?**
Use clear structure, reusable helpers, stable locators, configuration, and small focused tests.

### Code Questions

**6. What is the problem here?**

```java
Thread.sleep(5000);
driver.findElement(By.id("save")).click();
```

The fixed sleep is unreliable and slow. Use an explicit wait for the save button instead.
