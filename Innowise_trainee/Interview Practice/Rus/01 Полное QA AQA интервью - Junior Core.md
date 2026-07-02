# 01 Полное QA AQA интервью - Junior Core

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

Используй эту заметку как одно полное mock interview. Записывай голос, пока отвечаешь на английском. Не читай expected points до ответа.

Рекомендуемый порядок:

1. Начни запись.
2. Ответь вслух на каждый main question.
3. Ответь на один follow-up, если осталось время.
4. После записи сделай transcript через AI.
5. Используй prompt в конце, чтобы найти QA mistakes и English mistakes.

---

## План интервью на 40 минут

| Время | Блок | Цель |
|---|---|---|
| 3 min | Разогрев | Проверить self-introduction и English flow |
| 7 min | Manual QA | Проверить базовую теорию тестирования |
| 7 min | Практические QA-сценарии | Проверить мышление, а не запоминание |
| 5 min | Web API Database | Проверить техническую базу QA |
| 6 min | Java Core | Проверить Java foundation для AQA |
| 5 min | JUnit 5 и TestNG | Проверить знание test runners |
| 5 min | Selenium и framework | Проверить основы UI automation |
| 2 min | Инструменты и финал | Проверить Git/CI/reporting awareness |

---

## Вопросы интервью

### Разогрев

**1. Tell me about yourself and your QA learning path.**

Ожидаемые пункты:
- короткое introduction
- почему QA / AQA
- что ты изучил
- что практикуешь сейчас

Follow-up:
- Why do you want to move from manual QA to automation?

Признаки слабого ответа:
- слишком длинно
- нет структуры
- нет конкретных technologies

**2. Describe one project or practice task you worked on.**

Ожидаемые пункты:
- что это был за product или task
- что ты testing
- какие tools использовал
- какой result получил

Follow-up:
- What was the hardest part?

Признаки слабого ответа:
- только теория, без practical detail
- непонятна личная роль

### Manual QA

**3. What is software testing, and why do we need it?**

Ожидаемые пункты:
- testing проверяет product quality по requirements и expectations
- находит defects и снижает risk
- даёт information для release decisions

Follow-up:
- Does testing prove that there are no bugs?

Признаки слабого ответа:
- говорит, что testing гарантирует quality
- фокусируется только на поиске bugs

**4. Explain QA, QC, and testing.**

Ожидаемые пункты:
- QA ориентирован на process и prevention
- QC ориентирован на product и проверяет result
- testing — одна из QC activities

Follow-up:
- Give an example of QA activity before coding.

Признаки слабого ответа:
- считает QA и testing полностью одинаковыми

**5. What is the difference between functional and non-functional testing?**

Ожидаемые пункты:
- functional проверяет, что system does
- non-functional проверяет, how well it works
- примеры: login vs performance/security/usability

Follow-up:
- Can a product pass functional tests and still be bad?

Признаки слабого ответа:
- нет examples
- путает performance с functional behavior

**6. What is regression testing?**

Ожидаемые пункты:
- проверяет, что existing functionality работает после changes
- может быть manual или automated
- обычно фокусируется на high-risk и important areas

Follow-up:
- How is regression different from retesting?

Признаки слабого ответа:
- говорит, что regression проверяет только fixed bugs

### Практические QA-сценарии

**7. You receive a requirement: "The page should load quickly." What do you do?**

Ожидаемые пункты:
- requirement не measurable
- нужно попросить конкретный performance target
- уточнить conditions: device, network, load, percentile

Follow-up:
- Rewrite it as a testable requirement.

Признаки слабого ответа:
- принимает "quickly" без вопросов

**8. A developer says your bug is not a bug but expected behavior. What do you do?**

Ожидаемые пункты:
- проверить requirements и acceptance criteria
- дать evidence
- спокойно обсудить
- escalate к PO/PM, если requirements unclear

Follow-up:
- What if there is no written requirement?

Признаки слабого ответа:
- спорит мнениями
- закрывает bug без анализа

