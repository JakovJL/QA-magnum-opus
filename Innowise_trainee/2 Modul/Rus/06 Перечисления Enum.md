# Перечисления Enum

## Содержание

- [[#Зачем нужны Enum]]
- [[#Базовый Enum]]
- [[#Enum с полями и методами]]
- [[#Enum в switch]]
- [[#Служебные методы Enum]]
- [[#Enum на практике]]
- [[#Вопросы для собеседования]]
	- [[#Вопросы начального уровня]]
	- [[#Вопросы среднего уровня]]
	- [[#Вопросы повышенного уровня]]
	- [[#Вопросы с кодом]]

**Связанные заметки:** [[AQA Java rus]]

> [!info] Видео-курс (Заур Трегулов)
> Курс 2 (Чёрный Пояс): раздел «Другое» — Enum.

---

## Зачем нужны Enum

Представь, что у тебя есть фиксированный набор значений — типы браузеров, статусы тестов, дни недели. Можно использовать строковые константы:

```java
public static final String CHROME = "CHROME";
public static final String FIREFOX = "FIREFOX";
```

Но у этого подхода есть проблемы:
- Ничто не мешает передать `"CRHOME"` (опечатка) — код скомпилируется
- Нет способа перечислить все допустимые значения
- Нет типобезопасности — любая `String` подойдёт туда, где ожидается имя браузера

**Enum** — это специальный класс, который представляет фиксированный набор констант. Компилятор гарантирует, что можно использовать только объявленные значения.

```java
public enum Browser {
    CHROME, FIREFOX, EDGE, SAFARI
}

Browser b = Browser.CHROME;   // допустимы только эти 4 значения
// Browser b = Browser.OPERA; // ошибка компиляции — OPERA не объявлен
```

> [!danger] Ключевая идея
> Enum = тип с замкнутым набором значений. Если все возможные варианты известны на этапе компиляции — используй enum.

---

## Базовый Enum

```java
public enum Season {
    SPRING, SUMMER, AUTUMN, WINTER
}
```

Под капотом каждая константа — это `public static final` экземпляр класса `Season`. JVM создаёт ровно 4 объекта `Season` — по одному на каждую константу — и больше создать нельзя.

```java
Season current = Season.SUMMER;
System.out.println(current);          // SUMMER
System.out.println(current.name());   // "SUMMER" (String)
System.out.println(current.ordinal()); // 1 (позиция, начиная с 0)
```

> [!info] Enum — это класс
> Каждый enum неявно наследуется от `java.lang.Enum`. Это значит, что он не может наследовать другой класс. Но enum может реализовывать интерфейсы.

---

## Enum с полями и методами

Enum — это не просто метки. Каждая константа может хранить данные и иметь поведение. Именно это делает enum в Java мощнее, чем enum в других языках.

**Зачем это нужно?** Допустим, у каждого браузера есть имя драйвера и таймаут по умолчанию. Вместо хранения этих данных в отдельном конфиге или map, ты хранишь их прямо в enum — данные и константа живут вместе.

```java
public enum Browser {
    CHROME("chromedriver", 10),
    FIREFOX("geckodriver", 15),
    EDGE("msedgedriver", 10);

    private final String driverName;
    private final int defaultTimeout;

    // Конструктор всегда private (даже без ключевого слова)
    Browser(String driverName, int defaultTimeout) {
        this.driverName = driverName;
        this.defaultTimeout = defaultTimeout;
    }

    public String getDriverName() { return driverName; }
    public int getDefaultTimeout() { return defaultTimeout; }

    public String getDriverPath() {
        return "drivers/" + driverName;
    }
}
```

```java
Browser browser = Browser.CHROME;
System.out.println(browser.getDriverName());     // chromedriver
System.out.println(browser.getDefaultTimeout()); // 10
System.out.println(browser.getDriverPath());     // drivers/chromedriver
```

> [!caution] Конструктор Enum всегда private
> Нельзя написать `new Browser(...)`. Единственные экземпляры — те, что объявлены как константы. Именно так работает гарантия "фиксированный набор".

---

## Enum в switch

Enum естественно работает со `switch` — и компилятор предупредит, если пропущен вариант.

```java
public String getSeasonMessage(Season season) {
    switch (season) {
        case SPRING: return "Время сажать";
        case SUMMER: return "Время отдыхать";
        case AUTUMN: return "Время собирать урожай";
        case WINTER: return "Время кодить";
        default: return "Неизвестно";
    }
}
```

С улучшенным switch (Java 14+):

```java
String message = switch (season) {
    case SPRING -> "Время сажать";
    case SUMMER -> "Время отдыхать";
    case AUTUMN -> "Время собирать урожай";
    case WINTER -> "Время кодить";
};
```

> [!tip] default не нужен
> Если перечислены все значения enum в улучшенном switch, компилятор знает, что switch исчерпывающий — ветка `default` не нужна. Если потом добавить новую константу в enum, компилятор предупредит о каждом switch, который её не обрабатывает.

---

## Служебные методы Enum

Каждый enum получает эти методы бесплатно:

| Метод | Что делает | Пример |
|---|---|---|
| `name()` | Возвращает имя константы как String | `Browser.CHROME.name()` -> `"CHROME"` |
| `ordinal()` | Возвращает позицию (с 0) | `Browser.FIREFOX.ordinal()` -> `1` |
| `values()` | Возвращает массив всех констант | `Browser.values()` -> `[CHROME, FIREFOX, EDGE]` |
| `valueOf(String)` | Преобразует String в константу enum | `Browser.valueOf("CHROME")` -> `Browser.CHROME` |

```java
// Перебор всех значений
for (Browser b : Browser.values()) {
    System.out.println(b.name() + " -> " + b.getDriverName());
}

// Парсинг из String (например, из конфиг-файла)
String config = "FIREFOX";
Browser browser = Browser.valueOf(config);   // Browser.FIREFOX
// Browser.valueOf("Opera");                 // IllegalArgumentException!
```

> [!caution] valueOf чувствителен к регистру
> `Browser.valueOf("chrome")` выбросит `IllegalArgumentException`. Строка должна точно совпадать с именем константы. Для регистронезависимого парсинга напиши вспомогательный метод.

---

## Enum на практике

### Отслеживание статуса тестов

```java
public enum TestStatus {
    PASSED("green"),
    FAILED("red"),
    SKIPPED("yellow"),
    BLOCKED("gray");

    private final String color;

    TestStatus(String color) { this.color = color; }
    public String getColor() { return color; }
}
```

### Конфигурация окружений

```java
public enum Environment {
    DEV("https://dev.example.com", "dev_user"),
    STAGING("https://staging.example.com", "stage_user"),
    PROD("https://example.com", "prod_user");

    private final String baseUrl;
    private final String defaultUser;

    Environment(String baseUrl, String defaultUser) {
        this.baseUrl = baseUrl;
        this.defaultUser = defaultUser;
    }

    public String getBaseUrl() { return baseUrl; }
    public String getDefaultUser() { return defaultUser; }
}

// Использование в тестах
Environment env = Environment.valueOf(System.getProperty("env", "DEV"));
driver.get(env.getBaseUrl());
```

### Enum с реализацией интерфейса

```java
public interface Describable {
    String describe();
}

public enum Priority implements Describable {
    LOW {
        @Override
        public String describe() { return "Может подождать"; }
    },
    MEDIUM {
        @Override
        public String describe() { return "Нужно исправить скоро"; }
    },
    HIGH {
        @Override
        public String describe() { return "Исправить сейчас"; }
    };
}
```

> [!tip] Когда использовать Enum в AQA
> - Типы браузеров (`CHROME`, `FIREFOX`, `EDGE`)
> - Тестовые окружения (`DEV`, `STAGING`, `PROD`)
> - Статусы тестов (`PASSED`, `FAILED`, `SKIPPED`)
> - Роли пользователей (`ADMIN`, `USER`, `GUEST`)
> - Любое значение из конфига с известным фиксированным набором вариантов

---

## Вопросы для собеседования

### Вопросы начального уровня

**Что такое enum в Java?**
Enum — это специальный класс, представляющий фиксированный набор констант. Каждая константа — `public static final` экземпляр класса enum. Enum обеспечивает типобезопасность — компилятор гарантирует, что используются только объявленные значения.

**Может ли enum иметь поля, методы и конструкторы?**
Да. Enum может содержать private-поля, public-методы и конструкторы. Конструктор всегда private (неявно). Каждая константа может передавать аргументы конструктору и хранить свои данные.

**Что возвращает `values()`?**
Новый массив, содержащий все константы enum в порядке объявления. Часто используется для итерации или валидации.

**Можно ли использовать enum в switch?**
Да, и это одно из основных применений. Компилятор проверяет, что обработаны все варианты (в улучшенном switch), что предотвращает баги при добавлении новых констант.

**Может ли enum наследовать класс?**
Нет. Каждый enum уже неявно наследуется от `java.lang.Enum`. Java не допускает множественное наследование, поэтому enum не может наследовать другой класс.

### Вопросы среднего уровня

**В чём разница между `name()` и `toString()` у enum?**
`name()` всегда возвращает точное имя константы как объявлено (например, `"CHROME"`). `toString()` возвращает то же самое по умолчанию, но может быть переопределён для возврата человекочитаемой строки. `valueOf()` опирается на `name()`, а не на `toString()`.

**Как безопасно преобразовать String в enum?**
`Browser.valueOf("CHROME")` работает, если строка точно совпадает. При неизвестном значении выбрасывает `IllegalArgumentException`. Для безопасного преобразования оберни в try-catch или напиши метод, итерирующий по `values()`.

**Может ли enum реализовывать интерфейс?**
Да. Enum может реализовывать любое количество интерфейсов. Каждая константа может предоставить свою реализацию. Это полезно для паттернов типа Strategy.

**Зачем использовать enum вместо String- или int-констант?**
Типобезопасность (компилятор ловит невалидные значения), отсутствие опечаток, автодополнение в IDE, возможность привязать данные и поведение, `values()` для перечисления всех вариантов.

### Вопросы повышенного уровня

**Что происходит при сравнении enum через `==`?**
Работает корректно. Поскольку каждая константа — singleton-экземпляр, `==` сравнивает идентичность, что совпадает с логическим равенством. Более того, `==` предпочтительнее `.equals()` для enum, потому что он null-безопасен: `null == Browser.CHROME` вернёт `false`, а `null.equals(Browser.CHROME)` выбросит `NullPointerException`.

**Можно ли создать новые экземпляры enum во время выполнения?**
Нет. Набор экземпляров фиксирован на этапе компиляции. Нельзя использовать `new`, reflection или десериализацию для создания новых экземпляров. Это делает enum inherently singleton для каждого значения.

**Может ли enum иметь абстрактный метод?**
Да. Если enum объявляет абстрактный метод, каждая константа обязана его реализовать. Так можно дать каждой константе уникальное поведение без интерфейса.

**Почему конструктор enum всегда private и почему enum фактически singleton для каждой константы?**
JVM создаёт ровно один экземпляр на каждую объявленную константу при загрузке класса enum, а private-конструктор не позволяет никому (включая сам enum после загрузки) создать дополнительные экземпляры. Именно это обеспечивает гарантию «фиксированного замкнутого набора» — добавить новое значение во время выполнения невозможно.

### Вопросы с кодом

```java
public enum Season {
    SPRING, SUMMER, AUTUMN, WINTER
}

Season current = Season.SUMMER;
System.out.println(current);
System.out.println(current.name());
System.out.println(current.ordinal());
```
**Что выведет этот код?**
**Ответ:**
```
SUMMER
SUMMER
1
```
`toString()` по умолчанию возвращает имя константы, `name()` возвращает точное объявленное имя, а `ordinal()` — позицию с нуля (`SPRING`=0, `SUMMER`=1).

---

```java
String config = "chrome";
Browser b = Browser.valueOf(config);
System.out.println(b.getDriverName());
```
**Скомпилируется ли это и выполнится? Если нет — какое исключение выбрасывается и как исправить?**
**Ответ:** Выбрасывается `IllegalArgumentException`. `valueOf()` чувствителен к регистру и требует точного имени константы `"CHROME"`, а не `"chrome"`. Исправление: передать точное имя либо написать вспомогательный метод, который итерирует по `Browser.values()` и сравнивает без учёта регистра.

---

```java
public enum Browser {
    CHROME("chromedriver", 10),
    FIREFOX("geckodriver", 15),
    EDGE("msedgedriver", 10);

    private final String driverName;
    private final int defaultTimeout;

    Browser(String driverName, int defaultTimeout) {
        this.driverName = driverName;
        this.defaultTimeout = defaultTimeout;
    }
    public String getDriverName() { return driverName; }
    public int getDefaultTimeout() { return defaultTimeout; }
}

Browser b = Browser.FIREFOX;
System.out.println(b.getDriverName());
System.out.println(b.getDefaultTimeout());
```
**Что выведет код?**
**Ответ:**
```
geckodriver
15
```
Каждая константа передаёт свои аргументы в private-конструктор, поэтому `FIREFOX` хранит свои `driverName` ("geckodriver") и `defaultTimeout` (15).
