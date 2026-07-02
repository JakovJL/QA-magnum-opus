# Automation Framework Architecture

## Table of Contents

- [[#What a Framework Is]]
- [[#Main Layers]]
- [[#Configuration and Test Data]]
- [[#Page Object Model]]
- [[#Good Architecture Rules]]
- [[#Interview Questions]]
	- [[#Beginner Questions]]
	- [[#Intermediate Questions]]
	- [[#Advanced Questions]]
	- [[#Code Questions]]

**Related notes:** [[AQA Java eng]]

---

## What a Framework Is

An **automation framework** is a structured project for writing, running, and maintaining automated tests.

**Goal:** make tests readable, reusable, stable, and easy to run locally or in CI.

A framework usually includes:

- test runner
- page or API clients
- assertions
- configuration
- test data
- logging and reporting
- utilities

---

## Main Layers

A common UI framework has several layers.

| Layer | Responsibility |
|---|---|
| tests | describe test scenarios |
| pages / components | hide UI locators and UI actions |
| services / clients | call API or backend helpers |
| config | browser, URL, timeouts, environment |
| utils | reusable small helpers |
| reporting | logs, screenshots, Allure steps |

**Example:** a test should say `loginPage.loginAs(user)`, not repeat locators and low-level clicks everywhere.

---

## Configuration and Test Data

Configuration controls how tests run.

Examples:

- base URL
- browser
- timeout
- environment name
- API token source

Test data can come from:

- builders
- factories
- JSON files
- databases
- API setup steps

> [!caution] Do not hardcode environment secrets
> Passwords and tokens should come from environment variables or CI secrets, not from source code.

---

## Page Object Model

**Page Object Model (POM)** is a pattern where each important page or component has a class.

**Goal:** keep locators and page actions in one place.

Advantages:

- less duplication
- easier maintenance when UI changes
- more readable tests

Disadvantages:

- bad page objects can become too large
- business assertions should not be hidden too deeply

→ see [[17 Selenium WebDriver Basics]] for Selenium basics and POM examples.

---

## Good Architecture Rules

Good framework code:

- separates test intent from technical details
- keeps one reason to change per class
- avoids duplicated locators and waits
- uses clear names
- fails with useful error messages
- stores reports and logs for debugging

Simple structure:

```text
src/test/java
  tests/
  pages/
  config/
  data/
  utils/
```

---

## Interview Questions

### Beginner Questions

**1. What is an automation framework?**
It is a structured project for automated tests.

**2. Why do we need layers?**
Layers separate test logic, UI actions, configuration, and utilities.

### Intermediate Questions

**3. What problem does POM solve?**
POM keeps locators and page actions in one place, so tests are easier to maintain.

**4. Where should environment settings live?**
In configuration files, environment variables, or CI secrets.

### Advanced Questions

**5. What makes a framework hard to maintain?**
Duplicated locators, hidden waits, hardcoded data, large classes, and unclear failures.

### Code Questions

**6. Which line is better for a test and why?**

```java
loginPage.loginAs(user);
driver.findElement(By.id("login")).sendKeys(user.login());
```

The first line is better in a test because it shows intent and hides low-level UI details in the page object.
