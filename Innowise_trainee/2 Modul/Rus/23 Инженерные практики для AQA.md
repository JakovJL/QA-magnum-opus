# Инженерные практики для AQA

## Содержание

- [[#Почему инженерные практики важны]]
- [[#Clean code для тестов]]
- [[#Code review и стандарты команды]]
- [[#Надёжность тестов]]
- [[#Практики поддержки]]
- [[#Вопросы для собеседования]]
	- [[#Вопросы начального уровня]]
	- [[#Вопросы среднего уровня]]
	- [[#Вопросы повышенного уровня]]
	- [[#Вопросы с кодом]]

**Связанные заметки:** [[AQA Java rus]]

---

## Почему инженерные практики важны

AQA code — это production code для тестирования. Если он messy, команда теряет доверие к automation.

**Цель:** сделать test code понятным для чтения, изменения, review и debugging.

Важные практики:

- readable names
- small methods
- clear structure
- code review
- stable tests
- simple design

---

## Clean code для тестов

Хороший test code показывает intent.

```java
loginPage.loginAs(admin);
dashboard.shouldShowUserName(admin.name());
```

Плохой test code прячет intent в технических деталях.

```java
driver.findElement(By.id("u")).sendKeys("admin");
driver.findElement(By.id("p")).sendKeys("123");
```

Хорошие правила:

- один test проверяет одно главное behavior
- test names описывают scenario
- helpers простые и переиспользуемые
- assertions имеют понятные messages
- duplicated setup выносится аккуратно

---

## Code review и стандарты команды

Code review улучшает качество тестов до того, как изменения попадут в main branch.

Reviewers проверяют:

- readability
- надёжные waits
- отсутствие hardcoded secrets
- хорошую работу с test data
- полезные assertions
- отсутствие лишней duplication

Team standards могут включать naming rules, package structure, branch rules и report requirements.

---

## Надёжность тестов

Надёжный test падает только тогда, когда есть реальная проблема.

Частые причины ненадёжных tests:

- fixed sleeps вместо waits
- shared test data
- order dependency
- environment dependency
- слабые locators
- tests, зависящие от local state

> [!danger] Главное правило
> Flaky test — это проблема проекта, а не нормальное состояние. Исправь его, помести в quarantine или удали.

---

## Практики поддержки

Поддерживаемые AQA projects:

- отделяют framework code от test scenarios
- переиспользуют page objects и API clients
- держат configuration вне test logic
- обновляют dependencies осознанно
- документируют неочевидные решения
- удаляют dead tests и unused helpers

Простой design обычно лучше сложного framework, который команда не понимает.

---

## Вопросы для собеседования

### Вопросы начального уровня

**1. Почему test code должен быть clean?**
Clean test code проще читать, поддерживать и debug.

**2. Что такое code review?**
Code review — это проверка изменений перед merge.

### Вопросы среднего уровня

**3. Что делает test flaky?**
Timing issues, shared data, weak locators, order dependency или environment differences.

**4. Почему fixed sleeps плохи?**
Они замедляют tests и всё равно не гарантируют, что page готова.

### Вопросы повышенного уровня

**5. Как AQA-инженер может снизить стоимость поддержки?**
Использовать clear structure, reusable helpers, stable locators, configuration и small focused tests.

### Вопросы с кодом

**6. Какая здесь проблема?**

```java
Thread.sleep(5000);
driver.findElement(By.id("save")).click();
```

Fixed sleep ненадёжен и медленный. Лучше использовать explicit wait для save button.
