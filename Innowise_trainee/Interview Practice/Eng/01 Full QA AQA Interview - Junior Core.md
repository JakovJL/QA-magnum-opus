# 01 Full QA AQA Interview - Junior Core

## Table of Contents

- [[#How to Use This Interview]]
- [[#40-Minute Interview Plan]]
- [[#Interview Questions]]
	- [[#Warm-Up]]
	- [[#Manual QA]]
	- [[#Practical QA Scenarios]]
	- [[#Web API Database]]
	- [[#Java Core]]
	- [[#JUnit 5 and TestNG]]
	- [[#Selenium and Automation Framework]]
	- [[#Tools and CI]]
	- [[#Final Mixed Questions]]
- [[#Scoring Checklist]]
- [[#Transcript Review Prompt]]

**Related notes:** [[QA manual eng]], [[AQA Java eng]]

---

## How to Use This Interview

Use this note as one full mock interview. Record your voice while answering in English. Do not read the expected points before answering.

Recommended flow:

1. Start recording.
2. Answer each main question aloud.
3. Answer one follow-up if you still have time.
4. After the recording, transcribe it with AI.
5. Use the review prompt at the end to find QA mistakes and English mistakes.

---

## 40-Minute Interview Plan

| Time | Block | Goal |
|---|---|---|
| 3 min | Warm-Up | Check self-introduction and English flow |
| 7 min | Manual QA | Check core testing theory |
| 7 min | Practical QA Scenarios | Check thinking, not memorization |
| 5 min | Web API Database | Check technical QA basics |
| 6 min | Java Core | Check Java foundation for AQA |
| 5 min | JUnit 5 and TestNG | Check test runner knowledge |
| 5 min | Selenium and Framework | Check UI automation basics |
| 2 min | Tools and Final | Check Git/CI/reporting awareness |

---

## Interview Questions

### Warm-Up

**1. Tell me about yourself and your QA learning path.**

Expected answer points:
- short introduction
- why QA / AQA
- what you have learned
- what you are practicing now

Follow-up:
- Why do you want to move from manual QA to automation?

Weak answer signs:
- too long
- no structure
- no concrete technologies

**2. Describe one project or practice task you worked on.**

Expected answer points:
- what the product or task was
- what you tested
- what tools you used
- what result you got

Follow-up:
- What was the hardest part?

Weak answer signs:
- only theory, no practical detail
- unclear personal role

### Manual QA

**3. What is software testing, and why do we need it?**

Expected answer points:
- testing checks product quality against requirements and expectations
- finds defects and reduces risk
- gives information for release decisions

Follow-up:
- Does testing prove that there are no bugs?

Weak answer signs:
- says testing guarantees quality
- focuses only on finding bugs

**4. Explain QA, QC, and testing.**

Expected answer points:
- QA is process-oriented and preventive
- QC is product-oriented and checks results
- testing is one QC activity

Follow-up:
- Give an example of QA activity before coding.

Weak answer signs:
- treats QA and testing as exactly the same

**5. What is the difference between functional and non-functional testing?**

Expected answer points:
- functional checks what the system does
- non-functional checks how well it works
- examples: login vs performance/security/usability

Follow-up:
- Can a product pass functional tests and still be bad?

Weak answer signs:
- no examples
- confuses performance with functional behavior

**6. What is regression testing?**

Expected answer points:
- checks that existing functionality still works after changes
- can be manual or automated
- usually focuses on high-risk and important areas

Follow-up:
- How is regression different from retesting?

Weak answer signs:
- says regression only checks fixed bugs

### Practical QA Scenarios

**7. You receive a requirement: "The page should load quickly." What do you do?**

Expected answer points:
- requirement is not measurable
- ask for a concrete performance target
- clarify conditions: device, network, load, percentile

Follow-up:
- Rewrite it as a testable requirement.

Weak answer signs:
- accepts "quickly" without questions

**8. A developer says your bug is not a bug but expected behavior. What do you do?**

Expected answer points:
- check requirements and acceptance criteria
- provide evidence
- discuss calmly
- escalate to PO/PM if requirements are unclear

Follow-up:
- What if there is no written requirement?

Weak answer signs:
- argues by opinion
- closes the bug without analysis

**9. Design tests for a password field.**

Expected answer points:
- valid password
- too short / too long
- boundary values
- empty value
- special characters
- copy-paste, spaces, error messages
- security expectations if covered by requirements

Follow-up:
- Which test design techniques did you use?

Weak answer signs:
- only one happy path
- no boundaries or negative tests

**10. How would you write a good bug report?**

Expected answer points:
- clear title
- environment
- steps to reproduce
- expected and actual result
- severity/priority
- attachments

Follow-up:
- What makes a bug report weak?

Weak answer signs:
- misses expected result or environment

### Web API Database

**11. What happens when you enter a URL in a browser?**

Expected answer points:
- browser sends HTTP request
- server processes it
- server returns response
- browser renders HTML/CSS/JS
- mention DNS at a high level if possible

Follow-up:
- What is the difference between frontend and backend?

Weak answer signs:
- no client-server idea

**12. What is the difference between GET and POST?**

Expected answer points:
- GET reads data
- POST sends data to create or process something
- GET parameters often in URL
- POST body carries data

Follow-up:
- Should sensitive data be sent in query parameters?

Weak answer signs:
- says POST is always secure

**13. What would you check in an API response?**

Expected answer points:
- status code
- response body
- headers
- schema/contract
- data correctness
- error messages
- response time when relevant

Follow-up:
- What is the difference between 401 and 403?

Weak answer signs:
- checks only status code

**14. Basic SQL: how do you get all users with status active?**

Expected answer points:
- `SELECT`
- `FROM`
- `WHERE`

Example answer:

```sql
SELECT *
FROM users
WHERE status = 'active';
```

Follow-up:
- Why should QA know basic SQL?

Weak answer signs:
- cannot explain filtering

### Java Core

**15. What is the difference between primitive and reference types in Java?**

Expected answer points:
- primitives store simple values
- references point to objects
- examples: `int`, `boolean`; `String`, `List`
- references can be `null`

Follow-up:
- What can happen if you call a method on `null`?

Weak answer signs:
- says everything in Java is an object

**16. Explain OOP principles in simple words.**

Expected answer points:
- encapsulation
- inheritance
- polymorphism
- abstraction
- simple examples

Follow-up:
- Which principle helps hide internal state?

Weak answer signs:
- lists terms without explanation

**17. What is the difference between `ArrayList` and `HashMap`?**

Expected answer points:
- `ArrayList` stores ordered elements by index
- `HashMap` stores key-value pairs
- use `ArrayList` for lists
- use `HashMap` for lookup by key

Follow-up:
- Can `HashMap` have duplicate keys?

Weak answer signs:
- says both are just arrays

**18. What is an exception, and how do you handle it?**

Expected answer points:
- exception is an abnormal situation during program execution
- `try-catch`
- `finally` or try-with-resources when needed
- do not hide errors silently

Follow-up:
- What is the difference between checked and unchecked exceptions?

Weak answer signs:
- says catch all exceptions everywhere

### JUnit 5 and TestNG

**19. What is JUnit 5 used for in AQA?**

Expected answer points:
- writing and running automated tests
- assertions
- lifecycle methods
- grouping/tagging
- works with Selenium/API tests

Follow-up:
- What does `@Test` mean?

Weak answer signs:
- thinks JUnit is only for developers

**20. What are common JUnit 5 lifecycle annotations?**

Expected answer points:
- `@BeforeEach`
- `@AfterEach`
- `@BeforeAll`
- `@AfterAll`
- setup and cleanup

Follow-up:
- Where would you open and close WebDriver?

Weak answer signs:
- no setup/cleanup concept

**21. What is TestNG, and how is it different from JUnit 5?**

Expected answer points:
- TestNG is another Java testing framework
- often used in Selenium projects
- strong suite XML, groups, dependencies, parallel execution
- JUnit 5 is modern, clean, widely used, strong in unit and general testing

Follow-up:
- What are TestNG groups similar to in JUnit 5?

Weak answer signs:
- says one is always better without context

**22. How do assertions work?**

Expected answer points:
- assertion compares expected and actual result
- test fails if assertion is false
- examples: `assertEquals`, `assertTrue`

Follow-up:
- Why is "no exception" not always enough for a test?

Weak answer signs:
- no expected vs actual idea

### Selenium and Automation Framework

**23. What is Selenium WebDriver?**

Expected answer points:
- browser automation tool
- controls browser through WebDriver API
- finds elements, clicks, types, reads data
- used for UI tests

Follow-up:
- What is the difference between Selenium and JUnit/TestNG?

Weak answer signs:
- says Selenium is a test runner

**24. What are locators, and which ones do you know?**

Expected answer points:
- locators find elements on the page
- `id`, `name`, `cssSelector`, `xpath`, `linkText`
- stable locators are important

Follow-up:
- Why is XPath sometimes fragile?

Weak answer signs:
- no idea how elements are found

**25. Why do we need waits in Selenium?**

Expected answer points:
- web pages are dynamic
- element may not be ready immediately
- explicit waits wait for a condition
- fixed sleeps are slow and flaky

Follow-up:
- Give an example of a condition to wait for.

Weak answer signs:
- recommends `Thread.sleep()` as main solution

**26. What is Page Object Model?**

Expected answer points:
- pattern that stores locators and page actions in page classes
- makes tests cleaner
- reduces duplication
- easier maintenance

Follow-up:
- What should not be hidden inside page objects?

Weak answer signs:
- page object becomes a place for all test logic

### Tools and CI

**27. Why does an AQA engineer need Git?**

Expected answer points:
- version control for test code
- branches and pull requests
- team collaboration
- CI trigger

Follow-up:
- What should you check before pushing code?

Weak answer signs:
- Git only as file storage

**28. What is CI/CD, and why is it useful for tests?**

Expected answer points:
- CI runs checks automatically on changes
- CD prepares or deploys successful builds
- automated tests give fast feedback
- reports help analysis

Follow-up:
- What can make CI unreliable?

Weak answer signs:
- no pipeline concept

### Final Mixed Questions

**29. A UI test passes locally but fails in CI. What can be the reason?**

Expected answer points:
- browser/version difference
- timing
- screen size
- test data
- environment variables
- parallel execution

Follow-up:
- What evidence would you collect?

Weak answer signs:
- says "CI is broken" without investigation

**30. What are your next learning goals?**

Expected answer points:
- concrete topics
- realistic plan
- connection to QA/AQA role
- awareness of weak areas

Follow-up:
- How will you practice English for interviews?

Weak answer signs:
- no plan
- only vague motivation

---

## Scoring Checklist

Use this after listening to the recording.

| Area | Max score | Notes |
|---|---:|---|
| English clarity | 10 | clear sentences, understandable pronunciation |
| QA theory | 10 | correct core definitions |
| Practical thinking | 10 | asks clarifying questions, explains risk |
| Technical basics | 10 | Web, API, DB, Java basics |
| Automation | 10 | JUnit/TestNG, Selenium, framework, CI |
| Structure | 10 | answers are organized and not too long |

Total: 60 points.

Suggested result:

- 50-60: strong junior interview
- 40-49: good, but needs cleaner details
- 30-39: basic level, needs practice
- below 30: repeat this interview after reviewing notes

---

## Transcript Review Prompt

Use this prompt after you transcribe your recording:

```text
You are my QA/AQA interview coach and English teacher.

I will paste a transcript of my mock interview answer.
Please review it in four parts:

1. English corrections:
- grammar mistakes
- word choice mistakes
- better B1/B2 phrasing

2. QA/AQA content:
- incorrect statements
- missing important points
- weak or vague explanations

3. Interview quality:
- was the answer structured?
- was it too short or too long?
- did it sound confident?

4. Improved answer:
- rewrite my answer in clear spoken English
- keep it realistic for a Junior QA/AQA interview

Transcript:
[paste transcript here]
```
