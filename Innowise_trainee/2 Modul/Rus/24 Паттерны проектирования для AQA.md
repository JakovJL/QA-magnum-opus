# Паттерны проектирования для AQA

## Содержание

- [[#Зачем нужны паттерны проектирования]]
- [[#Категории паттернов]]
	- [[#Creational patterns]]
	- [[#Structural patterns]]
	- [[#Behavioral patterns]]
- [[#Factory Pattern]]
	- [[#Simple Factory]]
	- [[#Factory Method]]
- [[#Builder Pattern]]
	- [[#Test Data Builder]]
	- [[#Request Specification Builder]]
	- [[#Reusable Auth Specification]]
- [[#Strategy Pattern]]
	- [[#Authentication Strategy]]
- [[#Singleton и ThreadLocal]]
	- [[#Singleton]]
	- [[#ThreadLocal для параллельных тестов]]
- [[#Facade и Service Object]]
	- [[#Facade]]
	- [[#Service Object и API Object]]
- [[#Decorator и Filter]]
	- [[#Decorator]]
	- [[#Filter и Interceptor]]
- [[#Proxy Pattern]]
- [[#Adapter Pattern]]
- [[#Observer и Listener]]
- [[#Template Method и Backdoor]]
	- [[#Template Method]]
	- [[#Backdoor]]
- [[#Семейство Page Object]]
	- [[#Page Object]]
	- [[#Component Object]]
	- [[#Element Wrapper]]
- [[#Выбор паттерна]]
- [[#Совместное использование паттернов в AQA-фреймворке]]
- [[#Типичные ошибки]]
- [[#Суть темы]]
- [[#Вопросы для собеседования]]
	- [[#Вопросы начального уровня]]
	- [[#Вопросы среднего уровня]]
	- [[#Вопросы повышенного уровня]]
	- [[#Вопросы с кодом]]

**Связанные заметки:** [[AQA Java rus]]

> [!caution] Нет в видео-курсе
> Курсы Заура заканчиваются на Java Core — паттерны проектирования для AQA не разбираются. Это AQA-надстройка: учись по этой заметке и практике и применяй паттерн только тогда, когда он решает реальную проблему проектирования.

---

## Зачем нужны паттерны проектирования

**Паттерн проектирования** — это повторно используемая идея решения распространённой software problem. Он описывает взаимодействие classes и objects, но не является готовым кодом, который нужно копировать без изменений.

**Цель:** упростить расширение, тестирование и поддержку кода с помощью решений, понятных другим разработчикам.

В AQA framework паттерны помогают:

- создавать browser drivers или API clients
- собирать test data с понятными default values
- переиспользовать аутентификацию без дублирования
- изменять behavior во время выполнения
- отделять test scenarios от технического setup
- уменьшать большие блоки `if` и `switch`
- запускать тесты параллельно без общего состояния

Паттерны полезны, когда изменение или дублирование уже существует. Для небольшой проблемы часто достаточно прямого constructor, одного метода или простого condition.

> [!danger] Сначала решай проблему
> Не добавляй паттерн только потому, что он популярен. Паттерн должен устранять реальную проблему, а не добавлять новые classes без пользы.

---

## Категории паттернов

Классические категории показывают, какой вид design problem решает паттерн.

### Creational patterns

**Определение:** Creational patterns управляют созданием objects.

**Цель:** скрыть сложное создание и уменьшить прямую зависимость от concrete classes.

Основные примеры:

- Factory Method
- Abstract Factory
- Builder
- Prototype
- Singleton

В AQA часто применяются Factory, Builder и Singleton для drivers, clients, configuration и test data.

### Structural patterns

**Определение:** Structural patterns объединяют classes и objects в более крупные структуры.

**Цель:** связывать components без сильной зависимости между ними.

Основные примеры:

- Adapter
- Decorator
- Facade
- Proxy
- Composite

В AQA Facade прячет сложный login flow за одним методом, Decorator добавляет логирование или retry к запросу, Adapter соединяет разные tools, а Proxy добавляет ленивую загрузку или контроль доступа.

### Behavioral patterns

**Определение:** Behavioral patterns организуют communication и responsibilities между objects.

**Цель:** сделать behavior заменяемым и убрать decision logic из больших conditions.

Основные примеры:

- Strategy
- Observer
- Command
- Template Method
- Chain of Responsibility

В AQA Strategy выбирает authentication или retry, Observer реагирует на события тестов, а Template Method задаёт общий flow для setup и teardown.

> [!tip] Краткое сравнение
> | Категория | Главный вопрос | Паттерн в этой заметке | Пример в AQA |
> |---|---|---|---|
> | Creational | Как создаётся object? | Factory, Builder, Singleton | Создание driver, test user или config |
> | Structural | Как objects связаны? | Facade, Decorator, Proxy, Adapter | Обёртка API client, добавление auth header, адаптация reporting |
> | Behavioral | Как можно изменить behavior? | Strategy, Observer, Template Method | Выбор способа authentication, реакция на события теста, переиспользование setup |

---

## Factory Pattern

**Определение:** Factory централизует создание objects и возвращает object через общий type.

**Цель:** убрать прямое создание concrete implementations из tests и другого client code.

**Как работает:** caller передаёт type или option. Factory выбирает concrete class, создаёт его и возвращает interface или base type.

**Преимущества:**

- скрывает детали создания
- убирает зависимость tests от concrete implementations
- хранит creation logic в одном месте
- упрощает добавление новых implementations

**Недостатки:**

- добавляет дополнительную abstraction и новые classes
- может превратиться в большой conditional, если отвечает за слишком много задач
- не отменяет необходимость настраивать и закрывать созданные resources

### Simple Factory

Simple Factory — это class или method, который выбирает implementation, часто с помощью `switch`. Его часто называют Factory, но он не является GoF Factory Method pattern.

Пример создания browser:

```java
public enum Browser {
    CHROME,
    FIREFOX,
    EDGE
}
```

```java
public final class BrowserFactory {

    private BrowserFactory() {
    }

    public static WebDriver create(Browser browser) {
        return switch (browser) {
            case CHROME -> new ChromeDriver();
            case FIREFOX -> new FirefoxDriver();
            case EDGE -> new EdgeDriver();
        };
    }
}
```

```java
WebDriver driver = BrowserFactory.create(Browser.CHROME);

try {
    driver.get("https://example.com");
} finally {
    driver.quit();
}
```

Test зависит от `WebDriver`, а не напрямую от одного concrete driver class. Factory выбирает implementation.

**Когда использовать:** используй Simple Factory когда есть 2–4 стабильных варианта и создание — это один `new`.

### Factory Method

Factory Method определяет creation method в base type и позволяет subclasses выбирать concrete product.

```java
public abstract class DriverProvider {

    public abstract WebDriver createDriver();
}
```

```java
public final class ChromeDriverProvider extends DriverProvider {

    @Override
    public WebDriver createDriver() {
        return new ChromeDriver();
    }
}
```

```java
public final class FirefoxDriverProvider extends DriverProvider {

    @Override
    public WebDriver createDriver() {
        return new FirefoxDriver();
    }
}
```

**Когда использовать:** применяй Factory Method, когда создание должно изменяться через inheritance или каждому provider нужны собственные creation steps (options, capabilities, remote URL). Для простого выбора browser обычно достаточно Simple Factory.

> [!tip] Simple Factory и Factory Method
> | Пункт | Simple Factory | Factory Method |
> |---|---|---|
> | Основной механизм | Один method выбирает implementation | Subclasses переопределяют creation method |
> | GoF pattern | Нет | Да |
> | Количество classes | Обычно меньше | Обычно больше |
> | Подходящий пример в AQA | Простой выбор driver или client | Providers с разным setup logic |

---

## Builder Pattern

**Определение:** Builder создаёт сложный object пошагово и возвращает итоговый object через `build()`.

**Цель:** сделать создание object понятным, когда есть много optional values, default values или validation rules.

**Как работает:** Builder хранит временные values. Fluent methods изменяют выбранные values и возвращают тот же builder. `build()` проверяет state и создаёт итоговый object.

**Преимущества:**

- делает test data понятными
- поддерживает полезные default values
- заменяет constructors с большим количеством parameters
- позволяет повторно использовать разные object variations
- может проверять data до создания object

**Недостатки:**

- добавляет лишний код для простых objects
- плохие defaults могут скрывать важные test data
- mutable builder нельзя использовать совместно между parallel tests

### Test Data Builder

Предположим, test нужны данные user:

```java
public record UserData(
        String name,
        String email,
        String role,
        boolean active
) {
}
```

Builder задаёт valid defaults и позволяет каждому test изменять только важные fields:

```java
public final class UserDataBuilder {
    private String name = "Test User";
    private String email = "test.user@example.com";
    private String role = "USER";
    private boolean active = true;

    public UserDataBuilder withName(String name) {
        this.name = name;
        return this;
    }

    public UserDataBuilder withEmail(String email) {
        this.email = email;
        return this;
    }

    public UserDataBuilder withRole(String role) {
        this.role = role;
        return this;
    }

    public UserDataBuilder inactive() {
        this.active = false;
        return this;
    }

    public UserData build() {
        if (email == null || !email.contains("@")) {
            throw new IllegalStateException("A valid email is required");
        }

        return new UserData(name, email, role, active);
    }
}
```

```java
UserData admin = new UserDataBuilder()
    .withName("Ana")
    .withEmail("ana@example.com")
    .withRole("ADMIN")
    .build();
```

Test ясно показывает, чем отличается этот user. Остальные valid fields берутся из defaults.

**Когда использовать:** используй Test Data Builder когда у object много fields, но test важны только 1–2 из них. Не используй для object с двумя required fields — там проще constructor.

### Request Specification Builder

В REST Assured есть реальный пример Builder:

```java
RequestSpecification specification = new RequestSpecBuilder()
    .setBaseUri("https://api.example.com")
    .setBasePath("/v1")
    .setContentType(ContentType.JSON)
    .addHeader("Accept", "application/json")
    .build();
```

Каждый method настраивает одну часть. `build()` возвращает итоговый `RequestSpecification`.

### Reusable Auth Specification

Это тот же Builder, применённый к задаче переиспользования аутентификации. Он решает проблему: «не копировать header `Authorization` в каждый тест».

Плохо — дублирование в каждом тесте:

```java
// Дублируется 50 раз — сложно менять
given().header("Authorization", "Bearer " + token)
    .body(product)
.when().post("/api/products");
```

Хорошо — одна переиспользуемая спецификация, собранная один раз:

```java
public final class ApiSpecs {

    private ApiSpecs() {
    }

    public static RequestSpecification adminSpec() {
        return new RequestSpecBuilder()
            .setBaseUri(Config.getBaseUrl())
            .setContentType(ContentType.JSON)
            .addHeader("Authorization", "Bearer " + TokenManager.getAdminToken())
            .addFilter(new AllureRestAssured())
            .build();
    }

    public static RequestSpecification forRole(Role role) {
        String token = switch (role) {
            case ADMIN -> TokenManager.getAdminToken();
            case USER -> TokenManager.getUserToken();
            case GUEST -> null;
        };
        RequestSpecBuilder builder = new RequestSpecBuilder()
            .setBaseUri(Config.getBaseUrl())
            .setContentType(ContentType.JSON);
        if (token != null) {
            builder.addHeader("Authorization", "Bearer " + token);
        }
        return builder.build();
    }
}
```

Использование — в тесте нет деталей auth:

```java
@Test
void adminCanCreateProduct() {
    given().spec(ApiSpecs.adminSpec())
        .body(new Product("iPhone"))
    .when()
        .post("/api/products")
    .then()
        .statusCode(201);
}

@Test
void guestCannotCreateProduct() {
    given().spec(ApiSpecs.forRole(Role.GUEST))
        .body(new Product("iPhone"))
    .when()
        .post("/api/products")
    .then()
        .statusCode(401);
}
```

**Почему это лучше:** источник токена, имя header и base URL живут в одном месте. Если header изменится с `Bearer` на `Token`, ты меняешь один метод, а не 50 тестов.

> [!tip] Builder и Constructor
> | Пункт | Constructor | Builder |
> |---|---|---|
> | Лучше подходит для | Нескольких required values | Многих optional values или defaults |
> | Читаемость | Значение parameters может быть непонятно | Имена fluent methods объясняют values |
> | Validation | Обычно внутри constructor | Часто внутри `build()` |
> | Дополнительный код | Мало | Больше |

---

## Strategy Pattern

**Определение:** Strategy помещает заменяемый behavior за общий interface.

**Цель:** выбирать или изменять algorithm во время выполнения без изменения class, который его использует.

**Как работает:** context получает strategy object и делегирует ему одну responsibility. Разные strategies реализуют один interface по-разному.

**Преимущества:**

- заменяет большие conditions полиморфизмом
- поддерживает выбор behavior во время выполнения
- хранит каждый algorithm в отдельном class
- упрощает отдельное тестирование behavior

**Недостатки:**

- создаёт больше маленьких classes
- caller должен знать, какую strategy выбрать
- не нужен, если существует только один стабильный behavior

### Authentication Strategy

API client может поддерживать разные способы authentication. Без Strategy внутри client большой `if`:

```java
// Плохо — растёт с каждым новым типом auth
if (authType.equals("bearer")) { ... }
else if (authType.equals("basic")) { ... }
```

Со Strategy каждый способ — отдельный class:

```java
public interface AuthStrategy {
    RequestSpecification apply(RequestSpecification request);
}
```

```java
public final class BearerTokenStrategy implements AuthStrategy {
    private final String token;

    public BearerTokenStrategy(String token) {
        this.token = token;
    }

    @Override
    public RequestSpecification apply(RequestSpecification request) {
        return request.auth().oauth2(token);
    }
}
```

```java
public final class BasicAuthStrategy implements AuthStrategy {
    private final String username;
    private final String password;

    public BasicAuthStrategy(String username, String password) {
        this.username = username;
        this.password = password;
    }

    @Override
    public RequestSpecification apply(RequestSpecification request) {
        return request.auth().preemptive().basic(username, password);
    }
}
```

```java
public final class NoAuthStrategy implements AuthStrategy {
    @Override
    public RequestSpecification apply(RequestSpecification request) {
        return request; // guest — ничего не делаем
    }
}
```

API client делегирует authentication выбранной strategy:

```java
public final class ApiClient {
    private final String baseUri;
    private final AuthStrategy authStrategy;

    public ApiClient(String baseUri, AuthStrategy authStrategy) {
        this.baseUri = baseUri;
        this.authStrategy = authStrategy;
    }

    public Response get(String path) {
        RequestSpecification request = given().baseUri(baseUri);
        return authStrategy.apply(request)
            .when()
            .get(path);
    }
}
```

```java
AuthStrategy auth = new BearerTokenStrategy(token);
ApiClient client = new ApiClient("https://api.example.com", auth);

Response response = client.get("/profile");
```

В `ApiClient` не нужен `if (authType == ...)`. Новый authentication method можно добавить как новую strategy без изменения client.

**Когда использовать:** используй Strategy когда есть 3 и более behaviors которые могут расти (типы auth, retry, waiting). Для 1–2 стабильных случаев проще маленький `if`.

> [!tip] Strategy и Conditional
> | Пункт | Conditional | Strategy |
> |---|---|---|
> | Лучше подходит для | Одного небольшого стабильного выбора | Нескольких behaviors, которые изменяются или растут |
> | Расширение | Изменить существующий conditional | Добавить новую implementation |
> | Тестирование | Branches проверяются вместе | Каждая strategy проверяется отдельно |
> | Стоимость | Очень низкая | Больше classes и interfaces |

---

## Singleton и ThreadLocal

### Singleton

**Определение:** Singleton гарантирует что существует только один экземпляр class и даёт глобальный доступ к нему.

**Цель:** разделить один дорогой или stateful ресурс без многократного создания.

**Как работает:** class имеет private constructor, private static field для instance и public static method который создаёт instance один раз и потом возвращает тот же instance. В AQA instance часто ленивый и thread-safe.

**Преимущества:**

- одно место управляет созданием и lifecycle
- избегает многократного создания одного дорогого object (config, token, driver factory)
- простой глобальный доступ

**Недостатки:**

- глобальное состояние сложно сбросить между tests
- скрывает зависимости — test использует singleton не объявляя его
- небезопасен для параллельных тестов если состояние mutable и общее — все threads видят один object

Пример — configuration которую нужно прочитать один раз:

```java
public final class Config {
    private static Config instance;
    private final Properties properties;

    private Config() {
        properties = new Properties();
        try (InputStream in = Files.newInputStream(Path.of("config.properties"))) {
            properties.load(in);
        } catch (IOException e) {
            throw new IllegalStateException("Cannot load config", e);
        }
    }

    public static synchronized Config getInstance() {
        if (instance == null) {
            instance = new Config();
        }
        return instance;
    }

    public String getBaseUrl() {
        return properties.getProperty("baseUrl");
    }

    // Для тестов — сброс между suites если нужно
    static synchronized void reset() {
        instance = null;
    }
}
```

Более полезный пример в AQA — TokenManager с кешем и expiration:

```java
public final class TokenManager {
    private static String adminToken;
    private static Instant expiresAt;
    private static final Duration SAFETY_MARGIN = Duration.ofSeconds(60);

    private TokenManager() {
    }

    public static synchronized String getAdminToken() {
        if (adminToken == null || Instant.now().isAfter(expiresAt)) {
            AuthResponse response = AuthService.loginAsAdminWithResponse();
            adminToken = response.accessToken();
            expiresAt = Instant.now().plusSeconds(response.expiresIn())
                .minus(SAFETY_MARGIN);
        }
        return adminToken;
    }

    public static synchronized void invalidate() {
        adminToken = null;
        expiresAt = null;
    }
}
```

Первый test делает реальный login. Следующие 49 тестов переиспользуют кешированный token пока он не истечёт. Это экономит время и снижает нагрузку на auth service.

**Когда использовать:** используй Singleton для действительно глобальных, дорогих или read-only ресурсов: configuration, кеш token, read-only test data. Не используй для mutable состояния на уровне теста вроде `WebDriver` или per-test токенов при параллельном запуске.

### ThreadLocal для параллельных тестов

**Определение:** ThreadLocal даёт каждому thread свою копию переменной. Threads не видят значения друг друга.

**Цель:** сделать параллельные тесты изолированными, сохранив простой API (`get()` / `set()`).

**Как работает:** ты хранишь значение в `ThreadLocal` field. Каждый thread вызывает `get()` и получает своё значение. Framework устанавливает значение в начале теста и удаляет в конце.

Пример — driver который безопасен для параллельных тестов:

```java
public final class DriverManager {
    private static final ThreadLocal<WebDriver> DRIVER = new ThreadLocal<>();

    private DriverManager() {
    }

    public static void set(WebDriver driver) {
        DRIVER.set(driver);
    }

    public static WebDriver get() {
        WebDriver driver = DRIVER.get();
        if (driver == null) {
            throw new IllegalStateException("Driver not set for current thread. Call set() in @BeforeEach.");
        }
        return driver;
    }

    public static void remove() {
        WebDriver driver = DRIVER.get();
        if (driver != null) {
            driver.quit();
        }
        DRIVER.remove();
    }
}
```

Использование с JUnit 5:

```java
class CheckoutTest {

    @BeforeEach
    void setUp() {
        WebDriver driver = BrowserFactory.create(Browser.CHROME);
        DriverManager.set(driver);
    }

    @AfterEach
    void tearDown() {
        DriverManager.remove(); // закрывает и очищает ThreadLocal
    }

    @Test
    void guestCanAddToCart() {
        // Любая страница может вызвать DriverManager.get() и получит driver ЭТОГО thread
        new HomePage(DriverManager.get()).open();
    }
}
```

Та же идея для токенов когда tests идут параллельно с разными users:

```java
public final class TokenStorage {
    private static final ThreadLocal<String> TOKEN = new ThreadLocal<>();

    public static void set(String token) { TOKEN.set(token); }
    public static String get() { return TOKEN.get(); }
    public static void clear() { TOKEN.remove(); }
}
```

> [!danger] Один static driver ломает параллель
> Один `public static WebDriver driver` означает что все threads делят один browser. Thread A может сделать navigate или quit пока Thread B ещё его использует. Используй `ThreadLocal` или driver с test scope.

**Когда использовать:** всегда используй ThreadLocal (или test-scoped instance) для любого mutable состояния которое должно быть разным для каждого thread: WebDriver, per-user token, per-test data.

> [!tip] Singleton и ThreadLocal
> | Пункт | Singleton | ThreadLocal |
> |---|---|---|
> | Scope | Один instance на всю JVM | Один instance на thread |
> | Безопасен для параллели? | Только если immutable или synchronized read-only | Да — каждый thread изолирован |
> | Хороший пример в AQA | Config, кешированный admin token (read-only после создания) | WebDriver, per-test token, per-test data |

---

## Facade и Service Object

### Facade

**Определение:** Facade предоставляет простой interface к сложной subsystem.

**Цель:** спрятать много низкоуровневых шагов за одним понятным методом.

**Как работает:** facade class имеет один-два public метода которые внутри вызывают несколько других classes или API endpoints. Caller не знает внутренние шаги.

**Преимущества:**

- делает код caller коротким и читаемым
- прячет внутреннюю сложность и порядок операций
- хранит workflow в одном месте — изменения затрагивают один class

**Недостатки:**

- может стать "god object" если прячет слишком много несвязанных workflows
- прячет детали которые конкретный test может захотеть контролировать — может понадобиться низкоуровневый метод

Пример — login часто не один запрос (получить nonce, отправить credentials, сохранить refresh token). Facade это прячет:

```java
public final class AuthService {
    private final String baseUri;

    public AuthService(String baseUri) {
        this.baseUri = baseUri;
    }

    // Facade — один вызов делает весь flow
    public String loginAsAdmin() {
        return login(Config.get("admin.username"), Config.get("admin.password"));
    }

    public String login(String username, String password) {
        return given()
            .baseUri(baseUri)
            .contentType(ContentType.JSON)
            .body(new LoginRequest(username, password))
        .when()
            .post("/api/auth/login")
        .then()
            .statusCode(200)
            .extract().path("accessToken");
    }

    // Низкоуровневый метод для негативных тестов
    public Response loginWithResponse(String username, String password) {
        return given()
            .baseUri(baseUri)
            .contentType(ContentType.JSON)
            .body(new LoginRequest(username, password))
        .when()
            .post("/api/auth/login");
    }
}
```

Test для самой фичи использует низкоуровневый метод. Все остальные tests используют facade:

```java
// Негативный тест — проверяет саму фичу auth
@Test
void loginWithWrongPasswordReturns401() {
    authService.loginWithResponse("admin", "wrong")
        .then().statusCode(401);
}

// Все остальные тесты — переиспользуют facade, без дублирования
@Test
void adminCanViewOrders() {
    String token = authService.loginAsAdmin();
    given().header("Authorization", "Bearer " + token)
    .when().get("/api/orders")
    .then().statusCode(200);
}
```

Ещё лучше — объединить Facade с TokenManager и Builder из предыдущих разделов чтобы test вообще не вызывал `login`:

```java
given().spec(ApiSpecs.adminSpec())
.when().get("/api/orders")
.then().statusCode(200);
```

### Service Object и API Object

**Определение:** Service Object (также называют API Object) — это Facade применённый к тестам. Он представляет один backend service или доменную область как object с бизнес-методами.

**Цель:** держать tests на уровне бизнес-языка и прятать HTTP детали, так же как Page Object прячет Selenium детали.

**Как работает:** у каждого service (Orders, Products, Users) свой class. Методы названы по бизнес-действиям, а не по HTTP глаголам. Locators и status codes остаются внутри service.

```java
public final class OrderService {
    private final RequestSpecification spec;

    public OrderService(RequestSpecification spec) {
        this.spec = spec; // уже содержит baseUri + auth
    }

    public Order createOrder(Product product) {
        return given().spec(spec)
            .body(product)
        .when()
            .post("/api/orders")
        .then()
            .statusCode(201)
            .extract().as(Order.class);
    }

    public List<Order> getMyOrders() {
        return given().spec(spec)
        .when()
            .get("/api/orders")
        .then()
            .statusCode(200)
            .extract().jsonPath().getList("", Order.class);
    }
}
```

```java
@Test
void userCanCreateAndSeeOrder() {
    OrderService orderService = new OrderService(ApiSpecs.forRole(Role.USER));
    Order created = orderService.createOrder(new Product("iPhone"));
    assertThat(orderService.getMyOrders()).contains(created);
}
```

Test читается как бизнес-шаги. HTTP method, path и status code не дублируются в tests.

**Когда использовать:** используй Facade / Service Object когда workflow требует 3 и более шагов или когда одна API последовательность используется во многих tests. Для одного простого `GET` который используется один раз — достаточно прямого `given().when().get()` в тесте.

> [!tip] Page Object и Service Object
> | Пункт | Page Object | Service Object |
> |---|---|---|
> | Прячет | Locators и клики | URLs, headers, status codes |
> | Для | UI tests | API tests |
> | Имена методов | `loginAs()`, `openCart()` | `createOrder()`, `getMyOrders()` |
> | Идея одна? | Да — оба Facade для тестов |

---

## Decorator и Filter

### Decorator

**Определение:** Decorator оборачивает object и добавляет новое behavior не меняя исходный class.

**Цель:** добавить логирование, retry, замер времени или Allure steps к существующему object через обёртку.

**Как работает:** decorator реализует тот же interface что и исходный object, хранит оригинал внутри и делегирует ему вызовы добавляя логику до или после.

**Преимущества:**

- добавляет behavior без изменения существующего кода
- decorators можно комбинировать (logging + retry + timing)
- следует Open/Closed Principle

**Недостатки:**

- много обёрток усложняют debugging — нужно знать порядок обёртки
- чрезмерное использование создаёт много маленьких classes

Пример — добавить retry к любому API вызову:

```java
public interface ApiCaller {
    Response call(RequestSpecification spec, String path);
}

public final class RetryingCaller implements ApiCaller {
    private final ApiCaller original;
    private final int maxAttempts;

    public RetryingCaller(ApiCaller original, int maxAttempts) {
        this.original = original;
        this.maxAttempts = maxAttempts;
    }

    @Override
    public Response call(RequestSpecification spec, String path) {
        for (int attempt = 1; attempt <= maxAttempts; attempt++) {
            Response response = original.call(spec, path);
            if (response.statusCode() < 500) {
                return response;
            }
            // server error — retry
        }
        throw new IllegalStateException("Failed after " + maxAttempts + " attempts");
    }
}
```

### Filter и Interceptor

**Определение:** Filter (также называют Interceptor) — это Decorator для цепочки requests и responses. Он видит каждый request до отправки и каждый response до того как он попадёт в test.

**Цель:** добавить cross-cutting behavior один раз для всех API вызовов: authentication, logging, Allure attachment или обновление token.

**Как работает:** HTTP client (REST Assured, OkHttp) вызывает каждый filter по порядку. Filter может изменить request, передать его следующему filter и затем изменить response.

В REST Assured любой `Filter` можно добавить в `RequestSpecification`:

```java
public final class AuthFilter implements Filter {
    private final Supplier<String> tokenSupplier;

    public AuthFilter(Supplier<String> tokenSupplier) {
        this.tokenSupplier = tokenSupplier;
    }

    @Override
    public Response filter(FilterableRequestSpecification requestSpec,
                           FilterableResponseSpecification responseSpec,
                           FilterContext ctx) {
        // Добавляем auth header к каждому request автоматически
        requestSpec.header("Authorization", "Bearer " + tokenSupplier.get());
        return ctx.next(requestSpec, responseSpec);
    }
}
```

```java
public final class LoggingFilter implements Filter {
    @Override
    public Response filter(FilterableRequestSpecification requestSpec,
                           FilterableResponseSpecification responseSpec,
                           FilterContext ctx) {
        System.out.println("Request: " + requestSpec.getMethod() + " " + requestSpec.getURI());
        Response response = ctx.next(requestSpec, responseSpec);
        System.out.println("Response: " + response.statusCode());
        return response;
    }
}
```

Собери одну спецификацию со всеми filters. Каждый test её переиспользует:

```java
RequestSpecification spec = new RequestSpecBuilder()
    .setBaseUri(Config.getBaseUrl())
    .addFilter(new AuthFilter(TokenManager::getAdminToken))
    .addFilter(new LoggingFilter())
    .addFilter(new AllureRestAssured())
    .build();

// Нет header, нет кода логирования в тесте — filters делают это
given().spec(spec)
    .body(new Product("iPhone"))
.when().post("/api/products")
.then().statusCode(201);
```

**Когда использовать:** используй Decorator / Filter когда одно поведение должно применяться ко многим requests или tests: header auth, logging, Allure steps, retry при 500 или авто-обновление token при 401. Если поведение нужно только одному тесту — оставь его в этом тесте.

> [!tip] Decorator и Filter
> | Пункт | Decorator | Filter |
> |---|---|---|
> | Оборачивает | Один object | Всю цепочку request и response |
> | Порядок | Ручной порядок обёртки | Порядок цепочки задаётся порядком `addFilter` |
> | Хороший пример в AQA | Retry, замер времени для одного client | Header auth, logging для всех API вызовов |

---

## Proxy Pattern

**Определение:** Proxy — это placeholder который контролирует доступ к другому object.

**Цель:** добавить ленивую загрузку, контроль доступа, кеш или логирование не меняя реальный object.

**Как работает:** proxy реализует тот же interface что и реальный object. Он хранит реальный object (или создаёт его позже) и перенаправляет ему вызовы добавляя логику контроля до или после.

**Преимущества:**

- добавляет контроль без изменения реального object
- поддерживает ленивое создание — реальный object создаётся только когда нужен
- может кешировать результаты

**Недостатки:**

- добавляет косвенность — сложнее понять какой object реально вызывается
- может скрывать что реальный object дорогой

Пример в AQA — ленивый WebElement который ждёт перед каждым действием:

```java
public final class WaitingElement implements WebElement {
    private final WebDriver driver;
    private final By locator;
    private final Duration timeout;

    public WaitingElement(WebDriver driver, By locator, Duration timeout) {
        this.driver = driver;
        this.locator = locator;
        this.timeout = timeout;
    }

    private WebElement realElement() {
        return new WebDriverWait(driver, timeout)
            .until(ExpectedConditions.presenceOfElementLocated(locator));
    }

    @Override
    public void click() {
        realElement().click();
    }

    @Override
    public void sendKeys(CharSequence... keys) {
        realElement().sendKeys(keys);
    }

    // делегируй остальные методы WebElement к realElement()
}
```

Test использует `WaitingElement` как обычный `WebElement`, но каждое действие ждёт автоматически. Реальный element находится только в момент действия — это снижает `StaleElementReferenceException`.

Ещё пример — proxy который логирует каждый click:

```java
public final class LoggingElement implements WebElement {
    private final WebElement real;
    private final String name;

    public LoggingElement(WebElement real, String name) {
        this.real = real;
        this.name = name;
    }

    @Override
    public void click() {
        System.out.println("Click: " + name);
        real.click();
    }
}
```

**Когда использовать:** используй Proxy когда нужно контролировать доступ к object не меняя каждого caller: ленивая загрузка elements, логирование или кеш. Для одного места достаточно простого helper method.

> [!tip] Decorator и Proxy
> | Пункт | Decorator | Proxy |
> |---|---|---|
> | Цель | Добавить новое behavior | Контролировать доступ к существующему behavior |
> | Создаёт реальный object | Caller создаёт, decorator оборачивает | Proxy может создать реальный object позже |
> | Пример в AQA | Добавить retry к client | Добавить waiting к element |

---

## Adapter Pattern

**Определение:** Adapter конвертирует interface одного class в другой interface который ожидает client.

**Цель:** заставить несовместимые interfaces работать вместе не меняя ни одну из сторон.

**Как работает:** adapter реализует ожидаемый interface, хранит несовместимый object и транслирует к нему вызовы.

**Преимущества:**

- соединяет код который не был спроектирован для совместной работы
- изолирует трансляцию в одном месте

**Недостатки:**

- добавляет ещё один слой косвенности
- может скрывать что две системы имеют разную семантику, а не просто разные имена методов

Примеры в AQA — тебе нужны Adapters когда framework должен работать с разными tools которые делают одну работу разными API:

```java
// Framework ожидает этот interface
public interface TestReporter {
    void logStep(String name);
    void attachScreenshot(byte[] image);
}

// Allure имеет другой API
public final class AllureAdapter implements TestReporter {
    @Override
    public void logStep(String name) {
        Allure.step(name);
    }

    @Override
    public void attachScreenshot(byte[] image) {
        Allure.getLifecycle().addAttachment("screenshot", "image/png", "png", image);
    }
}

// ReportPortal — другой API
public final class ReportPortalAdapter implements TestReporter {
    @Override
    public void logStep(String name) {
        ReportPortal.emitLog(name);
    }

    @Override
    public void attachScreenshot(byte[] image) {
        ReportPortal.emitLog("screenshot", "INFO", new Date(), image);
    }
}
```

Test использует `TestReporter`. Переключение с Allure на ReportPortal требует только замены adapter, а не каждого теста.

Ещё пример — один interface для разных message brokers:

```java
public interface MessagePublisher {
    void publish(String topic, String message);
}

public final class RabbitAdapter implements MessagePublisher {
    private final RabbitTemplate rabbit;
    public void publish(String topic, String message) { rabbit.convertAndSend(topic, message); }
}

public final class KafkaAdapter implements MessagePublisher {
    private final KafkaTemplate<String, String> kafka;
    public void publish(String topic, String message) { kafka.send(topic, message); }
}
```

**Когда использовать:** используй Adapter когда нужно поддержать две или более внешние системы с разными API но одной ролью (reporting, messaging, cloud). Если ты используешь только одну систему — проще прямые вызовы.

---

## Observer и Listener

**Определение:** Observer определяет зависимость один-ко-многим. Когда один object (subject) меняет состояние, все зависимые objects (observers или listeners) уведомляются автоматически.

**Цель:** реагировать на события не вшивая реакцию в subject.

**Как работает:** subject хранит список listeners. Когда происходит событие, он вызывает метод у каждого listener. Listeners реализуют общий interface.

**Преимущества:**

- развязывает источник события и обработку события
- легко добавить новую реакцию не меняя источник
- одно событие может вызвать много реакций (log, screenshot, Allure step)

**Недостатки:**

- поток менее прямой — нужно искать какие listeners зарегистрированы
- неверный порядок или падающий listener может скрыть реальную проблему

Пример в AQA — JUnit 5 extension который делает screenshot при падении. Framework — subject, твой extension — observer:

```java
public final class ScreenshotExtension implements TestWatcher {

    @Override
    public void testFailed(ExtensionContext context, Throwable cause) {
        WebDriver driver = DriverManager.get();
        byte[] image = ((TakesScreenshot) driver).getScreenshotAs(OutputType.BYTES);
        Allure.getLifecycle().addAttachment("failure-screenshot", "image/png", "png", image);
        System.out.println("Test failed: " + context.getDisplayName() + " — " + cause.getMessage());
    }

    @Override
    public void testSuccessful(ExtensionContext context) {
        // ничего не делаем при успехе
    }
}
```

```java
@ExtendWith(ScreenshotExtension.class)
class CheckoutTest {

    @Test
    void userCanCheckout() {
        // если этот test упадёт, ScreenshotExtension.testFailed вызовется автоматически
    }
}
```

WebDriver listener — реагирует на каждый click не меняя Page Objects:

```java
public final class LoggingListener implements WebDriverListener {

    @Override
    public void beforeClick(WebElement element) {
        System.out.println("Before click: " + element);
    }

    @Override
    public void afterClick(WebElement element) {
        System.out.println("After click: " + element);
    }
}

WebDriver original = new ChromeDriver();
WebDriver driver = original; // обёрнут listener
```

Allure, ReportPortal и custom logging — все Observers. Test их не вызывает; framework уведомляет их о событиях.

**Когда использовать:** используй Observer / Listener для cross-cutting реакций на события lifecycle теста: screenshots при падении, логирование каждого шага или отправка результатов на dashboard. Если реакция нужна только в одном тесте — оставь её в этом тесте.

---

## Template Method и Backdoor

### Template Method

**Определение:** Template Method определяет скелет algorithm в base class и позволяет subclasses кастомизировать отдельные шаги.

**Цель:** переиспользовать общий flow позволяя вариации в деталях.

**Как работает:** base class имеет final method который вызывает несколько шагов в фиксированном порядке. Некоторые шаги abstract или имеют default behavior; subclasses их переопределяют.

**Преимущества:**

- переиспользует общий flow в одном месте
- обеспечивает одинаковый порядок шагов для всех subclasses
- легко добавить новую вариацию через новый subclass

**Недостатки:**

- наследование жёсткое — композиция часто гибче
- base class может стать сложно менять если много subclasses от него зависят

Пример в AQA — общий setup и teardown для всех tests:

```java
public abstract class BaseTest {

    @BeforeEach
    final void baseSetUp() {
        setUpDriver();
        setUpTestData();
        // Template method — subclasses могут добавить, но порядок фиксирован
    }

    @AfterEach
    final void baseTearDown() {
        takeScreenshotIfFailed();
        quitDriver();
        cleanUpTestData();
    }

    protected void setUpDriver() {
        WebDriver driver = BrowserFactory.create(Browser.CHROME);
        DriverManager.set(driver);
    }

    protected void quitDriver() {
        DriverManager.remove();
    }

    // Subclasses переопределяют только то что отличается
    protected abstract void setUpTestData();
    protected abstract void cleanUpTestData();

    private void takeScreenshotIfFailed() {
        // проверить статус теста и приложить screenshot
    }
}
```

```java
final class OrderTest extends BaseTest {
    private String orderId;

    @Override
    protected void setUpTestData() {
        orderId = new OrderService(ApiSpecs.adminSpec()).createOrder(new Product("Book")).id();
    }

    @Override
    protected void cleanUpTestData() {
        new OrderService(ApiSpecs.adminSpec()).deleteOrder(orderId);
    }

    @Test
    void userCanViewOrder() {
        // driver и orderId уже подготовлены BaseTest
    }
}
```

В современном JUnit 5 композиция с `@ExtendWith` часто предпочтительнее наследования, но идея та же: одно место определяет flow, tests определяют детали.

**Когда использовать:** используй Template Method когда много tests разделяют один порядок setup и teardown. Если только 2 tests разделяют flow — достаточно простого helper method. Для нового кода рассмотри JUnit Extension (Observer) вместо наследования.

### Backdoor

**Определение:** Backdoor (также называют Test Backdoor) устанавливает состояние приложения через более быстрый, не-UI канал вместо прохода через UI.

**Цель:** сделать tests быстрее и стабильнее пропустив медленные UI шаги которые не являются фокусом теста.

**Как работает:** test вызывает API, database или JavaScript injection чтобы подготовить состояние, затем открывает или обновляет UI. UI видит то же состояние как если бы user прокликал его.

**Преимущества:**

- намного быстрее — один API вызов заменяет много кликов
- стабильнее — у API нет waits или locators
- изолирует тестируемую фичу — падение login UI не ломает все остальные tests

**Недостатки:**

- обойдённый UI flow не тестируется — всё равно нужны несколько реальных UI login tests
- backdoor может использовать внутренние APIs которые меняются

Пример — каждому UI тесту нужен залогиненный user. Не кликай форму логина 50 раз:

Плохо — медленный UI login в каждом тесте:

```java
@Test
void userCanCheckout() {
    driver.get("/login");
    driver.findElement(By.id("email")).sendKeys("user@example.com");
    driver.findElement(By.id("password")).sendKeys("123456");
    driver.findElement(By.id("loginBtn")).click(); // 5 секунд, flaky
    // настоящий тест начинается здесь
}
```

Хорошо — API backdoor, затем инъекция состояния в browser:

```java
public final class SessionHelper {

    private SessionHelper() {
    }

    public static void loginViaApiAs(WebDriver driver, String username, String password) {
        String token = new AuthService(Config.getBaseUrl()).login(username, password);
        driver.get(Config.getBaseUrl()); // сначала открыть домен чтобы задать storage
        ((JavascriptExecutor) driver).executeScript(
            "window.localStorage.setItem('accessToken', arguments[0])", token);
        // Для cookie-based auth:
        // driver.manage().addCookie(new Cookie("session", token));
        driver.navigate().refresh();
    }
}
```

```java
@BeforeEach
void setUp() {
    WebDriver driver = BrowserFactory.create(Browser.CHROME);
    DriverManager.set(driver);
    SessionHelper.loginViaApiAs(driver, "user@example.com", "123456"); // ~200ms
}

@Test
void userCanCheckout() {
    // Уже залогинен — тестируем checkout, а не login
    new HomePage(DriverManager.get()).openCart();
}
```

Оставь несколько реальных UI login tests чтобы проверить саму страницу логина. Используй backdoor для всех остальных tests.

**Когда использовать:** используй Backdoor когда шаги setup не являются фокусом теста: login, создание test data или установка permissions. Всегда имей хотя бы один реальный end-to-end test который проходит через UI для обойдённого flow.

> [!tip] Template Method и Backdoor
> | Пункт | Template Method | Backdoor |
> |---|---|---|
> | Решает | Один flow во многих tests | Медленный UI setup |
> | Как | Base class задаёт порядок | Более быстрый канал (API/DB/JS) готовит состояние |
> | Комбинировать | BaseTest может вызывать SessionHelper.loginViaApi | Да — частый паттерн |

---

## Семейство Page Object

Эти три паттерна работают вместе. Это самые важные паттерны для UI tests и они требуются для вопроса 1 из Skills Matrix и для заметки [[20 Архитектура фреймворка автотестов]].

### Page Object

**Определение:** Page Object представляет одну страницу или большую область UI как class. Он хранит locators и page actions.

**Цель:** держать locators и низкоуровневые Selenium вызовы в одном месте, чтобы tests были читаемыми и изменения UI затрагивали один class.

**Как работает:** у каждой страницы private `By` locators и public методы которые описывают действия user. Test вызывает метод, страница делает клики и waits. Test никогда не вызывает `driver.findElement` напрямую.

**Преимущества:**

- меньше дублирования — один locator, одно место для исправления
- tests читаются как бизнес-шаги, а не Selenium вызовы
- легче поддерживать когда UI меняется

**Недостатки:**

- плохие page objects становятся огромными — разбивай их на components
- page objects не должны содержать бизнес assertions — они возвращают состояние, test делает assert

```java
public final class LoginPage {
    private final WebDriver driver;
    private final By emailInput = By.id("email");
    private final By passwordInput = By.id("password");
    private final By loginButton = By.id("loginBtn");

    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }

    public HomePage loginAs(String email, String password) {
        driver.findElement(emailInput).sendKeys(email);
        driver.findElement(passwordInput).sendKeys(password);
        driver.findElement(loginButton).click();
        return new HomePage(driver);
    }

    public boolean isErrorVisible() {
        return !driver.findElements(By.id("error")).isEmpty();
    }
}
```

```java
@Test
void loginWithWrongPasswordShowsError() {
    LoginPage loginPage = new LoginPage(DriverManager.get());
    loginPage.loginAs("user@example.com", "wrong");
    assertTrue(loginPage.isErrorVisible());
}
```

Для случая "тестируем страницу логина" используй Page Object. Для всех остальных tests используй Backdoor из предыдущего раздела чтобы пропустить эту страницу.

### Component Object

**Определение:** Component Object представляет переиспользуемую часть UI: header, table, modal или date picker.

**Цель:** избежать дублирования когда один UI блок появляется на многих страницах.

**Как работает:** component имеет root locator и ищет внутренние elements относительно root. Pages собираются из components через composition.

```java
public final class Header {
    private final WebElement root;

    public Header(WebElement root) {
        this.root = root;
    }

    public void openCart() {
        root.findElement(By.cssSelector("[data-test='cart-icon']")).click();
    }

    public String getUserName() {
        return root.findElement(By.cssSelector("[data-test='user-name']")).getText();
    }
}

public final class HomePage {
    private final WebDriver driver;
    private final Header header;

    public HomePage(WebDriver driver) {
        this.driver = driver;
        this.header = new Header(driver.findElement(By.tagName("header")));
    }

    public Header header() { return header; }
}
```

```java
homePage.header().openCart();
```

**Когда использовать:** используй Component Object когда один блок появляется на 2 и более страницах или когда страница слишком большая. Если блок используется только один раз — оставь его внутри Page Object.

### Element Wrapper

**Определение:** Element Wrapper — это маленький object вокруг одного `WebElement` который добавляет waiting, logging или доменный смысл.

**Цель:** дать общим elements (Button, Input, Table) понятный API и добавить waiting в одном месте.

**Как работает:** wrapper хранит `By` или `WebElement` и даёт методы вроде `safeClick()` или `type()` которые включают waits и logging перед делегированием к Selenium.

```java
public final class Button {
    private final WebDriver driver;
    private final By locator;

    public Button(WebDriver driver, By locator) {
        this.driver = driver;
        this.locator = locator;
    }

    public void click() {
        new WebDriverWait(driver, Duration.ofSeconds(10))
            .until(ExpectedConditions.elementToBeClickable(locator))
            .click();
    }
}

public final class Input {
    private final WebDriver driver;
    private final By locator;

    public Input(WebDriver driver, By locator) {
        this.driver = driver;
        this.locator = locator;
    }

    public void type(String text) {
        WebElement element = new WebDriverWait(driver, Duration.ofSeconds(10))
            .until(ExpectedConditions.visibilityOfElementLocated(locator));
        element.clear();
        element.sendKeys(text);
    }
}
```

Page Objects используют wrappers вместо сырого `By`:

```java
public final class LoginPage {
    private final Button loginButton;
    private final Input emailInput;

    public LoginPage(WebDriver driver) {
        this.loginButton = new Button(driver, By.id("loginBtn"));
        this.emailInput = new Input(driver, By.id("email"));
    }

    public void loginAs(String email) {
        emailInput.type(email);
        loginButton.click();
    }
}
```

**Когда использовать:** используй Element Wrapper когда многим tests нужна одна логика waiting или logging для базовых controls. Не создавай wrapper со 100 методами — дай ему только те методы которые реально переиспользуются.

> [!info] Семейство Page Object вместе
> Element Wrapper → Component Object → Page Object. Wrappers работают с одним control, components — с блоками, pages собирают components. В терминах GoF это Composite, но для AQA называй это семейством Page Object.

---

## Выбор паттерна

Выбирай паттерн на основе проблемы, а не имени class, который хочется создать.

| Проблема | Подходящий паттерн | Пример в AQA | Более простое решение |
|---|---|---|---|
| Client code не должен знать concrete class | Factory | Создание `ChromeDriver` или `FirefoxDriver` | Прямой constructor для одной implementation |
| Object имеет много optional fields и полезных defaults | Builder | Создание test users или request specifications | Constructor для небольшого value object |
| Один behavior должен заменяться во время выполнения | Strategy | Выбор authentication или retry logic | Небольшой `if` для двух стабильных случаев |
| Один instance должен быть общим глобально | Singleton | Config, кешированный token | Static factory method |
| Mutable состояние должно быть изолировано на thread | ThreadLocal | WebDriver, per-thread token | Передача object как параметра |
| Сложный workflow должен быть одним понятным вызовом | Facade / Service Object | `AuthService.loginAsAdmin()`, `OrderService.createOrder()` | Прямые API вызовы для одного простого request |
| Cross-cutting behavior для многих requests | Decorator / Filter | Header auth, logging, Allure | Код в каждом тесте для одного случая |
| Доступ к object нужно контролировать или делать ленивым | Proxy | Waiting element, logging element | Прямой вызов element |
| Два несовместимых interfaces должны работать вместе | Adapter | Allure vs ReportPortal reporter | Прямые вызовы если одна система |
| Реакция на события теста без связанности | Observer / Listener | Screenshot при падении, логирование шагов | Код в каждом тесте |
| Многие tests делят один порядок setup | Template Method | BaseTest setUp и tearDown | Helper method для 1–2 tests |
| UI setup медленный и не фокус теста | Backdoor | `loginViaApi` для UI tests | Реальные UI шаги для самой фичи login |
| Locators и клики дублируются в tests | Семейство Page Object | LoginPage, Header component, Button wrapper | Прямые locators для разовой проверки |

Сначала задай вопросы:

1. Какое изменение сейчас выполнить сложно?
2. Какое дублирование уберёт паттерн?
3. Может ли ту же проблему решить небольшой method или class?
4. Поймёт ли другой разработчик abstraction без длинного объяснения?
5. Можно ли тестировать каждый component отдельно?

---

## Совместное использование паттернов в AQA-фреймворке

Паттерны решают разные проблемы и могут работать вместе. Реальный framework использует несколько сразу:

1. **Factory** выбирает и создаёт нужный `WebDriver`.
2. **Singleton + ThreadLocal** даёт config и изолирует driver и token на thread.
3. **Builder** создаёт test data и переиспользуемую `RequestSpecification`.
4. **Strategy** выбирает authentication или retry behavior.
5. **Facade / Service Object** прячет API детали за бизнес-методами.
6. **Decorator / Filter** добавляет header auth и logging к каждому request.
7. **Observer / Listener** делает screenshots при падении и логирует шаги.
8. **Template Method + Backdoor** готовит быстрое, консистентное состояние теста.
9. **Семейство Page Object** прячет UI детали от tests.

Пример структуры framework:

```text
src/test/java
├── config
│   └── Config.java                     // Singleton
├── driver
│   ├── Browser.java
│   ├── BrowserFactory.java             // Factory
│   └── DriverManager.java              // Singleton + ThreadLocal
├── testdata
│   └── UserDataBuilder.java            // Builder
├── api
│   ├── ApiClient.java                  // Facade
│   ├── ApiSpecs.java                   // Builder + Filter
│   ├── auth
│   │   ├── AuthStrategy.java           // Strategy
│   │   ├── BearerTokenStrategy.java
│   │   ├── BasicAuthStrategy.java
│   │   └── AuthService.java            // Facade
│   ├── filter
│   │   ├── AuthFilter.java             // Filter / Decorator
│   │   └── LoggingFilter.java
│   └── service
│       └── OrderService.java           // Service Object
├── ui
│   ├── pages
│   │   ├── LoginPage.java              // Page Object
│   │   └── HomePage.java
│   ├── components
│   │   └── Header.java                 // Component Object
│   └── elements
│       ├── Button.java                 // Element Wrapper / Proxy
│       └── Input.java
├── listeners
│   └── ScreenshotExtension.java        // Observer
├── helpers
│   └── SessionHelper.java              // Backdoor
└── tests
    ├── BaseTest.java                   // Template Method
    └── OrderTest.java
```

Пример flow — UI test для checkout:

```java
@ExtendWith(ScreenshotExtension.class) // Observer — screenshot при падении
class CheckoutTest extends BaseTest {  // Template Method — общий setup

    @BeforeEach
    void setUpUser() {
        // Backdoor — быстрый API login вместо UI кликов
        SessionHelper.loginViaApiAs(DriverManager.get(), "user@example.com", "123456");
    }

    @Test
    void userCanCheckout() {
        // Семейство Page Object — нет locators в тесте
        new HomePage(DriverManager.get())
            .header().openCart();
        // Service Object + Reusable Spec — API часть без дублирования auth
        Order order = new OrderService(ApiSpecs.forRole(Role.USER))
            .createOrder(new Product("iPhone"));
        assertThat(order.status()).isEqualTo("CREATED");
    }
}
```

Такое разделение поддерживает принципы framework из [[20 Архитектура фреймворка автотестов]] и design principles из [[23 Инженерные практики для AQA]].

> [!info] У паттернов разные обязанности
> Factory отвечает на вопрос «какой object создать?», Builder — «как собрать сложный object?», Strategy — «какой behavior использовать?», Facade — «как сделать сложный workflow простым?», Decorator — «как добавить behavior не меняя object?», Proxy — «как контролировать доступ?», Adapter — «как соединить несовместимые interfaces?», Observer — «как реагировать на события?», Template Method — «как переиспользовать общий flow?», а Page Object — «как спрятать UI детали?».

---

## Типичные ошибки

| Ошибка | Почему это проблема | Что делать лучше |
|---|---|---|
| Добавлять паттерн до появления реальной потребности | Код становится сложнее, но проблема не исчезает | Начни с простого решения и выполни refactoring при появлении изменений или дублирования |
| Называть каждый creation helper Factory Method | Simple Factory путается с GoF pattern | Называй точный используемый паттерн |
| Помещать setup, logging, waits и cleanup в одну Factory | Factory получает слишком много responsibilities | Оставь Factory только creation logic |
| Использовать Builder для object с двумя required fields | Дополнительный код почти не приносит пользы | Используй constructor или factory method |
| Скрывать важные scenario values в Builder defaults | Test становится трудно понять | Показывай values, которые определяют scenario |
| Использовать один mutable Builder между tests | Parallel tests могут влиять друг на друга | Создавай новый builder для каждого object или test |
| Создавать Strategy classes для одного неизменяемого algorithm | Большое количество classes не добавляет гибкости | Используй одну прямую implementation |
| Оставлять большой `switch` внутри Strategy context | Behavior всё ещё не разделён | Перенеси каждый algorithm в отдельную strategy class |
| Хранить один global static `WebDriver` | Tests делят browser state и ломаются при parallel run | Используй ThreadLocal driver или test-scoped driver |
| Делать mutable Singleton общим между threads | Tests влияют друг на друга через скрытое глобальное состояние | Сделай Singleton immutable или используй ThreadLocal для mutable состояния |
| Делать Facade который прячет 10 несвязанных workflows | Facade становится god object | Раздели по домену — AuthService, OrderService |
| Добавлять Decorator обёртки в случайном порядке | Logging или retry может сработать не вовремя | Документируй и тестируй порядок обёртки |
| Оборачивать каждый element в Proxy без нужды | Косвенность усложняет debugging | Оборачивай только elements которым нужен waiting или logging |
| Писать Adapter который прячет разную семантику | Две системы делают разные вещи, а не просто разные имена | Проверь что адаптируемые системы действительно разделяют один contract |
| Добавлять много Listeners которые зависят от порядка | Падающий listener может скрыть реальный test failure | Делай listeners маленькими, независимыми и отдельно тестируемыми |
| Использовать наследование для всего (злоупотребление Template Method) | Base class становится жёстким и сложным для изменения | Предпочитай композицию с JUnit Extensions для нового кода |
| Использовать Backdoor для фичи которую ты тестируешь | Обойдённый UI flow вообще не тестируется | Оставь несколько реальных UI tests для обойдённого flow |
| Делать Page Objects огромными с 50 locators и методами | Сложно найти и поддерживать | Раздели на Component Objects и Element Wrappers |
| Прятать assertions глубоко внутри Page Objects | Tests скрывают свою цель | Держи assertions в tests или отдельном verification layer |
| Объединять паттерны без чётких границ | Responsibilities скрываются между множеством layers | Дай каждому паттерну одну конкретную проблему |

---

## Суть темы

> [!summary] Главное за 30 секунд
> - Паттерн проектирования — повторно используемая идея решения типичной software problem; в AQA он упрощает расширение, тестирование и поддержку фреймворка
> - Категории: Creational управляют созданием objects (Factory, Builder, Singleton), Structural связывают classes и objects (Facade, Decorator, Proxy, Adapter), Behavioral организуют behavior (Strategy, Observer, Template Method)
> - Паттерн добавляют только когда есть реальная проблема — изменение или дублирование; для небольшой задачи достаточно constructor, одного method или простого condition
> - Factory централизует создание drivers и возвращает interface (`WebDriver`), а не concrete class; Builder собирает test data пошагово с defaults и validation в `build()`
> - Strategy делает behavior заменяемым через общий interface (способы authentication), Singleton подходит для дорогого read-only ресурса — config, кеш token
> - Для параллельных тестов mutable состояние (`WebDriver`, token) держат в ThreadLocal: один static driver делится всеми threads и ломает параллельный запуск
> - Семейство Page Object (Page Object, Component Object, Element Wrapper) прячет locators и Selenium от tests; Facade и Service Object делают то же для API деталей
> - Спрашивают чаще всего: Simple Factory vs Factory Method, Builder vs constructor, почему static `WebDriver` опасен при параллельном запуске, Decorator vs Proxy

---

## Вопросы для собеседования

### Вопросы начального уровня

**1. Что такое паттерн проектирования?**

Паттерн проектирования — это повторно используемая идея решения распространённой software problem. Это не готовый код, который всегда нужно копировать без изменений.

**2. Какие три основные категории паттернов существуют?**

Creational patterns управляют созданием objects, Structural patterns связывают objects, а Behavioral patterns организуют behavior и communication.

**3. Какую проблему решает Factory?**

Factory централизует создание objects и скрывает concrete implementations от client code.

**4. Какую проблему решает Builder?**

Builder пошагово создаёт сложные objects, особенно если у них есть optional values или полезные defaults.

**5. Какую проблему решает Strategy?**

Strategy делает behavior заменяемым через общий interface и позволяет выбрать его во время выполнения.

**6. Какую проблему решает Singleton?**

Singleton обеспечивает один общий instance для ресурса который дорогой или должен читаться один раз, например config или кешированный token.

**7. Какую проблему решает Facade?**

Facade даёт один простой метод для сложного workflow, например полного login flow.

**8. Какую проблему решает Page Object?**

Page Object хранит locators и page actions в одном месте, поэтому tests читаемы и изменения UI затрагивают один class.

### Вопросы среднего уровня

**1. Почему `BrowserFactory` должен возвращать `WebDriver`, а не `ChromeDriver`?**

Возврат interface убирает зависимость client code от одной browser implementation.

**2. В чём разница между Simple Factory и Factory Method?**

Simple Factory обычно выбирает implementation в одном method. Factory Method позволяет subclasses переопределять creation method.

**3. Когда Builder лучше constructor?**

Builder лучше, когда у object много optional values, defaults или validation rules. Constructor проще для нескольких required values.

**4. Как Strategy поддерживает Open/Closed Principle?**

Новый behavior можно добавить как новую strategy implementation без изменения context class.

**5. Можно ли использовать Factory, Builder и Strategy вместе?**

Да. Они решают разные проблемы: Factory создаёт implementation, Builder собирает data, а Strategy предоставляет заменяемый behavior.

**6. Почему `ThreadLocal<WebDriver>` нужен для параллельных тестов?**

Один static driver делится всеми threads. ThreadLocal даёт каждому thread свой driver, поэтому tests не мешают друг другу.

**7. В чём разница между Decorator и Proxy?**

Decorator добавляет новое behavior к object, а Proxy контролирует доступ к object (ленивая загрузка, ожидание или контроль доступа).

**8. Что такое Backdoor в автоматизации тестов?**

Backdoor устанавливает состояние через более быстрый канал (API, DB или localStorage) вместо кликов через UI. Он делает tests быстрее, но реальный UI flow всё равно нуждается в нескольких отдельных тестах.

**9. Почему tests должны использовать `ApiSpecs.adminSpec()` вместо добавления header в каждом тесте?**

Спецификация с filters добавляет header auth один раз. Если header или логика token изменится, нужно обновить только одно место, и параллель-безопасное хранение токена обрабатывается в одном месте.

### Вопросы повышенного уровня

**1. Чем опасна большая Factory?**

Она может собрать несвязанные setup, configuration, logging и lifecycle logic. Это нарушает Single Responsibility Principle и делает изменения рискованными.

**2. Почему Builder defaults могут ослабить tests?**

Важные scenario data могут стать невидимыми. Читатель не поймёт, какие values влияют на result.

**3. Когда conditional лучше Strategy?**

Небольшой стабильный conditional лучше, если существует только один или два простых cases и не ожидается их рост.

**4. Почему один static `WebDriver` небезопасен для parallel tests?**

Tests используют общий mutable browser state. Один test может выполнить navigation, закрыть или изменить тот же driver, пока его использует другой test.

**5. Как решить, нужен ли паттерн?**

Нужно определить реальную проблему изменения или дублирования, сравнить паттерн с более простым решением и проверить, делает ли abstraction код понятнее и удобнее для тестирования.

**6. Когда выбрать Adapter вместо прямых вызовов?**

Когда framework должен поддерживать две системы с разными API но одной ролью, например две reporting системы или два message brokers.

**7. Когда Observer/Listener становится рискованным?**

Когда listeners зависят от порядка, делают тяжёлую работу или скрывают failures. Listeners должны быть маленькими, независимыми и не влиять на результат теста сверх своей цели (например, сделать screenshot).

**8. Почему Singleton может быть опасен в тестах?**

Mutable Singleton — это глобальное состояние. Tests могут его изменить и повлиять на другие tests. Делай Singletons immutable или сбрасывай их явно, и используй ThreadLocal для per-thread mutable состояния.

**9. Как не дать Page Objects стать слишком большими?**

Раздели страницу на Component Objects и Element Wrappers. Каждый имеет одну ответственность и переиспользуется на разных страницах.

### Вопросы с кодом

**1. Какая implementation будет возвращена?**

```java
WebDriver driver = BrowserFactory.create(Browser.FIREFOX);
```

**Ответ:** Simple Factory вернёт object `FirefoxDriver` через interface `WebDriver`.

**2. Какие значения содержит `admin`?**

```java
UserData admin = new UserDataBuilder()
    .withName("Ana")
    .withEmail("ana@example.com")
    .withRole("ADMIN")
    .build();
```

**Ответ:** Он содержит name `Ana`, email `ana@example.com`, role `ADMIN` и default value `active`, равное `true`.

**3. Что изменится при замене `BasicAuthStrategy` на `BearerTokenStrategy`?**

```java
AuthStrategy auth = new BearerTokenStrategy(token);
ApiClient client = new ApiClient(baseUri, auth);
```

**Ответ:** Изменится authentication behavior, но `ApiClient` и его request methods изменять не нужно.

**4. Почему этот design небезопасен для parallel tests?**

```java
public final class DriverStore {
    public static WebDriver driver;
}
```

**Ответ:** Все tests используют один mutable driver. Parallel tests могут перезаписать, изменить или закрыть одну browser session. Используй ThreadLocal driver.

**5. Что не так с этим Singleton для параллельных тестов?**

```java
public final class TokenHolder {
    public static String token;
}
```

**Ответ:** Все threads делят одну mutable строку. Thread A может перезаписать token пока Thread B его использует. Используй ThreadLocal или передавай token как параметр.

**6. Что делает `AuthFilter` для каждого request?**

```java
RequestSpecification spec = new RequestSpecBuilder()
    .addFilter(new AuthFilter(TokenManager::getAdminToken))
    .build();

given().spec(spec)
.when().get("/api/orders");
```

**Ответ:** Filter добавляет header `Authorization` с текущим token перед каждым request. Test никогда не пишет header напрямую.

**7. Почему этот Backdoor полезен?**

```java
SessionHelper.loginViaApiAs(driver, "user@example.com", "123456");
```

**Ответ:** Он логинится через API и инжектит token в storage браузера. UI test стартует уже аутентифицированным и экономит около 5 секунд на test по сравнению с кликами по форме логина.

**8. Какой паттерн использует `ScreenshotExtension implements TestWatcher`?**

**Ответ:** Observer / Listener. Framework уведомляет extension о событиях теста. Test не вызывает extension напрямую.

