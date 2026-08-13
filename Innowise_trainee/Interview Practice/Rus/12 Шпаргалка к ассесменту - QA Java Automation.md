# Шпаргалка к ассесменту — QA, Java и Automation

## Содержание

- [[#Как пользоваться шпаргалкой]]
- [[#Manual QA]]
	- [[#QA vs QC]]
	- [[#STLC]]
	- [[#Пирамида тестирования]]
	- [[#Functional Non-functional и Regression testing]]
	- [[#Test Strategy и Test Plan]]
	- [[#Чеклист и Test Case]]
	- [[#Баг-хантинг и Bug Report]]
	- [[#Техники тест-дизайна]]
	- [[#Работа с требованиями]]
- [[#Java Core]]
	- [[#Примитивные и ссылочные типы]]
	- [[#Логические операторы]]
	- [[#Ветвления и циклы]]
	- [[#Массивы String StringBuilder и StringBuffer]]
	- [[#Классы объекты и наследование]]
	- [[#Параметры Overloading и Overriding]]
	- [[#Контракт equals и hashCode]]
	- [[#Инкапсуляция static и final]]
	- [[#Абстрактный класс и интерфейс]]
	- [[#Исключения]]
	- [[#Collections]]
	- [[#Lambda и Stream API]]
	- [[#Generics]]
	- [[#Serialization и Deserialization]]
	- [[#Singleton]]
- [[#Automation и инструменты]]
	- [[#IDE Terminal Git и Merge Request]]
	- [[#UI Automation]]
	- [[#API Automation]]
	- [[#CI/CD]]
- [[#Быстрые сравнения]]
- [[#Маршрут по статьям базы]]
	- [[#Статьи по Manual QA]]
	- [[#Статьи по Java]]
	- [[#Статьи по Automation]]
- [[#Последний прогон перед ассесментом]]

**Связанные заметки:** [[QA manual rus]], [[AQA Java rus]]

---

## Как пользоваться шпаргалкой

Не заучивай текст дословно. Для каждого вопроса держи в голове четыре части ответа:

1. **Definition** — что это такое.
2. **Difference** — чем отличается от похожего понятия.
3. **Example** — короткий пример из продукта или кода.
4. **Risk** — что сломается при неправильном применении.

Сначала ответь одним предложением. Затем добавь 2–3 ключевых пункта и пример. Если тебя остановили, это нормально: основную мысль ты уже успел сказать.

> [!danger] Главное правило
> Не придумывай рабочий опыт. Если знаешь тему только в теории или применял её в учебном проекте, так и скажи: «В учебном проекте я делал…» или «На реальном проекте я бы начал с…».

---

## Manual QA

### QA vs QC

**Короткий ответ:** Quality Assurance (QA) работает с процессами и помогает **предотвращать** дефекты. Quality Control (QC) работает с продуктом и помогает **обнаруживать** дефекты в уже созданном результате. Testing обычно рассматривается как часть QC, а QC — как часть более широкого QA-подхода.

- **QA:** standards, process audit, Definition of Done, review требований, улучшение процесса.
- **QC:** проверка сборки, review результата, выполнение тестов, анализ дефектов.
- **Фокус QA:** «Как организовать работу, чтобы ошибок было меньше?»
- **Фокус QC:** «Соответствует ли созданный продукт требованиям?»

**Пример:** QA вводит обязательный review требований и code review. QC выполняет тест-кейсы для готовой функции и регистрирует найденные дефекты.

**Не путай:** QA не равно Testing. Testing — только одна из активностей контроля качества.

---

### STLC

**Короткий ответ:** Software Testing Life Cycle (STLC) — это структурированный жизненный цикл тестирования от анализа требований до завершения и отчётности. Он является частью SDLC и в Agile может повторяться в каждом спринте.

Основные этапы:

1. **Requirements Analysis** — понять требования, риски и testability; задать вопросы.
2. **Test Planning** — определить scope, подход, роли, сроки, инструменты, риски, entry и exit criteria.
3. **Test Design** — подготовить test conditions, test cases, checklists и test data.
4. **Environment Setup** — подготовить сборку, окружение, доступы и данные.
5. **Test Execution** — выполнить проверки, сравнить expected и actual results, зарегистрировать дефекты, провести Retest и Regression testing.
6. **Test Reporting и Closure** — подвести итоги, описать покрытие, дефекты и оставшиеся риски, сохранить testware и lessons learned.

Названия и границы этапов могут отличаться. Например, Test Reporting иногда выделяют в отдельную фазу, а подготовку окружения включают в Test Design. Важно объяснить сами активности и их результат.

**Типичный уточняющий вопрос:** что такое entry и exit criteria?

- **Entry criteria** — условия, при которых тестирование можно начинать: доступна сборка, окружение готово, требования согласованы.
- **Exit criteria** — условия завершения: критические проверки выполнены, нет блокирующих дефектов, известные риски приняты.

---

### Пирамида тестирования

**Короткий ответ:** пирамида тестирования показывает рекомендуемое распределение автоматизированных тестов: много быстрых Unit tests, меньше Integration или API tests и мало дорогих UI/E2E tests.

```text
        UI / E2E        мало, медленно, дорого
      Integration/API   умеренное количество
          Unit          много, быстро, дёшево
```

- **Unit tests** проверяют отдельный метод или класс в изоляции.
- **Integration/API tests** проверяют взаимодействие компонентов, сервисов, базы данных и contracts.
- **UI/E2E tests** проверяют критические пользовательские потоки через собранную систему.

**Главная идея:** если риск можно надёжно проверить на более низком уровне, лучше сделать это там. Низкие тесты быстрее, стабильнее и проще локализуют ошибку. UI tests оставляют для критичных сценариев и интеграции всего продукта.

**Не путай:** пирамида — ориентир, а не закон. Для микросервисов может быть шире Integration layer, потому что основной риск находится в contracts между сервисами.

---

### Functional Non-functional и Regression testing

**Короткий ответ:** Functional testing проверяет, **что** система делает согласно требованиям. Non-functional testing проверяет, **как хорошо** она это делает. Regression testing проверяет, что изменения не сломали ранее работавшую функциональность.

- **Functional testing:** логин, расчёт скидки, создание заказа, права ролей.
- **Non-functional testing:** Performance testing, Security testing, usability, accessibility, reliability, compatibility.
- **Regression testing:** повторный набор проверок после новой функции, исправления или изменения конфигурации.
- **Retest:** повторение конкретной проверки, которая ранее упала, чтобы подтвердить исправление дефекта.

> [!tip] Regression testing vs Retest
> Retest отвечает: «Исправили ли конкретный баг?» Regression testing отвечает: «Не сломало ли изменение что-то ещё?» После исправления обычно нужны оба.

Ещё два частых термина:

- **Smoke testing** — широкая, но поверхностная проверка жизнеспособности сборки и основных функций.
- **Sanity testing** — узкая проверка конкретной изменённой области после небольшого изменения.

---

### Test Strategy и Test Plan

**Короткий ответ:** Test Strategy задаёт общий и долгосрочный подход к тестированию — **как тестируем в целом**. Test Plan описывает конкретное тестирование релиза или спринта — **что, когда, кем и при каких критериях тестируем сейчас**.

**Test Strategy обычно содержит:**

- test levels и test types;
- automation approach;
- environments, tools и test data approach;
- defect management;
- metrics и reporting;
- общие risks и responsibilities.

**Test Plan обычно содержит:**

- scope и out of scope;
- features to test и not to test;
- schedule, resources и roles;
- entry и exit criteria;
- environment и deliverables;
- риски конкретного релиза и mitigation.

**Пример:** Strategy говорит, что критичные API scenarios автоматизируются, а UI suite содержит только основные E2E flows. Plan для релиза 2.4 уточняет, какие endpoints и UI flows входят в scope, кто их тестирует и когда можно дать Go/No-Go.

---

### Чеклист и Test Case

**Короткий ответ:** Checklist — краткий список того, что проверить, без подробных шагов. Test Case — воспроизводимый сценарий с preconditions, test data, steps и expected result.

**Test Case обычно содержит:** ID, title, priority, preconditions, test data, steps, expected result, actual result, status и postconditions.

**Когда выбирать:**

- Checklist — опытная команда, быстрые Smoke или exploratory проверки, часто меняющийся продукт.
- Test Case — сложный flow, Regression testing, передача проверки другому человеку, аудит или строгая traceability.

**Компромисс:** Checklist быстрее создавать и поддерживать, но результат сильнее зависит от опыта исполнителя. Test Case дольше писать, зато он лучше воспроизводится.

---

### Баг-хантинг и Bug Report

**Короткий ответ:** баг-хантинг — это не случайные клики, а последовательный поиск отклонения, его воспроизведение, локализация условий и передача доказательств команде.

Практический порядок:

1. Сверить actual result с требованием, acceptance criteria или согласованным ожидаемым поведением.
2. Повторить проблему и определить частоту воспроизведения.
3. Минимизировать steps и test data: убрать всё, без чего баг всё ещё возникает.
4. Сравнить условия: browser, OS, роль, данные, окружение, версия сборки, сеть.
5. Локализовать слой через DevTools, Network, Console, API response, logs и при необходимости database.
6. Проверить duplicates и собрать evidence.
7. Создать Bug Report без догадок о причине в коде.

QA не обязан указать строку кода с root cause. Хорошая локализация сужает область: например, UI не отправляет request; API возвращает неправильный status; response правильный, но UI неверно его показывает.

**Bug Report должен содержать:**

- конкретный title по формуле «что, где, при каком условии»;
- environment и build version;
- preconditions и test data;
- минимальные steps to reproduce;
- expected и actual result;
- Severity и предложенный Priority или business context;
- screenshots, video, logs, request/response или trace ID.

**Severity** показывает технический impact. **Priority** показывает бизнес-срочность. Они независимы: опечатка на главной странице может иметь низкую Severity, но высокий Priority.

---

### Техники тест-дизайна

**Короткий ответ:** техники тест-дизайна помогают системно выбрать небольшой, но достаточный набор проверок вместо перебора всех данных.

- **Equivalence Partitioning (EP):** разделить данные на классы, внутри которых система должна вести себя одинаково, и взять представителя каждого класса.
- **Boundary Value Analysis (BVA):** проверить границу, значение чуть ниже и чуть выше. Обычно применяется после EP.
- **Decision Table:** покрыть комбинации условий и действий; подходит для скидок, ролей и сложных business rules.
- **State Transition Testing:** проверить состояния, события, допустимые и недопустимые переходы; подходит для блокировки аккаунта или статусов заказа.
- **Pairwise:** покрыть все пары значений параметров при большом числе комбинаций.
- **Error Guessing:** использовать опыт и прошлые дефекты: `null`, пустой ввод, ноль, спецсимволы, timeout, повторный клик.
- **Checklist-based testing:** выполнять проверки по подготовленному командному списку рисков и типичных проблем.

**Пример EP + BVA:** поле принимает возраст от 18 до 60 включительно.

- Классы: меньше 18 — invalid; 18–60 — valid; больше 60 — invalid.
- Репрезентативные значения EP: `10`, `30`, `70`.
- Границы BVA: `17`, `18`, `19`, `59`, `60`, `61`.

**Ловушка:** Pairwise не заменяет BVA и проверки критичных business rules. Он сокращает combinations, но может пропустить дефект взаимодействия трёх и более факторов.

---

### Работа с требованиями

**Короткий ответ:** QA анализирует требования до разработки, чтобы найти неоднозначность, неполноту, противоречия, нетестируемость и пропущенные сценарии. Чем раньше найден дефект требования, тем дешевле его исправить.

Хорошее требование должно быть:

- корректным и понятным;
- однозначным;
- полным и непротиворечивым;
- измеримым и тестируемым;
- реализуемым;
- атомарным;
- приоритизированным и traceable.

Вопросы QA при review:

- Как выглядит happy path?
- Какие negative и boundary cases?
- Что происходит при timeout, повторном запросе или частичном сбое?
- Какие роли и permissions?
- Какие состояния и переходы?
- Какие требования к performance, security и accessibility?
- Какие acceptance criteria и как измерить результат?
- Что входит и не входит в scope?

Если требования конфликтуют, QA фиксирует конкретное противоречие, приводит пример и запрашивает решение у Product Owner, Business Analyst или другого владельца требования. Нельзя молча выбрать удобную трактовку.

---

## Java Core

### Примитивные и ссылочные типы

**Короткий ответ:** примитивная переменная хранит само значение. Ссылочная переменная хранит ссылку на объект и может иметь значение `null`.

В Java восемь примитивов:

| Группа | Типы |
|---|---|
| Целые | `byte`, `short`, `int`, `long` |
| Дробные | `float`, `double` |
| Символ | `char` |
| Логический | `boolean` |

Ссылочные типы: `String`, arrays, classes, interfaces, enums, records и collections.

```java
int a = 5;
int b = a;
b = 10;                 // a всё ещё 5

int[] first = {1, 2};
int[] second = first;
second[0] = 99;         // first[0] тоже 99
```

**Важно:** Java всегда передаёт параметры **по значению**. Для объекта копируется значение ссылки. Метод может изменить объект через эту копию, но не может заменить ссылку вызывающего кода своей новой ссылкой.

**Ловушки:**

- collections хранят objects, поэтому для примитивов используются wrappers: `Integer`, `Double`, `Boolean`;
- autoboxing и unboxing могут скрыть `NullPointerException`;
- `==` для references сравнивает identity, а не логическое содержимое.

---

### Логические операторы

| Оператор | Значение | Особенность |
|---|---|---|
| `&&` | AND | short-circuit: правая часть не вычисляется, если слева `false` |
| `||` | OR | short-circuit: правая часть не вычисляется, если слева `true` |
| `!` | NOT | инвертирует boolean |
| `^` | XOR | `true`, если операнды различаются |
| `&` / `|` | AND / OR | для boolean вычисляют обе части; также работают как bitwise operators |

```java
if (user != null && user.isActive()) {
    // isActive() не вызовется, если user == null
}
```

**Ловушка:** порядок условий важен. Сначала ставь безопасную дешёвую проверку, затем обращение к объекту или дорогую операцию.

---

### Ветвления и циклы

**Ветвления:**

- `if / else if / else` — условия и диапазоны;
- `switch` — выбор по одному значению; современный `switch` может возвращать результат;
- ternary operator `condition ? value1 : value2` — короткий выбор одного из двух значений.

**Циклы:**

- `for` — известно число итераций или нужен index;
- enhanced `for` — нужен каждый элемент, но не index;
- `while` — число итераций заранее неизвестно, условие проверяется до тела;
- `do-while` — тело должно выполниться хотя бы один раз;
- `break` завершает цикл, `continue` пропускает текущую итерацию.

**Частые ошибки:** off-by-one (`<` вместо `<=`), бесконечный цикл, изменение collection во время enhanced `for`, неверное условие выхода.

---

### Массивы String StringBuilder и StringBuffer

**Array:** фиксированный размер, элементы одного типа, быстрый доступ по index за O(1). Размер доступен через поле `length`.

**String:** immutable class. Любая «модификация» возвращает новый объект. String literals переиспользуются через String Pool. Содержимое сравнивают через `equals()`, а не через `==`.

**StringBuilder:** mutable, быстрый для многократной конкатенации, не thread-safe.

**StringBuffer:** mutable и synchronized, поэтому thread-safe для отдельных операций, но обычно медленнее.

```java
StringBuilder result = new StringBuilder();
for (String item : items) {
    result.append(item).append(',');
}
String text = result.toString();
```

**Выбор:** обычный текст — `String`; построение строки в цикле — `StringBuilder`; общий изменяемый buffer между потоками — только тогда рассматривать `StringBuffer`.

---

### Классы объекты и наследование

**Короткий ответ:** class — шаблон состояния и поведения, object — созданный instance этого class. Class содержит fields, constructors и methods.

Четыре принципа OOP:

- **Encapsulation** — скрыть внутреннее состояние и управлять доступом.
- **Inheritance** — переиспользовать и расширять поведение родителя.
- **Polymorphism** — работать через общий тип, получая разное runtime-поведение.
- **Abstraction** — показывать важный contract и скрывать детали реализации.

Java поддерживает одиночное наследование classes через `extends`, но class может реализовать несколько interfaces через `implements`. Constructors не наследуются; constructor родителя вызывается через `super(...)` первой инструкцией.

```java
Animal animal = new Dog();
animal.makeSound();       // вызывается реализация Dog
```

**Ловушка:** наследование описывает отношение «is-a». Для простого повторного использования поведения часто безопаснее composition — отношение «has-a».

---

### Параметры Overloading и Overriding

**Параметризация методов:** параметры задают вход метода, return type — результат. Java поддерживает varargs: `method(String... values)`, но внутри это array.

> [!danger] Java передаёт всё по значению
> Для примитива копируется значение. Для объекта копируется ссылка. Поэтому метод может изменить состояние переданного mutable object, но присваивание параметру нового объекта не заменит переменную вызывающего кода.

| Критерий | Overloading | Overriding |
|---|---|---|
| Где | Обычно в одном class | В subclass относительно parent |
| Сигнатура | То же имя, разные параметры | То же имя и параметры |
| Выбор | Compile time | Runtime |
| Цель | Несколько способов вызвать операцию | Изменить унаследованное поведение |

Правила:

- одного изменения return type недостаточно для Overloading;
- при Overriding access нельзя сделать уже;
- covariant return type разрешён;
- static methods скрываются, а не переопределяются;
- `final` method нельзя override;
- `@Override` помогает компилятору проверить намерение.

---

### Контракт equals и hashCode

**Короткий ответ:** `equals()` определяет логическое равенство objects. `hashCode()` определяет hash value, по которому `HashMap` и `HashSet` выбирают bucket. Если переопределён `equals()`, обычно обязательно переопределяют и `hashCode()` по тем же значимым fields.

Контракт `equals()`:

- reflexive: `x.equals(x)`;
- symmetric: если `x.equals(y)`, то `y.equals(x)`;
- transitive;
- consistent, пока значимые данные не меняются;
- `x.equals(null)` возвращает `false`.

Контракт с `hashCode()`:

- равные objects **обязаны** иметь одинаковый hash code;
- одинаковый hash code не означает равенство — collisions допустимы;
- неравные objects могут иметь одинаковый hash code, но это ухудшает performance.

**Ловушка:** не изменяй fields, участвующие в `equals()` и `hashCode()`, пока object используется как key в `HashMap` или element в `HashSet`. Иначе collection может перестать его находить.

---

### Инкапсуляция static и final

**Encapsulation** — скрытие внутреннего состояния и предоставление контролируемого API. Поля обычно делают `private`, а изменения выполняют через methods, которые сохраняют invariants.

| Модификатор | Доступ |
|---|---|
| `public` | отовсюду |
| `protected` | внутри package и в subclasses |
| package-private | только внутри package |
| `private` | только внутри class |

**`static`:** член принадлежит class, а не отдельному object. Static field общий для всех instances; static method нельзя напрямую использовать `this` и instance fields.

**`final`:**

- final variable можно присвоить один раз;
- final reference нельзя перенаправить, но mutable object по этой ссылке всё ещё можно изменить;
- final method нельзя override;
- final class нельзя наследовать.

Константы обычно объявляют `public static final`, если действительно нужен public contract.

---

### Абстрактный класс и интерфейс

**Короткий ответ:** abstract class удобен для общей базы со state, constructors и частичной реализацией. Interface задаёт contract или capability и позволяет class реализовать несколько contracts.

| Возможность | Abstract class | Interface |
|---|---|---|
| Instance fields | Да | Нет; поля являются `public static final` |
| Constructor | Да | Нет |
| Методы с реализацией | Да | `default`, `static`, private helper methods |
| Наследование | Только один parent class | Несколько interfaces |
| Основная идея | Общее состояние и базовое поведение | Contract и слабая связанность |

**Пример AQA:** `WebDriver` — interface, а `ChromeDriver` и `FirefoxDriver` — implementations. Test может работать через тип `WebDriver` и менять browser без изменения test logic.

**Выбор:** если объекты разделяют состояние и реализацию — abstract class. Если разные classes должны обещать одинаковое поведение — interface.

---

### Исключения

Иерархия:

```text
Throwable
├── Error
└── Exception
    └── RuntimeException
```

- **Error** — серьёзная проблема JVM; обычно application code её не обрабатывает.
- **Checked exception** — compiler требует `catch` или `throws`, например `IOException`.
- **Unchecked exception** — потомок `RuntimeException`; compiler не требует обработки, например `NullPointerException`.

**`try`** содержит опасный код. **`catch`** обрабатывает конкретную проблему. **`finally`** почти всегда выполняется и подходит для cleanup, но для `AutoCloseable` лучше try-with-resources.

```java
try (BufferedReader reader = Files.newBufferedReader(path)) {
    return reader.readLine();
} catch (IOException e) {
    throw new UncheckedIOException("Cannot read test data", e);
}
```

- `throw` — выбросить один exception object внутри метода.
- `throws` — объявить возможные exceptions в method signature.
- try-with-resources автоматически закрывает resources, реализующие `AutoCloseable`, даже при exception.

**Ловушки:** не используй пустой `catch`, не лови `Exception` без причины и не превращай test failure в успешный test. Лови exception, только если можешь восстановиться или добавить полезный context.

---

### Collections

**Короткий ответ:** Java Collections Framework предоставляет interfaces и implementations для групп objects. `Map` входит во framework, но не наследует `Collection`.

| Interface | Свойства | Частые implementations |
|---|---|---|
| `List` | порядок и index, duplicates разрешены | `ArrayList`, `LinkedList` |
| `Set` | unique elements | `HashSet`, `LinkedHashSet`, `TreeSet` |
| `Queue` / `Deque` | обработка FIFO или с двух концов | `ArrayDeque`, `PriorityQueue` |
| `Map` | key-value, keys unique | `HashMap`, `LinkedHashMap`, `TreeMap` |

Быстрый выбор:

- `ArrayList` — List по умолчанию, быстрый `get(index)`.
- `HashSet` — uniqueness и быстрый `contains()` без гарантии порядка.
- `HashMap` — быстрый поиск value по key без гарантии порядка.
- `LinkedHashSet` / `LinkedHashMap` — сохраняют insertion order.
- `TreeSet` / `TreeMap` — sorted order, обычно O(log n).
- `ArrayDeque` — предпочтительный stack или queue общего назначения.

**Важно:** hash collections зависят от правильного `equals()` и `hashCode()`. Collections хранят objects, поэтому примитивы проходят через boxing.

---

### Lambda и Stream API

**Lambda expression** — короткая реализация functional interface, у которого один abstract method.

```java
Predicate<String> isLong = value -> value.length() > 5;
```

Частые functional interfaces: `Predicate`, `Function`, `Consumer`, `Supplier`.

**Stream** — не data structure, а одноразовый pipeline обработки данных:

```java
List<String> activeNames = users.stream()
    .filter(User::isActive)
    .map(User::getName)
    .distinct()
    .sorted()
    .toList();
```

- intermediate operations (`filter`, `map`, `sorted`) lazy и возвращают Stream;
- terminal operations (`toList`, `count`, `findFirst`, `reduce`) запускают pipeline;
- Stream не изменяет source collection сам по себе;
- один Stream нельзя использовать повторно после terminal operation.

**`filter` vs `map`:** `filter` решает, оставить ли element; `map` преобразует element в другое value.

---

### Generics

**Короткий ответ:** Generics дают compile-time type safety и позволяют писать reusable classes и methods без ручных casts.

```java
List<String> names = new ArrayList<>();
names.add("Ann");
// names.add(10); // compile-time error
```

Ключевые понятия:

- `<T>` — type parameter;
- `<T extends Number>` — upper bound;
- `List<?>` — list неизвестного типа;
- `? extends T` — producer, безопасно читать как `T`;
- `? super T` — consumer, безопасно добавлять `T`;
- **PECS:** Producer Extends, Consumer Super.

Generics реализованы через **type erasure**: большая часть информации о type parameter стирается после compilation. Поэтому нельзя создать `new T()`, `new T[]` или проверить `instanceof List<String>` обычным способом.

**Ловушка:** `List<Integer>` не является subtype `List<Number>`. Generics в Java invariant.

---

### Serialization и Deserialization

**Короткий ответ:** Serialization преобразует object в формат для передачи или хранения. Deserialization выполняет обратное преобразование из bytes, JSON или XML в object.

В API automation обычно используются DTO или records и mapper Jackson/Gson:

```java
public record CreateUserRequest(String name, String role) {}
public record UserResponse(long id, String name, String role) {}

CreateUserRequest request = new CreateUserRequest("Ann", "QA");

UserResponse response = given()
    .contentType(ContentType.JSON)
    .body(request)                         // Java object -> JSON
.when()
    .post("/users")
.then()
    .statusCode(201)
    .extract().as(UserResponse.class);     // JSON -> Java object
```

Fields, types и mapping DTO должны соответствовать contract response. Неизвестные поля, даты, enums и `null` требуют явной настройки mapper, если defaults не подходят.

**Не путай:** Java native serialization через `Serializable` и `ObjectOutputStream` — отдельный механизм. Для JSON mapping interface `Serializable` не требуется. Нельзя безопасно десериализовать недоверенные native Java streams без строгого контроля.

---

### Singleton

**Короткий ответ:** Singleton — Creational pattern, который ограничивает class одним instance и предоставляет общую точку доступа к нему.

Простой eager-вариант:

```java
public final class Config {
    private static final Config INSTANCE = new Config();

    private Config() {}

    public static Config getInstance() {
        return INSTANCE;
    }
}
```

Для простого надёжного Singleton в Java также можно использовать enum:

```java
public enum Config {
    INSTANCE
}
```

**Что важно на собеседовании:**

- constructor закрыт через `private`;
- instance хранится в static field;
- lazy initialization должна быть thread-safe;
- enum защищает от обычных проблем reflection и serialization лучше классического варианта.

**Риск в AQA:** Singleton с mutable state создаёт зависимость между tests, мешает parallel execution и усложняет cleanup. Не делай один глобальный WebDriver на все параллельные tests; lifecycle лучше контролировать через fixtures, dependency injection или thread-local strategy.

---

## Automation и инструменты

### IDE Terminal Git и Merge Request

**Подготовка проекта в IDE:** установить JDK, проверить `JAVA_HOME`, выбрать Project SDK, импортировать Maven или Gradle project, дождаться загрузки dependencies и запустить один test из IDE и Terminal.

Полезные проверки:

```bash
java -version
mvn -version
git --version
git status
```

Базовый Git flow:

```bash
git clone <repository-url>
git switch -c feature/login-tests
git status
git add <files>
git commit -m "Add login tests"
git push -u origin feature/login-tests
```

После push создаётся **Merge Request** в GitLab или **Pull Request** в GitHub. Команда смотрит diff, оставляет review comments, CI запускает checks, автор вносит исправления, после approvals и зелёного pipeline изменения merge в target branch.

Частые различия:

- `git fetch` загружает remote history, но не меняет working branch;
- `git pull` обычно выполняет fetch и затем merge или rebase;
- merge сохраняет ветвление истории;
- rebase переносит commits на новую базу и переписывает их hashes;
- merge conflict решается вручную, затем изменения отмечаются как resolved и операция продолжается.

**Ловушки:** не коммить secrets и generated reports; перед commit и push проверяй `git status` и `git diff`; не переписывай shared history без договорённости команды.

---

### UI Automation

**Локаторы:** выбирай самый простой, уникальный и стабильный selector.

Предпочтение обычно такое:

1. согласованный `data-testid` или стабильный unique `id`;
2. устойчивый `name`, `role` или другой semantic attribute;
3. короткий CSS selector;
4. XPath, если нужен текст, axes или движение по DOM;
5. длинный absolute XPath и positional indexes — последний вариант.

```java
By css = By.cssSelector("button[data-testid='login']");
By xpath = By.xpath("//button[@data-testid='login']");
```

CSS обычно короче. XPath умеет искать по text и двигаться к parent или sibling. Важнее не тип locator, а его stability и uniqueness.

**Ожидания:**

- Implicit wait — общий timeout для поиска elements.
- Explicit wait — ожидание конкретного condition в конкретном месте.
- Fluent wait — настраиваемые timeout, polling interval и ignored exceptions.
- `Thread.sleep()` всегда ждёт фиксированное время и обычно делает suite медленнее и нестабильнее.

```java
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
WebElement save = wait.until(
    ExpectedConditions.elementToBeClickable(By.id("save"))
);
save.click();
```

Обычно предпочтительны explicit waits. Не смешивай большие implicit и explicit waits без причины: итоговое время ожидания становится сложнее предсказать.

**Page Object Model:** page или component представлен class, который скрывает locators и технические UI actions. Test описывает scenario и assertions.

Хороший Page Object:

- предоставляет business-readable actions, например `loginAs(user)`;
- хранит locators в одном месте;
- не раскрывает WebDriver без необходимости;
- не содержит assertions полного business scenario;
- может проверить, что нужная page действительно открылась.

**Частые падения:** `NoSuchElementException`, `TimeoutException`, `StaleElementReferenceException`, `ElementNotInteractableException`. Не лечи их слепыми retries — сначала выясни condition и lifecycle element.

---

### API Automation

**Короткий ответ:** API test формирует HTTP request, отправляет его, проверяет transport contract и важный business result. Он быстрее и стабильнее UI test, потому что работает ниже UI layer.

Request состоит из:

- HTTP method и URL;
- path и query parameters;
- headers, cookies и authentication;
- content type и body.

Response проверяют по:

- status code;
- headers и content type;
- response body, schema и field types;
- business rules;
- side effects в другом API или database, если это часть scenario.

Основные методы:

- `GET` — получить resource;
- `POST` — создать resource или запустить operation;
- `PUT` — полностью заменить resource;
- `PATCH` — частично изменить resource;
- `DELETE` — удалить resource.

`GET`, `PUT` и `DELETE` по HTTP semantics должны быть idempotent; повторение одинакового request не должно менять итоговый state после первого успешного выполнения. `POST` обычно не idempotent, а поведение `PATCH` зависит от contract.

REST Assured часто использует структуру Given–When–Then:

```java
given()
    .baseUri(baseUrl)
    .auth().oauth2(token)
    .contentType(ContentType.JSON)
    .body(request)
.when()
    .post("/users")
.then()
    .statusCode(201)
    .body("name", equalTo(request.name()));
```

Serialization и Deserialization позволяют использовать typed DTO вместо ручных JSON strings. Это улучшает refactoring и compile-time checks, но DTO всё равно нужно сверять с API contract.

**Ловушки:** проверять только status code; хранить token в code; логировать secrets; зависеть от общих test data; путать path parameter с query parameter; делать API test зависимым от результата предыдущего test.

---

### CI/CD

**Короткий ответ:** Continuous Integration (CI) автоматически собирает и проверяет частые изменения кода. Continuous Delivery держит успешный build готовым к ручному release. Continuous Deployment автоматически выпускает каждое изменение, прошедшее quality gates.

Типичный pipeline:

1. checkout source code;
2. setup JDK и dependencies;
3. static checks и compilation;
4. Unit tests;
5. Integration и API tests;
6. build artifact;
7. deploy в test environment;
8. Smoke и выбранные UI/E2E tests;
9. публикация reports, logs, screenshots и других artifacts.

**Почему порядок важен:** быстрые и дешёвые проверки должны падать раньше дорогих UI tests. Это ускоряет feedback и упрощает localization.

**Quality gate:** failed critical tests могут блокировать merge или deployment. Flaky test нельзя бесконечно перезапускать до зелёного результата; его причину исправляют или временно помещают в quarantine с owner и сроком.

Secrets передаются через CI secret storage или environment variables. Reports и screenshots нужно сохранять даже при failed tests, иначе расследовать падение будет трудно.

---

## Быстрые сравнения

| Пара | Главное различие |
|---|---|
| QA vs QC | QA предотвращает дефекты через процессы; QC находит дефекты в продукте |
| Test Strategy vs Test Plan | общий долгосрочный подход vs конкретный релиз, scope и schedule |
| Checklist vs Test Case | список проверок vs воспроизводимые steps и expected result |
| Regression testing vs Retest | влияние изменения на систему vs проверка конкретного исправления |
| Primitive vs Reference | само значение vs ссылка на object |
| `==` vs `equals()` | значение примитива или identity reference vs логическое равенство objects |
| Overloading vs Overriding | разные параметры и compile time vs та же сигнатура и runtime polymorphism |
| Abstract class vs Interface | state и общая реализация vs contract и несколько implementations |
| `throw` vs `throws` | выброс exception внутри method vs объявление в signature |
| StringBuilder vs StringBuffer | быстрее и не thread-safe vs synchronized и обычно медленнее |
| `List` vs `Set` | порядок и duplicates vs uniqueness |
| `filter` vs `map` | отбор elements vs преобразование elements |
| Implicit vs Explicit wait | общий timeout поиска vs конкретное condition в конкретном месте |
| Serialization vs Deserialization | object в формат vs формат в object |
| Merge Request vs Merge | запрос на review и слияние vs сама операция объединения history |

---

## Маршрут по статьям базы

Открывай полную статью только по теме, на которую не можешь уверенно ответить вслух за 30–60 секунд.

### Статьи по Manual QA

1. [[00 Основы тестирования#QA и QC]] — QA, QC и Testing.
2. [[02 SDLC и STLC#Этапы STLC]] — этапы процесса тестирования.
3. [[04 Пирамида тестирования]] — Unit, Integration и E2E layers.
4. [[01 Виды тестирования]] — Functional, Non-functional, Regression testing и test levels.
5. [[10 Регрессионное тестирование]] — подробный Regression testing и выбор suite.
6. [[03 Тестовые артефакты]] — Test Strategy, Test Plan, Test Case и Checklist.
7. [[07 Баг-репорт и жизненный цикл дефекта]] — Bug Report, Severity, Priority и statuses.
8. [[06 Техники тест-дизайна]] — EP, BVA, Decision Table, State Transition и Pairwise.
9. [[08 Работа с требованиями]] — review, testability и defects требований.

---

### Статьи по Java

1. [[01 Переменные типы данных операторы]] — primitives, references, casting и operators.
2. [[02 Управляющие конструкции]] — conditions и loops.
3. [[03 Основы ООП]] — classes, objects, static, access modifiers, `equals()` и `hashCode()`.
4. [[04 ООП Наследование и полиморфизм]] — inheritance, Overloading, Overriding и `final`.
5. [[05 ООП Интерфейсы и абстракция]] — abstract class vs interface.
6. [[07 Строки подробно]] — String Pool, immutability, StringBuilder и StringBuffer.
7. [[10 Массивы и коллекции]] — arrays и полный Java Collections Framework.
8. [[11 Обработка исключений]] — checked/unchecked, try-with-resources, `throw` и `throws`.
9. [[12 Лямбда, Streams и Optional]] — Lambda expressions и Stream API.
10. [[09 Дженерики]] — bounds, wildcards, PECS и type erasure.
11. [[14 Файловый ввод-вывод]] — resources, streams и работа с файлами.
12. [[26 REST API тестирование с REST Assured#Сериализация и десериализация]] — JSON Serialization и Deserialization.
13. [[24 Паттерны проектирования для AQA#Creational patterns]] — контекст Singleton и других Creational patterns.

---

### Статьи по Automation

1. [[00 Основы Java]] — JDK, JVM и запуск Java application.
2. [[15 Сборочные инструменты Maven Gradle]] — project setup и dependencies.
3. [[18 Git и контроль версий]] — branches, commits, remote и Pull/Merge Request.
4. [[17 Основы Selenium WebDriver]] — Selenium API, locators, elements и waits.
5. [[10 Locators]] — подробные вопросы по CSS и XPath.
6. [[14 Waiters]] — Implicit, Explicit и Fluent waits.
7. [[20 Архитектура фреймворка автотестов]] — layers и Page Object Model.
8. [[09 Основы тестирования API]] — HTTP, request и response basics.
9. [[26 REST API тестирование с REST Assured]] — практическая API automation.
10. [[19 CI-CD с GitHub Actions]] — pipeline и запуск tests в CI.

---

## Последний прогон перед ассесментом

1. Пройди раздел [[#Быстрые сравнения]] без подсказок.
2. Ответь вслух на каждый `###` вопрос: сначала 30 секунд, затем до 60 секунд с примером.
3. Напиши без подсказки три маленьких примера: Overloading/Overriding, Stream pipeline и explicit wait.
4. Открой статьи из [[#Маршрут по статьям базы]] только для слабых ответов.
5. Проведи мини-интервью: Manual QA → Java → UI/API → CI/CD.
6. Если не знаешь деталь, скажи честно, что знаешь основной принцип, и объясни, как проверил бы ответ или исследовал проблему.

> [!tip] Перед сном
> Не пытайся перечитать все статьи целиком. Цель последнего вечера — быстро и ясно воспроизводить уже изученные понятия, а не добавить десятки новых деталей.