**9. Design tests for a password field.**

Ожидаемые пункты:
- valid password
- too short / too long
- boundary values
- empty value
- special characters
- copy-paste, spaces, error messages
- security expectations, если они есть в requirements

Follow-up:
- Which test design techniques did you use?

Признаки слабого ответа:
- только happy path
- нет boundaries или negative tests

**10. How would you write a good bug report?**

Ожидаемые пункты:
- clear title
- environment
- steps to reproduce
- expected и actual result
- severity/priority
- attachments

Follow-up:
- What makes a bug report weak?

Признаки слабого ответа:
- нет expected result или environment

### Web API Database

**11. What happens when you enter a URL in a browser?**

Ожидаемые пункты:
- browser отправляет HTTP request
- server обрабатывает его
- server возвращает response
- browser renders HTML/CSS/JS
- можно кратко упомянуть DNS

Follow-up:
- What is the difference between frontend and backend?

Признаки слабого ответа:
- нет client-server idea

**12. What is the difference between GET and POST?**

Ожидаемые пункты:
- GET reads data
- POST sends data to create или process something
- GET parameters часто в URL
- POST body carries data

Follow-up:
- Should sensitive data be sent in query parameters?

Признаки слабого ответа:
- говорит, что POST всегда secure

**13. What would you check in an API response?**

Ожидаемые пункты:
- status code
- response body
- headers
- schema/contract
- data correctness
- error messages
- response time when relevant

Follow-up:
- What is the difference between 401 and 403?

Признаки слабого ответа:
- проверяет только status code

**14. Basic SQL: how do you get all users with status active?**

Ожидаемые пункты:
- `SELECT`
- `FROM`
- `WHERE`

Пример ответа:

```sql
SELECT *
FROM users
WHERE status = 'active';
```

Follow-up:
- Why should QA know basic SQL?

Признаки слабого ответа:
- не может объяснить filtering

### Java Core

**15. What is the difference between primitive and reference types in Java?**

Ожидаемые пункты:
- primitives хранят простые values
- references указывают на objects
- примеры: `int`, `boolean`; `String`, `List`
- references могут быть `null`

Follow-up:
- What can happen if you call a method on `null`?

Признаки слабого ответа:
- говорит, что everything in Java is an object

**16. Explain OOP principles in simple words.**

Ожидаемые пункты:
- encapsulation
- inheritance
- polymorphism
- abstraction
- simple examples

Follow-up:
- Which principle helps hide internal state?

Признаки слабого ответа:
- перечисляет terms без explanation

**17. What is the difference between `ArrayList` and `HashMap`?**

Ожидаемые пункты:
- `ArrayList` stores ordered elements by index
- `HashMap` stores key-value pairs
- `ArrayList` для lists
- `HashMap` для lookup by key

Follow-up:
- Can `HashMap` have duplicate keys?

Признаки слабого ответа:
- говорит, что оба просто arrays

**18. What is an exception, and how do you handle it?**

Ожидаемые пункты:
- exception — abnormal situation during program execution
- `try-catch`
- `finally` или try-with-resources when needed
- не скрывать errors silently

Follow-up:
- What is the difference between checked and unchecked exceptions?

Признаки слабого ответа:
- предлагает catch all exceptions везде

### JUnit 5 и TestNG

**19. What is JUnit 5 used for in AQA?**

Ожидаемые пункты:
- writing and running automated tests
- assertions
- lifecycle methods
- grouping/tagging
- works with Selenium/API tests

Follow-up:
- What does `@Test` mean?

Признаки слабого ответа:
- думает, что JUnit только для developers

**20. What are common JUnit 5 lifecycle annotations?**

Ожидаемые пункты:
- `@BeforeEach`
- `@AfterEach`
- `@BeforeAll`
- `@AfterAll`
- setup and cleanup

Follow-up:
- Where would you open and close WebDriver?

Признаки слабого ответа:
- нет setup/cleanup concept

**21. What is TestNG, and how is it different from JUnit 5?**

