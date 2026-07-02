# CI/CD с GitHub Actions

## Содержание

- [[#Что такое CI/CD]]
- [[#Основы pipeline]]
- [[#GitHub Actions]]
- [[#Пример pipeline для AQA]]
- [[#Частые проблемы]]
- [[#Вопросы для собеседования]]
	- [[#Вопросы начального уровня]]
	- [[#Вопросы среднего уровня]]
	- [[#Вопросы повышенного уровня]]
	- [[#Вопросы с кодом]]

**Связанные заметки:** [[AQA Java rus]]

---

## Что такое CI/CD

**CI (Continuous Integration)** означает, что код часто вливается и автоматически проверяется.

**CD (Continuous Delivery или Deployment)** означает, что успешная сборка может быть подготовлена к релизу или выкачена автоматически.

**Цель:** находить проблемы рано и делать сборки повторяемыми.

---

## Основы pipeline

**Pipeline** — это упорядоченный набор автоматических шагов.

Типичные шаги:

1. Checkout code
2. Настроить Java
3. Скачать dependencies
4. Скомпилировать проект
5. Запустить тесты
6. Опубликовать reports

Распространённые CI/CD tools:

- GitHub Actions
- GitLab CI
- Jenkins
- TeamCity
- Azure Pipelines

---

## GitHub Actions

GitHub Actions использует workflow-файлы в `.github/workflows`.

Базовый workflow:

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

Важные термины:

| Термин | Значение |
|---|---|
| workflow | весь файл автоматизации |
| job | группа steps на одном runner |
| step | одна команда или action |
| runner | машина, которая выполняет job |
| artifact | файл, сохранённый после запуска, например report |

---

## Пример pipeline для AQA

В AQA-проекте pipeline часто запускает:

- unit tests
- API tests
- UI smoke tests
- static checks
- генерацию Allure report

Хорошая практика:

- запускать быстрые проверки на каждый pull request
- запускать медленную regression по расписанию или вручную
- делать test data независимыми
- сохранять screenshots, logs и reports как artifacts

---

## Частые проблемы

| Проблема | Типичная причина |
|---|---|
| Тест проходит локально, но падает в CI | другой browser, OS, timing или test data |
| Pipeline слишком медленный | слишком много UI tests или нет parallelization |
| Report отсутствует | нет шага upload artifact |
| Красные builds игнорируют | flaky tests не исправляются |

> [!caution] CI должен вызывать доверие
> Flaky pipeline быстро теряет ценность. Исправляй нестабильные тесты или помещай их в quarantine с понятной причиной.

---

## Вопросы для собеседования

### Вопросы начального уровня

**1. Что такое CI?**
CI — это автоматическая проверка изменений кода после push или pull request.

**2. Что такое pipeline?**
Pipeline — это набор автоматических шагов, например build, test и report.

### Вопросы среднего уровня

**3. Почему тесты нужно запускать в CI?**
CI ловит проблемы рано и быстро даёт команде feedback.

**4. Что AQA pipeline должен сохранять как artifacts?**
Reports, screenshots, logs и иногда файлы test results.

### Вопросы повышенного уровня

**5. Почему тест может падать только в CI?**
CI environment может отличаться версией browser, размером экрана, timing, данными или parallel execution.

### Вопросы с кодом

**6. Что делает этот шаг workflow?**

```yaml
- run: mvn test
```

Он запускает Maven tests на CI runner.
