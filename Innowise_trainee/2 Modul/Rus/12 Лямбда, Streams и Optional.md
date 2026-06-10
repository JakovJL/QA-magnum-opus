# Лямбда, Streams и Optional

## Содержание

- [[#Почему функциональный стиль важен]]
- [[#Лямбда-выражения]]
- [[#Функциональные интерфейсы]]
- [[#Method References]]
- [[#Основы Streams]]
- [[#Промежуточные и терминальные операции]]
- [[#Optional]]
- [[#Лямбда Streams и Optional в AQA]]
- [[#Вопросы для собеседования]]

**Связанные заметки:** [[AQA Java rus]]

> [!info] Видео-курс (Заур Трегулов)
> Курс 1 (OCA): L26 лямбда-выражения. Курс 2 (Чёрный Пояс): Lambda и Streams.

---

## Почему функциональный стиль важен

До Java 8 работа с коллекциями часто требовала многословных циклов и временных переменных. Java 8 добавила **лямбда-выражения**, **streams** и **Optional**, чтобы сделать код короче, понятнее и выразительнее.

Эти возможности важны, потому что помогают:

- Описывать **что** нужно сделать, а не только **как**
- Обрабатывать коллекции в виде понятного пайплайна
- Уменьшать boilerplate для простого поведения
- Безопаснее работать с отсутствующими значениями, чем через обычный `null`

В реальных проектах и в AQA эти инструменты часто используются для фильтрации данных, преобразования списков, проверки условий и упрощения утилитного кода.

---

## Лямбда-выражения

**Лямбда-выражение** — это короткий способ передать поведение как данные. Проще говоря, это анонимная функция.

```java
(x, y) -> x + y
name -> name.toUpperCase()
() -> System.out.println("Hello")
```

### Базовый синтаксис

```java
parameters -> expression
parameters -> { statements; }
```

Примеры:

```java
List<String> names = List.of("Ann", "Bob", "Kate");
names.forEach(name -> System.out.println(name)); // печатает каждое имя на своей строке

Comparator<String> byLength = (a, b) -> a.length() - b.length(); // правило сортировки: короче — раньше
```

Лямбды бывают нескольких форм — зависит от количества параметров и тела:

```java
Runnable greet = () -> System.out.println("Hello");        // нет параметров; greet.run() печатает Hello
Consumer<String> print = name -> System.out.println(name); // один параметр; print.accept("Ann")
BinaryOperator<Integer> add = (a, b) -> a + b;             // два параметра; add.apply(2, 3) -> 5
Function<Integer, String> grade = score -> {               // тело из нескольких операторов: нужны { } и return
    if (score >= 60) return "pass";
    return "fail";
};
```

### Почему лямбда полезна

- Убирает boilerplate анонимных классов
- Делает короткие операции проще для чтения
- Отлично работает с коллекциями и streams

### До и после

До лямбды:

```java
Runnable task = new Runnable() {
    @Override
    public void run() {
        System.out.println("Run");
    }
};
```

С лямбдой:

```java
Runnable task = () -> System.out.println("Run");
```

> [!tip] Ключевая идея
> Используй лямбду, когда нужно передать небольшой кусок поведения: правило сортировки, условие фильтра, click handler, retry logic или пользовательскую проверку.

---

## Функциональные интерфейсы

Лямбда работает только с **функциональным интерфейсом**. Это интерфейс, у которого ровно **один абстрактный метод**.

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);
}

Calculator sum = (a, b) -> a + b;
System.out.println(sum.calculate(2, 3)); // 5
```

### Частые встроенные функциональные интерфейсы

| Интерфейс | Метод | Смысл |
|---|---|---|
| `Predicate<T>` | `test(T t)` | проверяет условие |
| `Function<T, R>` | `apply(T t)` | преобразует одно значение в другое |
| `Consumer<T>` | `accept(T t)` | потребляет значение, ничего не возвращает |
| `Supplier<T>` | `get()` | создаёт значение |
| `UnaryOperator<T>` | `apply(T t)` | преобразует значение того же типа |
| `BinaryOperator<T>` | `apply(T a, T b)` | объединяет два значения одного типа |

Примеры:

```java
Predicate<String> isEmpty = text -> text.isEmpty();          // isEmpty.test("") -> true
Function<String, Integer> length = text -> text.length();    // length.apply("abc") -> 3
Consumer<String> printer = text -> System.out.println(text); // printer.accept("hi") печатает hi
Supplier<Double> random = () -> Math.random();               // random.get() -> новое случайное число
```

> [!info] `@FunctionalInterface`
> Эта аннотация не обязательна, но полезна. Она говорит компилятору, что интерфейс должен оставаться функциональным. Если кто-то добавит второй абстрактный метод, код перестанет компилироваться.

---

## Method References

**Method reference** — это более короткая форма лямбды, когда лямбда только вызывает уже существующий метод.

```java
names.forEach(System.out::println);
```

Это эквивалентно:

```java
names.forEach(name -> System.out.println(name));
```

### Частые формы

```java
ClassName::staticMethod    // статический метод класса:        x -> ClassName.staticMethod(x)
objectRef::instanceMethod  // метод конкретного объекта:       x -> objectRef.method(x)
ClassName::instanceMethod  // метод элемента, который придёт:  s -> s.method()
ClassName::new             // конструктор как фабрика:         () -> new ClassName()
```

По одному конкретному примеру на каждую форму:

```java
Function<String, Integer> parse = Integer::parseInt;   // x -> Integer.parseInt(x)
Consumer<String> print = System.out::println;          // x -> System.out.println(x)
Function<String, String> upper = String::toUpperCase;  // s -> s.toUpperCase()
Supplier<ArrayList<String>> make = ArrayList::new;     // () -> new ArrayList<>()
```

Примеры:

```java
List<String> names = List.of("ann", "bob");
names.stream()
     .map(String::toUpperCase)      // "ann" -> "ANN", "bob" -> "BOB"
     .forEach(System.out::println); // печатает ANN, затем BOB

Supplier<List<String>> listFactory = ArrayList::new; // listFactory.get() создаёт новый пустой список
```

Method reference улучшает читаемость, когда операция уже и так понятна из имени метода.

---

## Основы Streams

**Stream** — это не структура данных. Это пайплайн для обработки данных из источника, например списка, множества или массива.

```java
List<String> names = List.of("Ann", "Bob", "Kate", "Alex");

List<String> result = names.stream()
    .filter(name -> name.length() > 3) // оставить имена длиннее 3 -> [Kate, Alex]
    .map(String::toUpperCase)          // перевести каждое в верхний регистр -> [KATE, ALEX]
    .toList();                         // собрать в List

System.out.println(result); // [KATE, ALEX]
```

### Stream pipeline

Обычно stream pipeline состоит из трёх частей:

1. source
2. intermediate operations
3. terminal operation

```java
names.stream()                    // source
     .filter(n -> n.length() > 3) // intermediate
     .map(String::toUpperCase)    // intermediate
     .toList();                   // terminal
```

### Важные правила

- Streams не изменяют исходную коллекцию
- Streams обычно используются один раз
- Lazy evaluation означает, что промежуточные операции запускаются только при старте терминальной операции

> [!warning] Stream одноразовый
> После терминальной операции вроде `toList()` или `count()` stream закрывается. Повторно использовать тот же объект stream нельзя.

---

## Промежуточные и терминальные операции

### Промежуточные операции

Они возвращают новый stream и строят пайплайн.

Частые примеры:

- `filter(predicate)` — оставляет только элементы, подходящие под условие
- `map(function)` — преобразует каждый элемент в другое значение
- `sorted()` — сортирует элементы (естественный порядок или по `Comparator`)
- `distinct()` — убирает дубликаты
- `limit(n)` — оставляет только первые `n` элементов
- `skip(n)` — пропускает первые `n` элементов

```java
List<Integer> numbers = List.of(1, 2, 2, 3, 4, 5);

List<Integer> result = numbers.stream()
    .filter(n -> n > 2) // оставить > 2 -> [3, 4, 5] (две двойки отброшены)
    .distinct()         // убрать дубликаты -> [3, 4, 5]
    .sorted()           // отсортировать по возрастанию -> [3, 4, 5]
    .toList();          // собрать в List
// result = [3, 4, 5]
```

### Терминальные операции

Они завершают пайплайн и дают итоговый результат.

Частые примеры:

- `toList()` — собирает stream в List
- `collect(...)` — гибкий сбор (в List/Set/Map, склейка в строку...)
- `forEach(action)` — выполняет действие для каждого элемента (side effect)
- `count()` — возвращает количество элементов (`long`)
- `findFirst()` — возвращает первый элемент как `Optional`
- `anyMatch(predicate)` — `true`, если хотя бы один элемент подходит
- `allMatch(predicate)` — `true`, если подходят все элементы
- `reduce(...)` — сворачивает все элементы в одно значение (сумма, max, склейка...)

```java
long count = numbers.stream()
    .filter(n -> n % 2 == 0) // оставить чётные -> [2, 2, 4]
    .count();                // сколько осталось -> 3

boolean hasAdmin = List.of("user", "admin", "guest").stream()
    .anyMatch(role -> role.equals("admin")); // есть ли "admin"? -> true
```

### `map()` vs `filter()`

- `filter()` оставляет или убирает элементы
- `map()` преобразует каждый элемент

> [!tip] Быстрое сравнение
> | Операция | Что делает |
> |---|---|
> | `filter()` | оставляет только подходящие элементы |
> | `map()` | преобразует каждый элемент |
> | `distinct()` | убирает дубликаты |
> | `sorted()` | сортирует элементы |
> | `count()` | возвращает количество элементов |

---

## Optional

`Optional<T>` — это контейнер, который может содержать значение, а может быть пустым. Он используется для более явного представления "значение может отсутствовать", чем обычный `null`.

```java
Optional<String> name = Optional.of("Alice");            // содержит значение; null здесь бросил бы исключение
Optional<String> empty = Optional.empty();               // явно пустой, без значения
Optional<String> maybe = Optional.ofNullable(getName()); // пустой, если getName() вернёт null
```

### Частые методы

```java
Optional<String> maybeName = Optional.ofNullable("Ann");

maybeName.ifPresent(System.out::println);                   // выполнится только если значение есть -> печатает Ann
System.out.println(maybeName.orElse("Unknown"));            // значение, или "Unknown" если пусто -> Ann
System.out.println(maybeName.orElseGet(() -> "Generated")); // как orElse, но значение вычисляется только если пусто
```

Полезные методы:

- `isPresent()` — `true`, если значение есть
- `ifPresent(action)` — выполняет действие, только если значение есть
- `orElse(other)` — возвращает значение, или `other` если пусто
- `orElseGet(supplier)` — как `orElse`, но запасное значение вычисляется только если пусто
- `orElseThrow(...)` — возвращает значение, или бросает исключение если пусто
- `map(function)` — преобразует значение, если оно есть
- `filter(predicate)` — оставляет значение, только если оно подходит

### Почему это полезно

- Делает отсутствие значения явным
- Заставляет разработчика подумать о случае отсутствия
- Может уменьшить количество `NullPointerException` в сервисном или утилитном коде

### Чего лучше не делать

```java
Optional<String> name = Optional.of("Bob");
System.out.println(name.get()); // работает, но стиль рискованный (бросит исключение, если пусто)
```

Прямой вызов `get()` часто считается плохой практикой, потому что при пустом значении он бросает `NoSuchElementException`.

> [!caution] Optional нужен не везде
> `Optional` хорошо подходит для возвращаемых значений методов. Обычно его не рекомендуют использовать для полей сущностей, DTO и параметров методов.

---

## Лямбда Streams и Optional в AQA

Эти инструменты очень полезны в автотестировании.

### Типичные примеры

```java
List<WebElement> rows = driver.findElements(By.cssSelector(".row"));

List<String> visibleTexts = rows.stream()
    .map(WebElement::getText)        // каждая строка -> её текст
    .filter(text -> !text.isBlank()) // убрать пустые / пробельные тексты
    .toList();                       // собрать оставшиеся тексты в List
```

```java
boolean hasError = visibleTexts.stream()
    .anyMatch(text -> text.contains("Error")); // true, если хоть один текст содержит "Error"
```

```java
Optional<String> token = Optional.ofNullable(System.getenv("API_TOKEN"));         // пусто, если переменная окружения не задана
String actualToken = token.orElseThrow(() -> new IllegalStateException("API token is missing")); // значение, или исключение если пусто
```

### Где это используется

- Фильтрация списков элементов
- Преобразование API-данных
- Поиск одного подходящего значения
- Проверка условий через `anyMatch()` и `allMatch()`
- Безопасная работа с optional-конфигами

### Частые ошибки

- Строить слишком длинные stream-цепочки, которые ухудшают читаемость
- Использовать streams для кода с side effects
- Вызывать `Optional.get()` без проверки
- Использовать stream там, где обычный цикл проще

> [!info] Хорошее правило для AQA
> Если операция выглядит как "filter -> transform -> collect", stream обычно подходит хорошо. Если логика сложная и с состоянием, обычный цикл может быть понятнее.

---

## Вопросы для собеседования

### Топ-10

**1. Что такое лямбда-выражение в Java?**  
Лямбда-выражение — это короткий способ представить анонимную функцию. Оно позволяет передавать поведение как данные.

**2. Что такое функциональный интерфейс?**  
Это интерфейс с ровно одним абстрактным методом. Именно к нему можно привязать лямбду.

**3. В чём разница между `map()` и `filter()` в streams?**  
`filter()` оставляет только элементы, подходящие под условие. `map()` преобразует каждый элемент в другое значение.

**4. Изменяет ли stream исходную коллекцию?**  
Нет. Stream обрабатывает данные и обычно возвращает новый результат, не меняя исходную коллекцию.

**5. В чём разница между промежуточными и терминальными операциями?**  
Промежуточные операции возвращают новый stream и строят пайплайн. Терминальные операции завершают пайплайн и дают результат или side effect.

**6. Для чего нужен `Optional`?**  
Он используется для представления значения, которое может быть как доступным, так и отсутствующим, как более безопасная альтернатива обычному `null`.

**7. В чём разница между `orElse()` и `orElseGet()`?**  
`orElse()` всегда вычисляет свой аргумент. `orElseGet()` вызывает supplier только если значение отсутствует.

**8. Можно ли переиспользовать stream?**  
Нет. После терминальной операции stream закрывается и не может использоваться повторно.

**9. Что такое method reference?**  
Это более короткая форма лямбды, когда лямбда только вызывает уже существующий метод, например `System.out::println`.

**10. Когда использовать stream, а когда цикл?**  
Stream хорош для понятных пайплайнов обработки данных вроде фильтрации и преобразования. Цикл лучше, когда логика сложная, с состоянием или так проще читать.

---

### Хитрые вопросы

**1. Является ли `Optional` заменой каждому `null` в Java?**  
Нет. Он в основном полезен для возвращаемых значений. Использовать его везде подряд часто делает код менее читаемым.

**2. Почему `forEach()` не всегда лучший выбор для бизнес-логики?**  
Потому что он в основном предназначен для side effects. Для преобразований и фильтрации обычно понятнее использовать `map()` и `filter()`.

**3. Легко ли бросать checked-исключения внутри лямбды?**  
Не всегда. Многие функциональные интерфейсы не объявляют checked-исключения, поэтому часто нужен wrapping или специальная обработка.

**4. Что такое lazy evaluation в streams?**  
Промежуточные операции не выполняются сразу. Они запускаются только когда начинается терминальная операция.

**5. Почему parallel streams могут быть опасны?**  
Они могут создавать проблемы с thread-safety, лишние накладные расходы и непредсказуемую производительность, если операция не является stateless и безопасной для параллельного выполнения.
