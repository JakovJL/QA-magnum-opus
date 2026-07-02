# 02 Полное QA AQA интервью - Практические сценарии

## Содержание

- [[#Как использовать это интервью]]
- [[#План интервью на 40 минут]]
- [[#Вопросы интервью]]
	- [[#Разогрев]]
	- [[#Manual QA]]
	- [[#Практические QA-сценарии]]
	- [[#Web API Database]]
	- [[#Java Core]]
	- [[#JUnit 5 и TestNG]]
	- [[#Selenium и automation framework]]
	- [[#Инструменты и CI]]
	- [[#Финальные смешанные вопросы]]
- [[#Чеклист оценки]]
- [[#Prompt для проверки транскрипта]]

**Связанные заметки:** [[QA manual rus]], [[AQA Java rus]]

---

## Как использовать это интервью

Используй эту заметку как второе полное mock interview. Оно более scenario-based, чем Interview 01. Записывай ответы на английском и старайся объяснять ход мыслей step by step.

Рекомендуемый порядок:

1. Начни запись.
2. Отвечай, не читая expected points.
3. Держи большинство ответов короче 90 seconds.
4. После записи сделай transcript через AI.
5. Используй prompt в конце.

---

## План интервью на 40 минут

| Время | Блок | Цель |
|---|---|---|
| 3 min | Разогрев | Проверить clear self-presentation |
| 6 min | Manual QA | Проверить core theory with examples |
| 9 min | Практические QA-сценарии | Проверить real testing decisions |
| 5 min | Web API Database | Проверить backend-facing QA basics |
| 6 min | Java Core | Проверить collections, strings, exceptions |
| 5 min | JUnit 5 и TestNG | Проверить runner and lifecycle understanding |
| 4 min | Selenium и framework | Проверить UI automation decisions |
| 2 min | Инструменты и финал | Проверить release and learning mindset |

---

## Вопросы интервью

### Разогрев

**1. Tell me about your QA experience and what you are learning now.**

Ожидаемые пункты:
- short background
- Manual QA foundation
- Java/AQA learning path
- current practice focus

Follow-up:
- What topic is hardest for you now?

Признаки слабого ответа:
- answer is too generic
- no connection to QA/AQA role

**2. Why should we consider you for a Junior QA/AQA position?**

Ожидаемые пункты:
- motivation
- learning discipline
- basic QA knowledge
- ability to practice and improve

Follow-up:
- What can you already do independently?

Признаки слабого ответа:
- only says "I am motivated"
- no concrete skills

### Manual QA

**3. What is the difference between verification and validation?**

Ожидаемые пункты:
- verification asks "Are we building the product right?"
- validation asks "Are we building the right product?"
- examples: requirement review vs user acceptance

Follow-up:
- Is testing more verification or validation?

Признаки слабого ответа:
- repeats definitions without examples

**4. What is a test case, and what fields should it contain?**

Ожидаемые пункты:
- test case is a documented check
- preconditions
- steps
- test data
- expected result
- actual result/status when executed

Follow-up:
- When would you use a checklist instead?

Признаки слабого ответа:
- misses expected result
- cannot explain why documentation matters

**5. What is smoke testing?**

Ожидаемые пункты:
- quick basic check of build stability
- decides whether deeper testing makes sense
- covers critical paths
- can be automated

Follow-up:
- How is smoke different from sanity testing?

Признаки слабого ответа:
- says smoke is full regression

**6. What is severity and priority?**

Ожидаемые пункты:
- severity is impact of defect
- priority is urgency to fix
- QA can suggest, business often decides priority
- examples of high severity/low priority and low severity/high priority

Follow-up:
- Who should set final priority?

Признаки слабого ответа:
- treats them as the same field

### Практические QA-сценарии

**7. You have only two hours to test a changed checkout flow. What do you test first?**

Ожидаемые пункты:
- clarify change scope
- smoke critical path
- payment, totals, delivery, order creation
- regression around affected areas
- risk-based prioritization

Follow-up:
- What would you explicitly leave out and report?

Признаки слабого ответа:
- tries to test everything equally

**8. A bug appears only once and you cannot reproduce it. What do you do?**

Ожидаемые пункты:
- collect evidence
- check logs, environment, data, user account, time
- try variations
- document as intermittent if enough evidence exists
- do not close too quickly

Follow-up:
- What information helps developers investigate?

Признаки слабого ответа:
- ignores the bug because it is not reproducible once

**9. Requirements changed after test cases were written. What should QA update?**

Ожидаемые пункты:
- test cases
- checklist
- RTM
- test data
- regression scope
- maybe bug reports if expected behavior changed

Follow-up:
- What is the risk if RTM is not updated?

Признаки слабого ответа:
- updates only one test case

**10. A release has one open minor bug. Can it be released?**

Ожидаемые пункты:
- depends on risk and business decision
- check severity, priority, affected users
- document known issue
- stakeholders accept or reject risk

Follow-up:
- What if the bug is minor but on the main page?

Признаки слабого ответа:
- says always yes or always no

**11. Design tests for a file upload feature.**

Ожидаемые пункты:
- valid file type and size
- invalid type
- too large file
- empty file
- duplicate file
- slow network/interruption
- progress/error messages
- security checks if relevant

Follow-up:
- Which cases are smoke, and which are deeper regression?

Признаки слабого ответа:
- only uploads one valid file

### Web API Database

**12. What is an HTTP status code? Give examples.**

Ожидаемые пункты:
- status code describes response result
- 200 OK
- 201 Created
- 400 Bad Request
- 401 Unauthorized
- 403 Forbidden
- 404 Not Found
- 500 Server Error

Follow-up:
- What is the difference between 400 and 500?

Признаки слабого ответа:
- knows only 200 and 404

**13. How would you test login through API?**

Ожидаемые пункты:
- valid credentials
- invalid password
- missing fields
- locked user
- status codes
- token/cookie
- response body and security

Follow-up:
- What should not be returned in the response?

Признаки слабого ответа:
- checks only positive login

**14. What is the difference between SQL `WHERE` and `ORDER BY`?**

Ожидаемые пункты:
- `WHERE` filters rows
- `ORDER BY` sorts result
- they solve different tasks

Пример:

```sql
SELECT *
FROM bugs
WHERE status = 'Open'
ORDER BY severity;
```

Follow-up:
- How would you count open bugs?

Признаки слабого ответа:
- confuses filtering and sorting

### Java Core

**15. What is the difference between `String`, `StringBuilder`, and `StringBuffer`?**

Ожидаемые пункты:
- `String` is immutable
- `StringBuilder` is mutable and faster for local string changes
- `StringBuffer` is synchronized
- in most simple cases use `String` or `StringBuilder`

Follow-up:
- Why can many string concatenations be inefficient?

Признаки слабого ответа:
- says they are identical

**16. What is the difference between `List`, `Set`, and `Map`?**

Ожидаемые пункты:
- `List` keeps ordered elements and allows duplicates
- `Set` stores unique elements
- `Map` stores key-value pairs
- examples from tests or bug data

Follow-up:
- Which one would you use for unique usernames?

Признаки слабого ответа:
- no real use cases

**17. Explain `equals()` and `hashCode()` in simple words.**

Ожидаемые пункты:
- `equals()` compares logical equality
- `hashCode()` supports hash-based collections
- if objects are equal, hash codes must be equal
- important for `HashSet` and `HashMap`

Follow-up:
- What can go wrong if only `equals()` is overridden?

Признаки слабого ответа:
- says `==` and `equals()` are always the same

**18. What is the difference between checked and unchecked exceptions?**

Ожидаемые пункты:
- checked exceptions must be handled or declared
- unchecked exceptions are runtime exceptions
- examples: `IOException`, `NullPointerException`
- do not catch exceptions without reason

Follow-up:
- What exception can happen when reading a missing file?

Признаки слабого ответа:
- says all exceptions are the same

### JUnit 5 и TestNG

**19. Compare JUnit 5 `@BeforeEach` and TestNG `@BeforeMethod`.**

Ожидаемые пункты:
- both run before each test method
- used for setup
- TestNG has a different annotation model but similar lifecycle idea

Follow-up:
- Which cleanup annotation matches them?

Признаки слабого ответа:
- does not understand test lifecycle

**20. What are parameterized tests?**

Ожидаемые пункты:
- same test logic runs with different input data
- reduces duplication
- JUnit 5 has `@ParameterizedTest`
- TestNG has `@DataProvider`

Follow-up:
- Give an example for login validation.

Признаки слабого ответа:
- writes many copied tests instead

**21. What are groups in TestNG and tags in JUnit 5 used for?**

Ожидаемые пункты:
- run selected subsets of tests
- smoke, regression, api, ui
- useful in CI
- helps organize suites

Follow-up:
- Which tests would you run on every pull request?

Признаки слабого ответа:
- no connection to CI or test selection

**22. Why should assertions be specific?**

Ожидаемые пункты:
- they prove expected behavior
- failure message becomes clearer
- weak assertions miss bugs
- one test should verify meaningful outcome

Follow-up:
- Is checking only that the page opened enough?

Признаки слабого ответа:
- relies only on no exceptions

### Selenium и automation framework

**23. What is the difference between `findElement()` and `findElements()`?**

Ожидаемые пункты:
- `findElement()` returns first matching element or throws exception
- `findElements()` returns a list, empty if none found
- useful for optional elements

Follow-up:
- Which one would you use to check that no error messages are visible?

Признаки слабого ответа:
- does not know exception behavior

**24. What makes a locator stable?**

Ожидаемые пункты:
- unique
- not based on changing text or dynamic indexes
- preferably id, data-test attribute, stable CSS
- readable and maintainable

Follow-up:
- Why can absolute XPath be bad?

Признаки слабого ответа:
- chooses the longest XPath from DevTools

**25. What is a flaky test?**

Ожидаемые пункты:
- test passes and fails without product changes
- causes: timing, data, environment, order dependency
- must be fixed or quarantined

Follow-up:
- How can waits reduce flakiness?

Признаки слабого ответа:
- says rerun until green

**26. What should be inside an automation framework besides tests?**

Ожидаемые пункты:
- configuration
- page objects or API clients
- test data
- utilities
- logging
- reporting
- CI setup

Follow-up:
- Where should passwords and tokens be stored?

Признаки слабого ответа:
- framework means only test classes

### Инструменты и CI

**27. What is the purpose of a pull request?**

Ожидаемые пункты:
- review code before merge
- discuss changes
- run CI checks
- keep main branch stable

Follow-up:
- What should a reviewer check in test automation code?

Признаки слабого ответа:
- pull request is only a formality

**28. What is Allure used for?**

Ожидаемые пункты:
- test reporting
- steps, screenshots, attachments
- failure analysis
- trends/history when configured

Follow-up:
- What would you attach for a failed UI test?

Признаки слабого ответа:
- says it runs tests

### Финальные смешанные вопросы

**29. You join a project with no test documentation. What are your first steps?**

Ожидаемые пункты:
- learn product and risks
- talk to team
- review requirements if any
- create smoke checklist
- document critical flows first
- improve gradually

Follow-up:
- What would you not do in the first week?

Признаки слабого ответа:
- tries to document everything immediately

**30. What makes you ready for a Junior QA/AQA interview?**

Ожидаемые пункты:
- can explain basics
- can solve scenarios
- can write simple Java
- understands Selenium/JUnit/TestNG basics
- can learn from feedback

Follow-up:
- What answer from today would you improve?

Признаки слабого ответа:
- overconfidence without examples

---

## Чеклист оценки

| Область | Максимум | Заметки |
|---|---:|---|
| English clarity | 10 | answers are understandable and structured |
| Manual QA | 10 | testing types, artifacts, bugs, requirements |
| Scenario thinking | 10 | risk-based decisions and clarification |
| Web API Database | 10 | HTTP, API checks, SQL basics |
| Java Core | 10 | strings, collections, exceptions |
| Automation | 10 | JUnit/TestNG, Selenium, framework, CI |

Total: 60 points.

Результат:

- 50-60: strong junior interview
- 40-49: good, but needs cleaner explanations
- 30-39: basic level, repeat weak blocks
- below 30: review notes and retry

---

## Prompt для проверки транскрипта

```text
You are my QA/AQA interview coach and English teacher.

Review my transcript in five parts:

1. Correct my English:
- grammar
- word choice
- sentence structure
- pronunciation hints if a phrase may sound unnatural

2. Check QA/AQA accuracy:
- wrong ideas
- missing key points
- unclear terminology

3. Check answer structure:
- did I answer directly?
- did I give examples?
- was the answer too long?

4. Give a score from 0 to 10 for:
- English
- QA content
- technical content
- interview confidence

5. Rewrite my answer in clear spoken English for a Junior QA/AQA interview.

Transcript:
[paste transcript here]
```