Ожидаемые пункты:
- TestNG — другой Java testing framework
- часто используется в Selenium projects
- strong suite XML, groups, dependencies, parallel execution
- JUnit 5 современный, clean, widely used, strong in unit and general testing

Follow-up:
- What are TestNG groups similar to in JUnit 5?

Признаки слабого ответа:
- говорит, что один всегда лучше без context

**22. How do assertions work?**

Ожидаемые пункты:
- assertion сравнивает expected и actual result
- test fails, если assertion false
- examples: `assertEquals`, `assertTrue`

Follow-up:
- Why is "no exception" not always enough for a test?

Признаки слабого ответа:
- нет expected vs actual idea

### Selenium и automation framework

**23. What is Selenium WebDriver?**

Ожидаемые пункты:
- browser automation tool
- controls browser через WebDriver API
- finds elements, clicks, types, reads data
- используется для UI tests

Follow-up:
- What is the difference between Selenium and JUnit/TestNG?

Признаки слабого ответа:
- говорит, что Selenium — test runner

**24. What are locators, and which ones do you know?**

Ожидаемые пункты:
- locators find elements on the page
- `id`, `name`, `cssSelector`, `xpath`, `linkText`
- stable locators are important

Follow-up:
- Why is XPath sometimes fragile?

Признаки слабого ответа:
- не понимает, как находятся elements

**25. Why do we need waits in Selenium?**

Ожидаемые пункты:
- web pages are dynamic
- element может быть не ready immediately
- explicit waits wait for a condition
- fixed sleeps are slow and flaky

Follow-up:
- Give an example of a condition to wait for.

Признаки слабого ответа:
- предлагает `Thread.sleep()` как основное решение

**26. What is Page Object Model?**

Ожидаемые пункты:
- pattern, где locators и page actions лежат в page classes
- делает tests cleaner
- reduces duplication
- easier maintenance

Follow-up:
- What should not be hidden inside page objects?

Признаки слабого ответа:
- page object становится местом для всей test logic

### Инструменты и CI

**27. Why does an AQA engineer need Git?**

Ожидаемые пункты:
- version control для test code
- branches и pull requests
- team collaboration
- CI trigger

Follow-up:
- What should you check before pushing code?

Признаки слабого ответа:
- Git только как file storage

**28. What is CI/CD, and why is it useful for tests?**

Ожидаемые пункты:
- CI запускает checks automatically on changes
- CD prepares или deploys successful builds
- automated tests дают fast feedback
- reports помогают analysis

Follow-up:
- What can make CI unreliable?

Признаки слабого ответа:
- нет pipeline concept

### Финальные смешанные вопросы

**29. A UI test passes locally but fails in CI. What can be the reason?**

Ожидаемые пункты:
- browser/version difference
- timing
- screen size
- test data
- environment variables
- parallel execution

Follow-up:
- What evidence would you collect?

Признаки слабого ответа:
- говорит "CI is broken" без investigation

**30. What are your next learning goals?**

Ожидаемые пункты:
- concrete topics
- realistic plan
- connection to QA/AQA role
- awareness of weak areas

Follow-up:
- How will you practice English for interviews?

Признаки слабого ответа:
- нет plan
- только vague motivation

---

## Чеклист оценки

Используй после прослушивания записи.

| Область | Максимум | Заметки |
|---|---:|---|
| English clarity | 10 | clear sentences, understandable pronunciation |
| QA theory | 10 | correct core definitions |
| Practical thinking | 10 | asks clarifying questions, explains risk |
| Technical basics | 10 | Web, API, DB, Java basics |
| Automation | 10 | JUnit/TestNG, Selenium, framework, CI |
| Structure | 10 | answers are organized and not too long |

Total: 60 points.

Результат:

- 50-60: strong junior interview
- 40-49: good, but needs cleaner details
- 30-39: basic level, needs practice
- below 30: repeat this interview after reviewing notes

---

## Prompt для проверки транскрипта

Используй этот prompt после transcript записи:

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
