# Основы Selenium WebDriver

## Содержание

- [[#Зачем нужен Selenium WebDriver]]
- [[#Архитектура WebDriver]]
- [[#Selenium API]]
- [[#Настройка проекта]]
- [[#Инициализация браузера и навигация]]
- [[#Локаторы элементов]]
- [[#Работа с элементами]]
- [[#Ожидания Waiters]]
- [[#Типичные ошибки]]
- [[#Selenium в AQA]]
- [[#Вопросы для собеседования]]
	- [[#Вопросы начального уровня]]
	- [[#Вопросы среднего уровня]]
	- [[#Вопросы повышенного уровня]]
	- [[#Вопросы с кодом]]

**Связанные заметки:** [[AQA Java rus]]

> [!caution] Нет в видео-курсе
> Курсы Заура заканчиваются на Java Core — Selenium не разбирается. Это AQA-надстройка: учись по заметке и практике, видео ищи отдельно.

---

## Зачем нужен Selenium WebDriver

Selenium WebDriver — это инструмент для автоматизации браузеров. Он позволяет:

- Открывать веб-страницы
- Взаимодействовать с элементами (клики, ввод текста)
- Проверять состояние страницы
- Автоматизировать тесты

WebDriver поддерживает все основные браузеры: Chrome, Firefox, Edge, Safari.

---

## Архитектура WebDriver

WebDriver работает по модели клиент-сервер:

1. **Клиент** — ваш код на Java
2. **WebDriver API** — команды для браузера
3. **Driver** — специфичная для браузера реализация
4. **Браузер** — выполняет действия

Пример: `ChromeDriver` — это драйвер для браузера Chrome.

---

## Selenium API

Selenium API — это набор classes и methods для управления browser.

Самые важные части:

| Часть API | Назначение |
|---|---|
| `WebDriver` | управляет browser session и navigation |
| `WebElement` | представляет один element на странице |
| `By` | описывает, как найти element |
| `Actions` | выполняет сложные mouse и keyboard actions |
| `WebDriverWait` | ждёт, пока condition станет true |
| `ExpectedConditions` | готовые wait conditions |

**Цель:** понимать, какой object отвечает за какое действие в browser.

---

## Настройка проекта

Минимальные зависимости для Maven:

```xml
<dependency>
    <groupId>org.seleniumhq.selenium</groupId>
    <artifactId>selenium-java</artifactId>
    <version>4.1.2</version>
</dependency>
```

Пример простого теста:

```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class FirstTest {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        driver.get("https://example.com");
        System.out.println(driver.getTitle());
        driver.quit();
    }
}
```

---

## Инициализация браузера и навигация

| Команда | Описание |
|---|---|
| `driver.get(url)` | Открывает страницу |
| `driver.getTitle()` | Возвращает заголовок страницы |
| `driver.getCurrentUrl()` | Возвращает текущий URL |
| `driver.findElement(locator)` | Находит элемент |
| `driver.findElements(locator)` | Находит несколько элементов |
| `driver.quit()` | Закрывает браузер и завершает сессию |
| `driver.close()` | Закрывает текущее окно |

---

## Локаторы элементов

Локаторы нужны, чтобы находить элементы на странице:

| Тип локатора | Пример |
|---|---|
| `By.id` | `By.id("username")` |
| `By.name` | `By.name("email")` |
| `By.className` | `By.className("btn")` |
| `By.tagName` | `By.tagName("input")` |
| `By.linkText` | `By.linkText("Click here")` |
| `By.partialLinkText` | `By.partialLinkText("Click")` |
| `By.cssSelector` | `By.cssSelector("input[type='submit']")` |
| `By.xpath` | `By.xpath("//input[@name='q']")` |

Пример использования:

```java
WebElement element = driver.findElement(By.id("login"));
element.click();
```

---

## Работа с элементами

Основные действия с элементами:

```java
WebElement button = driver.findElement(By.id("submit"));
button.click(); // клик

WebElement input = driver.findElement(By.name("username"));
input.sendKeys("testuser"); // ввод текста

String text = element.getText(); // получение текста
boolean displayed = element.isDisplayed(); // проверка видимости
```

---

## Ожидания Waiters

Многие страницы загружают данные асинхронно. Если Selenium ищет element слишком рано, test падает.

Основные виды ожиданий:

| Вид | Описание |
|---|---|
| Implicit wait | общий timeout для поиска elements |
| Explicit wait | ожидание конкретного condition |
| Fluent wait | explicit wait с настройкой polling и ignored exceptions |

Пример explicit wait:

```java
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
WebElement button = wait.until(ExpectedConditions.elementToBeClickable(By.id("save")));
button.click();
```

> [!caution] Не смешивай waits без причины
> Обычно explicit waits проще контролировать и debug, чем fixed sleeps или неявные ожидания везде.

---

## Типичные ошибки

| Ошибка | Причина |
|---|---|
| `NoSuchElementException` | Элемент не найден на странице |
| `ElementNotInteractableException` | Элемент невидим или недоступен для взаимодействия |
| `TimeoutException` | Действие не выполнено за отведённое время |
| `StaleElementReferenceException` | Элемент устарел (страница обновилась) |

---

## Selenium в AQA

В автотестировании Selenium используется для:

- Тестирования UI
- Проверки функциональности
- Автоматизации регрессионных тестов

Пример теста для страницы логина:

```java
@Test
public void testLogin() {
    driver.get("https://example.com/login");
    driver.findElement(By.id("username")).sendKeys("user");
    driver.findElement(By.id("password")).sendKeys("pass");
    driver.findElement(By.id("submit")).click();
    assertTrue(driver.getCurrentUrl().contains("dashboard"));
}
```

---

## Вопросы для собеседования

### Вопросы начального уровня

**1. Что такое Selenium WebDriver?**
Это инструмент и API для автоматизации действий в браузере.

**2. Что такое `WebDriver` в Selenium?**
Это основной интерфейс для управления браузером.

**3. Что такое локаторы?**
Локаторы — способы находить элементы на странице, например `id`, `name`, `cssSelector` или `xpath`.

**4. Что делает `driver.get()`?**
Открывает указанный URL в браузере.

**5. Что такое `WebElement`?**
Это интерфейс Selenium для элемента страницы.

**6. Какое исключение часто возникает, когда элемент не найден?**
`NoSuchElementException`.

---

### Вопросы среднего уровня

**1. В чём разница между `findElement()` и `findElements()`?**
`findElement()` возвращает один элемент или бросает исключение, если ничего не найдено. `findElements()` возвращает список и даёт пустой список, если ничего не найдено.

**2. В чём разница между `close()` и `quit()`?**
`close()` закрывает текущее окно. `quit()` закрывает всю сессию браузера.

**3. Почему Selenium-тестам нужны waits?**
Потому что веб-элементы могут загружаться или становиться кликабельными асинхронно.

**4. Какой обычно приоритет локаторов?**
Обычно `id`, затем `name`, затем CSS, затем XPath при необходимости.

---

### Вопросы повышенного уровня

**1. Достаточно ли Selenium для всего automation-фреймворка?**
Нет. Selenium отвечает за взаимодействие с браузером, но тестам обычно также нужны тестовый фреймворк, отчёты, конфигурация и структура проекта.

**2. Почему XPath иногда критикуют?**
Потому что сложные XPath-локаторы могут стать трудночитаемыми и хрупкими при изменении структуры страницы. Но XPath всё равно полезен в некоторых случаях.

**3. Почему сохранять `WebElement` слишком рано может быть опасно?**
Потому что DOM может измениться, и позже сохранённая ссылка станет устаревшей (stale).

**4. Хватит ли implicit wait для всех реальных проектов?**
Обычно нет. Explicit waits точнее и лучше подходят для динамического поведения.

**5. Почему setup браузера часто выносят в `@BeforeEach` или базовые классы?**
Чтобы избежать дублирования и сделать тесты чище и проще в поддержке.

---

### Вопросы с кодом

**1. Ни один элемент не подходит под селектор. Что произойдёт с `one` и `many`?**

```java
WebElement one = driver.findElement(By.id("submit"));
List<WebElement> many = driver.findElements(By.cssSelector(".missing"));
```

**Ответ:** `findElement(By.id("submit"))` бросает `NoSuchElementException`, если ни один элемент не подходит. `findElements(By.cssSelector(".missing"))` **не** бросает исключение — если ничего не найдено, он возвращает пустой список.

**2. Что именно ждёт это ожидание и что будет, если условие так и не станет true?**

```java
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
WebElement button = wait.until(
    ExpectedConditions.elementToBeClickable(By.id("submit"))
);
button.click();
```

**Ответ:** Оно ждёт, пока элемент `#submit` не станет одновременно **видимым и активным** (clickable), периодически опрашивая страницу, до 10 секунд. Если условие за это время не выполняется, бросается `TimeoutException`.

**3. Этот код выполняется после обновления страницы. Что пойдёт не так?**

```java
WebElement el = driver.findElement(By.id("name"));
driver.navigate().refresh();
el.getText();
```

**Ответ:** После `refresh()` DOM перестраивается, поэтому старая ссылка `el` больше недействительна. Вызов `el.getText()` бросает `StaleElementReferenceException`. Элемент нужно найти заново после навигации.
