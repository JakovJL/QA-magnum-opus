# Design Patterns for AQA

## Table of Contents

- [[#Why Design Patterns Matter]]
- [[#Pattern Categories]]
	- [[#Creational Patterns]]
	- [[#Structural Patterns]]
	- [[#Behavioral Patterns]]
- [[#Factory Pattern]]
	- [[#Simple Factory]]
	- [[#Factory Method]]
- [[#Builder Pattern]]
	- [[#Test Data Builder]]
	- [[#Request Specification Builder]]
	- [[#Reusable Auth Specification]]
- [[#Strategy Pattern]]
	- [[#Authentication Strategy]]
- [[#Singleton and ThreadLocal Pattern]]
	- [[#Singleton]]
	- [[#ThreadLocal for Parallel Tests]]
- [[#Facade and Service Object Pattern]]
	- [[#Facade]]
	- [[#Service Object and API Object]]
- [[#Decorator and Filter Pattern]]
	- [[#Decorator]]
	- [[#Filter and Interceptor]]
- [[#Proxy Pattern]]
- [[#Adapter Pattern]]
- [[#Observer and Listener Pattern]]
- [[#Template Method and Backdoor Pattern]]
	- [[#Template Method]]
	- [[#Backdoor]]
- [[#Page Object Family]]
	- [[#Page Object]]
	- [[#Component Object]]
	- [[#Element Wrapper]]
- [[#Choosing a Pattern]]
- [[#Combining Patterns in an AQA Framework]]
- [[#Common Mistakes]]
- [[#Key Takeaways]]
- [[#Interview Questions]]
	- [[#Beginner Questions]]
	- [[#Intermediate Questions]]
	- [[#Advanced Questions]]
	- [[#Code Questions]]

**Related notes:** [[AQA Java eng]]

> [!caution] Not in the video course
> Zaur's courses end at Java Core — design patterns for AQA are not covered. This is the AQA layer: learn from this note and practice, and apply a pattern only when it solves a real design problem.

---

## Why Design Patterns Matter

A **design pattern** is a reusable design idea for a common software problem. It describes how classes and objects can work together; it is not finished code that must be copied exactly.

**Goal:** make code easier to extend, test, and maintain by using solutions that other developers recognize.

In an AQA framework, patterns can help with:

- creating browser drivers or API clients
- building test data with clear defaults
- reusing authentication without duplication
- changing behavior at runtime
- separating test scenarios from technical setup
- reducing large `if` and `switch` blocks
- running tests in parallel without shared state

Patterns are useful when change or duplication already exists. A direct constructor, one small method, or a simple conditional is often enough for a small problem.

> [!danger] Solve the Problem First
> Do not add a pattern only because it is popular. A pattern should remove a real problem, not add extra classes without value.

---

## Pattern Categories

The classic categories describe what kind of design problem a pattern solves.

### Creational Patterns

**Definition:** creational patterns control how objects are created.

**Goal:** hide complex construction and reduce direct dependency on concrete classes.

Common examples:

- Factory Method
- Abstract Factory
- Builder
- Prototype
- Singleton

In AQA, Factory, Builder, and Singleton are common for drivers, clients, configuration, and test data.

### Structural Patterns

**Definition:** structural patterns organize classes and objects into larger structures.

**Goal:** connect components without making them tightly dependent on one another.

Common examples:

- Adapter
- Decorator
- Facade
- Proxy
- Composite

In AQA, a Facade hides a complex login flow behind one method, a Decorator adds logging or retry to a request, an Adapter connects different tools, and a Proxy adds lazy loading or access control.

### Behavioral Patterns

**Definition:** behavioral patterns organize communication and responsibility between objects.

**Goal:** make behavior replaceable and keep decision logic out of large conditionals.

Common examples:

- Strategy
- Observer
- Command
- Template Method
- Chain of Responsibility

In AQA, Strategy selects authentication or retry logic, Observer reacts to test events, and Template Method defines a common setup and teardown flow.

> [!tip] Quick Comparison
> | Category | Main question | Pattern in this note | AQA example |
> |---|---|---|---|
> | Creational | How is an object created? | Factory, Builder, Singleton | Create a driver, test user, or config |
> | Structural | How are objects connected? | Facade, Decorator, Proxy, Adapter | Wrap API client, add auth header, adapt reporting |
> | Behavioral | How can behavior change? | Strategy, Observer, Template Method | Select auth method, listen to test events, reuse setup |

---

## Factory Pattern

**Definition:** a Factory centralizes object creation and returns an object through a common type.

**Goal:** remove direct construction of concrete implementations from tests and other client code.

**How it works:** the caller asks for a type or option. The factory chooses the concrete class, creates it, and returns an interface or base type.

**Advantages:**

- hides construction details
- keeps tests independent from concrete implementations
- places creation logic in one location
- makes new implementations easier to add

**Disadvantages:**

- adds another abstraction and more classes
- can become a large conditional if it handles too many responsibilities
- does not remove the need to configure and close created resources

### Simple Factory

A Simple Factory is a class or method that chooses an implementation, often with `switch`. It is commonly called a factory, but it is not the GoF Factory Method pattern.

Example for browser creation:

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

The test depends on `WebDriver`, not directly on one concrete driver class. The factory chooses the implementation.

**When to use:** use Simple Factory when you have 2–4 stable options and creation is a one-line `new` call.

### Factory Method

Factory Method defines a creation method in a base type and lets subclasses choose the concrete product.

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

**When to use:** use Factory Method when creation should vary through inheritance or each provider needs its own setup steps (options, capabilities, remote URL). For a small browser choice, a Simple Factory is usually easier.

> [!tip] Simple Factory vs Factory Method
> | Point | Simple Factory | Factory Method |
> |---|---|---|
> | Main mechanism | One method selects an implementation | Subclasses override a creation method |
> | GoF pattern | No | Yes |
> | Number of classes | Usually smaller | Usually larger |
> | Good AQA use | Small driver or client selection | Providers with different setup logic |

---

## Builder Pattern

**Definition:** Builder creates a complex object step by step and produces the final object with `build()`.

**Goal:** make object construction readable when there are many optional values, defaults, or validation rules.

**How it works:** the builder stores temporary values. Fluent methods change selected values and return the same builder. `build()` validates the state and creates the final object.

**Advantages:**

- makes test data readable
- supports useful defaults
- avoids constructors with many parameters
- allows reusable object variations
- can validate data before object creation

**Disadvantages:**

- adds extra code for simple objects
- bad defaults can hide important test data
- a mutable builder should not be shared between parallel tests

### Test Data Builder

Assume a test needs user data:

```java
public record UserData(
        String name,
        String email,
        String role,
        boolean active
) {
}
```

A builder gives valid defaults and lets each test change only important fields:

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

The test clearly shows what makes this user special. Other valid fields come from defaults.

**When to use:** use Test Data Builder when an object has many fields but a test cares about only 1–2 of them. Do not use it for an object with only 2 required fields — a constructor is simpler.

### Request Specification Builder

REST Assured provides a real Builder example:

```java
RequestSpecification specification = new RequestSpecBuilder()
    .setBaseUri("https://api.example.com")
    .setBasePath("/v1")
    .setContentType(ContentType.JSON)
    .addHeader("Accept", "application/json")
    .build();
```

Each method configures one part. `build()` returns the final `RequestSpecification`.

### Reusable Auth Specification

This is the Builder applied to authentication reuse. It solves the problem: "do not copy the `Authorization` header into every test".

Bad — duplication in every test:

```java
// Duplicated 50 times — hard to change
given().header("Authorization", "Bearer " + token)
    .body(product)
.when().post("/api/products");
```

Good — one reusable specification built once:

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

Usage — the test has zero auth details:

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

**Why this is better:** the token source, header name, and base URL live in one place. If the header changes from `Bearer` to `Token`, you change one method, not 50 tests.

> [!tip] Builder vs Constructor
> | Point | Constructor | Builder |
> |---|---|---|
> | Best for | A few required values | Many optional values or defaults |
> | Readability | Parameter meaning may be unclear | Fluent method names explain values |
> | Validation | Usually inside the constructor | Often inside `build()` |
> | Extra code | Low | Higher |

---

## Strategy Pattern

**Definition:** Strategy puts replaceable behavior behind a common interface.

**Goal:** choose or change an algorithm at runtime without changing the class that uses it.

**How it works:** a context receives a strategy object and delegates one responsibility to it. Different strategies implement the same interface in different ways.

**Advantages:**

- replaces large conditionals with polymorphism
- supports runtime behavior selection
- keeps each algorithm in a separate class
- makes behavior easy to test in isolation

**Disadvantages:**

- creates more small classes
- the caller must know which strategy to choose
- it is unnecessary when there is only one stable behavior

### Authentication Strategy

An API client may support different authentication methods. Without Strategy, the client has a large `if`:

```java
// Bad — grows with every new auth type
if (authType.equals("bearer")) { ... }
else if (authType.equals("basic")) { ... }
```

With Strategy, each method is a separate class:

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
        return request; // guest — do nothing
    }
}
```

The API client delegates authentication to the selected strategy:

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

The `ApiClient` does not need `if (authType == ...)`. A new authentication method can be added as another strategy without changing the client.

**When to use:** use Strategy when you have 3 or more behaviors that may grow (auth types, retry policies, waiting strategies). For 1–2 stable cases, a small `if` is simpler.

> [!tip] Strategy vs Conditional
> | Point | Conditional | Strategy |
> |---|---|---|
> | Good for | One small stable choice | Several behaviors that change or grow |
> | Extension | Modify the existing conditional | Add another implementation |
> | Testing | Branches are tested together | Each strategy is tested separately |
> | Cost | Very low | More classes and interfaces |

---

## Singleton and ThreadLocal Pattern

### Singleton

**Definition:** Singleton ensures that only one instance of a class exists and provides global access to it.

**Goal:** share one expensive or stateful resource without creating it many times.

**How it works:** the class has a private constructor, a private static field for the instance, and a public static method that creates the instance once and returns the same instance later. In AQA, the instance is often lazy and thread-safe.

**Advantages:**

- one place controls creation and lifecycle
- avoids creating the same expensive object many times (config, token, driver factory)
- easy global access

**Disadvantages:**

- global state is hard to reset between tests
- hides dependencies — a test uses the singleton without declaring it
- unsafe for parallel tests if the state is mutable and shared — every thread sees the same object

Example — configuration that should be read once:

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

    // For tests — reset between suites if needed
    static synchronized void reset() {
        instance = null;
    }
}
```

More useful in AQA — TokenManager with caching and expiration:

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

The first test triggers a real login. The next 49 tests reuse the cached token until it expires. This saves time and reduces load on the auth service.

**When to use:** use Singleton for truly global, expensive, or shared-read-only resources: configuration, token cache, read-only test data. Do not use it for mutable per-test state like `WebDriver` or per-test tokens in parallel runs.

### ThreadLocal for Parallel Tests

**Definition:** ThreadLocal gives each thread its own copy of a variable. Threads do not see each other's values.

**Goal:** make parallel tests isolated while keeping the same simple API (`get()` / `set()`).

**How it works:** you store the value in a `ThreadLocal` field. Each thread calls `get()` and receives its own value. The framework sets the value at test start and removes it at test end.

Example — driver that is safe for parallel tests:

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

Usage with JUnit 5:

```java
class CheckoutTest {

    @BeforeEach
    void setUp() {
        WebDriver driver = BrowserFactory.create(Browser.CHROME);
        DriverManager.set(driver);
    }

    @AfterEach
    void tearDown() {
        DriverManager.remove(); // quits and clears ThreadLocal
    }

    @Test
    void guestCanAddToCart() {
        // Any page can call DriverManager.get() and receives the driver for THIS thread
        new HomePage(DriverManager.get()).open();
    }
}
```

The same idea applies to tokens when tests run in parallel with different users:

```java
public final class TokenStorage {
    private static final ThreadLocal<String> TOKEN = new ThreadLocal<>();

    public static void set(String token) { TOKEN.set(token); }
    public static String get() { return TOKEN.get(); }
    public static void clear() { TOKEN.remove(); }
}
```

> [!danger] One Static Driver Breaks Parallel Tests
> A single `public static WebDriver driver` means all threads share one browser. Thread A can navigate or quit the browser while Thread B is still using it. Use `ThreadLocal` or a test-scoped driver instead.

**When to use:** always use ThreadLocal (or a test-scoped instance) for any mutable state that must be different per test thread: WebDriver, per-user token, per-test data.

> [!tip] Singleton vs ThreadLocal
> | Point | Singleton | ThreadLocal |
> |---|---|---|
> | Scope | One instance for the whole JVM | One instance per thread |
> | Safe for parallel? | Only if immutable or synchronized read-only | Yes — each thread is isolated |
> | Good AQA use | Config, cached admin token (read-only after creation) | WebDriver, per-test token, per-test data |

---

## Facade and Service Object Pattern

### Facade

**Definition:** Facade provides a simple interface to a complex subsystem.

**Goal:** hide many low-level steps behind one readable method.

**How it works:** the facade class has one or two public methods that internally call several other classes or API endpoints. The caller does not know the internal steps.

**Advantages:**

- makes the caller's code short and readable
- hides internal complexity and order of operations
- places the workflow in one place, so changes affect one class

**Disadvantages:**

- can become a "god object" if it hides too many unrelated workflows
- hides details that a specific test may need to control — you may still need a lower-level method

Example — login is not one request in many systems (get nonce, post credentials, store refresh token). A facade hides it:

```java
public final class AuthService {
    private final String baseUri;

    public AuthService(String baseUri) {
        this.baseUri = baseUri;
    }

    // Facade — one call does the whole flow
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

    // Lower-level method for negative tests
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

Test for the feature itself uses the low-level method. All other tests use the facade:

```java
// Negative test — checks the auth feature itself
@Test
void loginWithWrongPasswordReturns401() {
    authService.loginWithResponse("admin", "wrong")
        .then().statusCode(401);
}

// All other tests — reuse the facade, no duplication
@Test
void adminCanViewOrders() {
    String token = authService.loginAsAdmin();
    given().header("Authorization", "Bearer " + token)
    .when().get("/api/orders")
    .then().statusCode(200);
}
```

Even better — combine Facade with TokenManager and Builder from previous sections so the test never calls `login` at all:

```java
given().spec(ApiSpecs.adminSpec())
.when().get("/api/orders")
.then().statusCode(200);
```

### Service Object and API Object

**Definition:** Service Object (also called API Object) is a Facade applied to tests. It represents one backend service or domain area as an object with business methods.

**Goal:** keep tests at business language level and hide HTTP details, similar to how Page Object hides Selenium details.

**How it works:** each service (Orders, Products, Users) has its own class. Methods are named by business actions, not HTTP verbs. Locators and status codes stay inside the service.

```java
public final class OrderService {
    private final RequestSpecification spec;

    public OrderService(RequestSpecification spec) {
        this.spec = spec; // already contains baseUri + auth
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

The test reads as business steps. HTTP method, path, and status code are not duplicated in tests.

**When to use:** use Facade / Service Object when a workflow needs 3 or more steps or when the same API sequence is used in many tests. For a single simple `GET` used once, a direct `given().when().get()` in the test is fine.

> [!tip] Page Object vs Service Object
> | Point | Page Object | Service Object |
> |---|---|---|
> | Hides | Locators and clicks | URLs, headers, status codes |
> | Used for | UI tests | API tests |
> | Method names | `loginAs()`, `openCart()` | `createOrder()`, `getMyOrders()` |
> | Same idea | Yes — both are Facades for tests |

---

## Decorator and Filter Pattern

### Decorator

**Definition:** Decorator wraps an object and adds new behavior without changing the original class.

**Goal:** add logging, retry, timing, or Allure steps to an existing object by wrapping it.

**How it works:** the decorator implements the same interface as the original object, holds the original inside, and delegates calls to it while adding extra logic before or after.

**Advantages:**

- adds behavior without changing existing code
- decorators can be combined (logging + retry + timing)
- follows the Open/Closed Principle

**Disadvantages:**

- many wrappers make debugging harder — you need to know the wrapping order
- overuse creates many small classes

Example — add retry to any API call:

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

### Filter and Interceptor

**Definition:** Filter (also called Interceptor) is a Decorator for request and response chains. It sees every request before it is sent and every response before it reaches the test.

**Goal:** add cross-cutting behavior once for all API calls: authentication, logging, Allure attachment, or token refresh.

**How it works:** the HTTP client (REST Assured, OkHttp) calls each filter in order. A filter can modify the request, pass it to the next filter, and then modify the response.

In REST Assured, any `Filter` can be added to a `RequestSpecification`:

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
        // Add auth header to every request automatically
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

Build one specification with all filters. Every test reuses it:

```java
RequestSpecification spec = new RequestSpecBuilder()
    .setBaseUri(Config.getBaseUrl())
    .addFilter(new AuthFilter(TokenManager::getAdminToken))
    .addFilter(new LoggingFilter())
    .addFilter(new AllureRestAssured())
    .build();

// No auth header, no logging code in the test — filters do it
given().spec(spec)
    .body(new Product("iPhone"))
.when().post("/api/products")
.then().statusCode(201);
```

**When to use:** use Decorator / Filter when the same behavior must apply to many requests or tests: auth header, logging, Allure steps, retry on 500, or automatic token refresh on 401. If only one test needs the behavior, keep it in that test.

> [!tip] Decorator vs Filter
> | Point | Decorator | Filter |
> |---|---|---|
> | Wraps | One object | The whole request and response chain |
> | Order | Manual wrapping order | Chain order defined by `addFilter` order |
> | Good AQA use | Retry, timing for one client | Auth header, logging for all API calls |

---

## Proxy Pattern

**Definition:** Proxy is a placeholder that controls access to another object.

**Goal:** add lazy loading, access control, caching, or logging without changing the real object.

**How it works:** the proxy implements the same interface as the real object. It holds the real object (or creates it later) and forwards calls to it, adding control logic before or after.

**Advantages:**

- adds control without changing the real object
- supports lazy creation — the real object is created only when needed
- can cache results

**Disadvantages:**

- adds indirection — harder to see which object is really called
- can hide that the real object is expensive to create

AQA example — lazy WebElement that waits before every action:

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

    // delegate other WebElement methods to realElement()
}
```

The test uses `WaitingElement` like a normal `WebElement`, but every action waits automatically. The real element is found only at action time, which reduces `StaleElementReferenceException`.

Another example — proxy that logs every click:

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

**When to use:** use Proxy when you need to control access to an object without changing every caller: lazy loading of elements, logging, or caching. For one place, a simple helper method is enough.

> [!tip] Decorator vs Proxy
> | Point | Decorator | Proxy |
> |---|---|---|
> | Purpose | Add new behavior | Control access to existing behavior |
> | Creates real object | Caller creates it, decorator wraps it | Proxy may create the real object later |
> | AQA example | Add retry to a client | Add waiting to an element |

---

## Adapter Pattern

**Definition:** Adapter converts the interface of one class into another interface that the client expects.

**Goal:** make incompatible interfaces work together without changing either side.

**How it works:** the adapter implements the expected interface, holds the incompatible object, and translates calls to it.

**Advantages:**

- connects code that was not designed to work together
- isolates the translation in one place

**Disadvantages:**

- adds another layer of indirection
- can hide that two systems have different semantics, not just different method names

AQA examples — you need Adapters when the framework must work with different tools that do the same job with different APIs:

```java
// The framework expects this interface
public interface TestReporter {
    void logStep(String name);
    void attachScreenshot(byte[] image);
}

// Allure has a different API
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

// ReportPortal has another API
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

The test uses `TestReporter`. Switching from Allure to ReportPortal requires only changing the adapter, not every test.

Another example — one interface for different message brokers:

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

**When to use:** use Adapter when you must support two or more external systems with different APIs but the same role (reporting, messaging, cloud). If you use only one system, direct calls are simpler.

---

## Observer and Listener Pattern

**Definition:** Observer defines a one-to-many dependency. When one object (subject) changes state, all dependent objects (observers or listeners) are notified automatically.

**Goal:** react to events without hard-coding the reaction into the subject.

**How it works:** the subject keeps a list of listeners. When an event happens, it calls a method on each listener. Listeners implement a common interface.

**Advantages:**

- decouples event source from event handling
- easy to add new reactions without changing the source
- one event can trigger many reactions (log, screenshot, Allure step)

**Disadvantages:**

- flow is less direct — you need to find which listeners are registered
- wrong listener order or a failing listener can hide the real problem

AQA example — JUnit 5 extension that takes a screenshot on failure. The test framework is the subject, your extension is the observer:

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
        // do nothing on success
    }
}
```

```java
@ExtendWith(ScreenshotExtension.class)
class CheckoutTest {

    @Test
    void userCanCheckout() {
        // if this test fails, ScreenshotExtension.testFailed is called automatically
    }
}
```

WebDriver listener — react to every click without changing Page Objects:

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
WebDriver driver = original; // wrapped with listener
// Or with EventFiring: WebDriver driver = new EventFiringDecorator(new LoggingListener()).decorate(original);
```

Allure, ReportPortal, and custom logging are all Observers. The test does not call them; the framework notifies them on events.

**When to use:** use Observer / Listener for cross-cutting reactions to test lifecycle events: screenshots on failure, logging each step, or sending results to a dashboard. If the reaction is needed only in one test, keep it in that test.

---

## Template Method and Backdoor Pattern

### Template Method

**Definition:** Template Method defines the skeleton of an algorithm in a base class and lets subclasses customize specific steps.

**Goal:** reuse a common flow while allowing variation in details.

**How it works:** the base class has a final method that calls several steps in fixed order. Some steps are abstract or have default behavior; subclasses override them.

**Advantages:**

- reuses the common flow in one place
- enforces the same order of steps for all subclasses
- easy to add a new variation by adding a subclass

**Disadvantages:**

- inheritance is rigid — composition is often more flexible
- the base class can become hard to change if many subclasses depend on it

AQA example — common setup and teardown for all tests:

```java
public abstract class BaseTest {

    @BeforeEach
    final void baseSetUp() {
        setUpDriver();
        setUpTestData();
        // Template method — subclasses can add more, but order is fixed
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

    // Subclasses override only what is different
    protected abstract void setUpTestData();
    protected abstract void cleanUpTestData();

    private void takeScreenshotIfFailed() {
        // check test status and attach screenshot
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
        // driver and orderId are already prepared by BaseTest
    }
}
```

In modern JUnit 5, composition with `@ExtendWith` is often preferred over inheritance, but the idea is the same: one place defines the flow, tests define the details.

**When to use:** use Template Method when many tests share the same setup and teardown order. If only 2 tests share the flow, a simple helper method is enough. For new code, consider a JUnit Extension (Observer) instead of inheritance.

### Backdoor

**Definition:** Backdoor (also called Test Backdoor) sets up application state through a faster, non-UI channel instead of going through the UI.

**Goal:** make tests faster and more stable by skipping slow UI steps that are not the focus of the test.

**How it works:** the test calls an API, database, or JavaScript injection to prepare state, then opens or refreshes the UI. The UI sees the same state as if the user had clicked through it.

**Advantages:**

- much faster — one API call replaces many clicks
- more stable — API has no waits or locators
- isolates the tested feature — a failure in login UI does not break all other tests

**Disadvantages:**

- the bypassed UI flow is not tested — you still need a few real UI login tests
- the backdoor may use internal APIs that change

Example — every UI test needs a logged-in user. Do not click the login form 50 times:

Bad — slow UI login in every test:

```java
@Test
void userCanCheckout() {
    driver.get("/login");
    driver.findElement(By.id("email")).sendKeys("user@example.com");
    driver.findElement(By.id("password")).sendKeys("123456");
    driver.findElement(By.id("loginBtn")).click(); // 5 seconds, flaky
    // real test starts here
}
```

Good — API backdoor, then inject state into the browser:

```java
public final class SessionHelper {

    private SessionHelper() {
    }

    public static void loginViaApiAs(WebDriver driver, String username, String password) {
        String token = new AuthService(Config.getBaseUrl()).login(username, password);
        driver.get(Config.getBaseUrl()); // open domain first to set storage
        ((JavascriptExecutor) driver).executeScript(
            "window.localStorage.setItem('accessToken', arguments[0])", token);
        // For cookie-based auth:
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
    // Already logged in — test the checkout, not the login
    new HomePage(DriverManager.get()).openCart();
}
```

Keep a few real UI login tests to verify the login page itself. Use the backdoor for all other tests.

**When to use:** use Backdoor whenever the setup steps are not the focus of the test: login, creating test data, or setting permissions. Always have at least one real end-to-end test that goes through the UI for the bypassed flow.

> [!tip] Template Method vs Backdoor
> | Point | Template Method | Backdoor |
> |---|---|---|
> | Solves | Same flow in many tests | Slow UI setup |
> | How | Base class defines order | Faster channel (API/DB/JS) prepares state |
> | Combine | BaseTest can call SessionHelper.loginViaApi | Yes — common pattern |

---

## Page Object Family

These three patterns work together. They are the most important patterns for UI tests and are required for Skills Matrix question 1 and for note [[20 Automation Framework Architecture]].

### Page Object

**Definition:** Page Object represents one page or one large area of the UI as a class. It holds locators and page actions.

**Goal:** keep locators and low-level Selenium calls in one place, so tests are readable and UI changes affect one class.

**How it works:** each page has private `By` locators and public methods that describe user actions. The test calls the method, the page does the clicks and waits. The test never calls `driver.findElement` directly.

**Advantages:**

- less duplication — one locator, one place to fix
- tests read as business steps, not Selenium calls
- easier to maintain when UI changes

**Disadvantages:**

- bad page objects become huge — split them into components
- page objects should not contain business assertions — they return state, the test asserts

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

For the case "test the login page", use the Page Object. For all other tests, use the Backdoor from the previous section to skip this page.

### Component Object

**Definition:** Component Object represents a reusable part of the UI: header, table, modal, or date picker.

**Goal:** avoid duplication when the same UI block appears on many pages.

**How it works:** the component has a root locator and finds its inner elements relative to the root. Pages are built from components via composition.

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

**When to use:** use Component Object when the same block appears on 2 or more pages or when a page is too large. If a block is used only once, keep it inside the Page Object.

### Element Wrapper

**Definition:** Element Wrapper is a small object around a single `WebElement` that adds waiting, logging, or domain meaning.

**Goal:** give common elements (Button, Input, Table) a clear API and add waiting in one place.

**How it works:** the wrapper holds a `By` or `WebElement` and provides methods like `safeClick()` or `type()` that include waits and logging before delegating to Selenium.

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

Page Objects use wrappers instead of raw `By`:

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

**When to use:** use Element Wrapper when many tests need the same waiting or logging logic for basic controls. Do not create a wrapper with 100 methods — give it only methods that are actually reused.

> [!info] Page Object Family Together
> Element Wrapper → Component Object → Page Object. Wrappers handle single controls, components handle blocks, pages compose components. This is Composite in GoF terms, but for AQA call it Page Object Family.

---

## Choosing a Pattern

Choose a pattern from the problem, not from the class name you want to create.

| Problem | Suitable pattern | AQA example | Simpler option |
|---|---|---|---|
| Client code should not know the concrete class | Factory | Create `ChromeDriver` or `FirefoxDriver` | Direct constructor for one implementation |
| Object has many optional fields and useful defaults | Builder | Create test users or request specifications | Constructor for a small value object |
| One behavior must be replaceable at runtime | Strategy | Select authentication or retry logic | Small `if` for two stable cases |
| One instance must be shared globally | Singleton | Config, cached token | Static factory method |
| Mutable state must be isolated per thread | ThreadLocal | WebDriver, per-thread token | Pass the object as a parameter |
| A complex workflow should be one readable call | Facade / Service Object | `AuthService.loginAsAdmin()`, `OrderService.createOrder()` | Direct API calls for one simple request |
| Cross-cutting behavior for many requests | Decorator / Filter | Auth header, logging, Allure | Code in each test for one case |
| Access to an object must be controlled or lazy | Proxy | Waiting element, logging element | Direct element call |
| Two incompatible interfaces must work together | Adapter | Allure vs ReportPortal reporter | Direct calls if one system only |
| React to test events without coupling | Observer / Listener | Screenshot on failure, step logging | Code in each test |
| Many tests share the same setup order | Template Method | BaseTest setUp and tearDown | Helper method for 1–2 tests |
| UI setup is slow and not the test focus | Backdoor | `loginViaApi` for UI tests | Real UI steps for the login feature itself |
| Locators and clicks are duplicated in tests | Page Object family | LoginPage, Header component, Button wrapper | Direct locators for a one-off check |

Ask these questions first:

1. What change is difficult today?
2. What duplication will the pattern remove?
3. Can a smaller method or class solve the same problem?
4. Will another developer understand the abstraction without a long explanation?
5. Can each component be tested independently?

---

## Combining Patterns in an AQA Framework

Patterns solve different problems and can work together. A real framework uses several at once:

1. **Factory** selects and creates the required `WebDriver`.
2. **Singleton + ThreadLocal** provides config and isolates driver and token per thread.
3. **Builder** creates test data and reusable `RequestSpecification`.
4. **Strategy** selects authentication or retry behavior.
5. **Facade / Service Object** hides API details behind business methods.
6. **Decorator / Filter** adds auth header and logging to every request.
7. **Observer / Listener** takes screenshots on failure and logs steps.
8. **Template Method + Backdoor** prepares fast, consistent test state.
9. **Page Object family** hides UI details from tests.

Example framework mapping:

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

Example flow — UI test for checkout:

```java
@ExtendWith(ScreenshotExtension.class) // Observer — screenshot on failure
class CheckoutTest extends BaseTest {  // Template Method — common setup

    @BeforeEach
    void setUpUser() {
        // Backdoor — fast API login instead of UI clicks
        SessionHelper.loginViaApiAs(DriverManager.get(), "user@example.com", "123456");
    }

    @Test
    void userCanCheckout() {
        // Page Object family — no locators in test
        new HomePage(DriverManager.get())
            .header().openCart();
        // Service Object + Reusable Spec — API part with no auth duplication
        Order order = new OrderService(ApiSpecs.forRole(Role.USER))
            .createOrder(new Product("iPhone"));
        assertThat(order.status()).isEqualTo("CREATED");
    }
}
```

This separation supports the framework principles from [[20 Automation Framework Architecture]] and the design principles from [[23 Software Engineering Practices for AQA]].

> [!info] Patterns Have Different Responsibilities
> Factory answers "which object should be created?", Builder answers "how should a complex object be assembled?", Strategy answers "which behavior should be used?", Facade answers "how to make a complex workflow simple?", Decorator answers "how to add behavior without changing the object?", Proxy answers "how to control access?", Adapter answers "how to connect incompatible interfaces?", Observer answers "how to react to events?", Template Method answers "how to reuse a common flow?", and Page Object answers "how to hide UI details?".

---

## Common Mistakes

| Mistake | Why it is a problem | Better approach |
|---|---|---|
| Adding a pattern before a real need appears | The code becomes harder without removing a problem | Start simple and refactor when change or duplication appears |
| Calling every creation helper a Factory Method | It confuses Simple Factory with the GoF pattern | Name the exact pattern used |
| Putting setup, logging, waits, and cleanup into one factory | The factory gets too many responsibilities | Keep the factory focused on creation |
| Using a Builder for an object with two required fields | Extra code gives little value | Use a constructor or factory method |
| Hiding important scenario values in Builder defaults | The test becomes difficult to understand | Show values that define the scenario |
| Sharing one mutable Builder between tests | Parallel tests can affect one another | Create a new builder per object or test |
| Creating Strategy classes for one unchanging algorithm | Many classes add no flexibility | Use one direct implementation |
| Keeping a large `switch` inside the Strategy context | Behavior is still not separated | Move each algorithm into its strategy class |
| Storing one global static `WebDriver` | Tests share browser state and break in parallel runs | Use ThreadLocal driver or test-scoped driver |
| Making a Singleton mutable and shared between threads | Tests affect each other through hidden global state | Make Singleton immutable or use ThreadLocal for mutable state |
| Making a Facade that hides 10 unrelated workflows | The facade becomes a god object | Split by domain — AuthService, OrderService |
| Adding Decorator wrappers in random order | Logging or retry may happen at the wrong time | Document and test the wrapping order |
| Wrapping every element in a Proxy without need | Indirection makes debugging harder | Wrap only elements that need waiting or logging |
| Writing an Adapter that hides different semantics | Two systems do different things, not just different names | Verify that the adapted systems truly share the same contract |
| Adding many Listeners that depend on order | A failing listener can hide the real test failure | Keep listeners small, independent, and tested separately |
| Using inheritance for everything (Template Method overuse) | Base class becomes rigid and hard to change | Prefer composition with JUnit Extensions for new code |
| Using Backdoor for the feature you are testing | The bypassed UI flow is not tested at all | Keep a few real UI tests for the bypassed flow |
| Making Page Objects huge with 50 locators and methods | Hard to find and maintain | Split into Component Objects and Element Wrappers |
| Putting assertions deep inside Page Objects | Tests hide their purpose | Keep assertions in tests or a dedicated verification layer |
| Combining patterns without clear boundaries | Responsibilities become hidden across many layers | Give each pattern one specific problem |

---

## Key Takeaways

> [!summary] Key points in 30 seconds
> - A design pattern is a reusable design idea for a common problem — it makes a framework easier to extend, test, and maintain, but add it only when real change or duplication exists
> - Three categories: Creational (Factory, Builder, Singleton — how objects are created), Structural (Facade, Decorator, Proxy, Adapter — how objects are connected), Behavioral (Strategy, Observer, Template Method — how behavior can change)
> - Factory centralizes object creation — `BrowserFactory` returns the `WebDriver` interface, so tests do not depend on a concrete browser
> - Builder assembles objects step by step with `build()` — good for test data with defaults and for a reusable `RequestSpecification` that keeps the `Authorization` header in one place
> - Strategy puts replaceable behavior (auth methods, retry policies) behind one interface instead of a growing `if` block
> - Singleton fits read-only shared resources like config or a cached token; one static `WebDriver` breaks parallel tests — ThreadLocal gives each thread its own driver
> - Facade and Service Object hide complex flows behind business methods; the Page Object family keeps locators and UI details out of tests
> - Most often asked: Singleton vs ThreadLocal, Builder vs constructor, Decorator vs Proxy, when a pattern is worth adding

---

## Interview Questions

### Beginner Questions

**1. What is a design pattern?**

A design pattern is a reusable design idea for a common software problem. It is not finished code that must always be copied exactly.

**2. What are the three main pattern categories?**

Creational patterns control object creation, structural patterns connect objects, and behavioral patterns organize behavior and communication.

**3. What problem does Factory solve?**

Factory centralizes object creation and hides concrete implementations from client code.

**4. What problem does Builder solve?**

Builder creates complex objects step by step, especially when they have optional values or useful defaults.

**5. What problem does Strategy solve?**

Strategy makes behavior replaceable through a common interface and allows it to be selected at runtime.

**6. What problem does Singleton solve?**

Singleton ensures one shared instance for a resource that is expensive or should be read once, such as config or a cached token.

**7. What problem does Facade solve?**

Facade provides one simple method for a complex workflow, such as a full login flow.

**8. What problem does Page Object solve?**

Page Object keeps locators and page actions in one place, so tests are readable and UI changes affect one class.

### Intermediate Questions

**1. Why should `BrowserFactory` return `WebDriver` instead of `ChromeDriver`?**

Returning the interface keeps client code independent from one browser implementation.

**2. What is the difference between Simple Factory and Factory Method?**

Simple Factory usually selects an implementation in one method. Factory Method lets subclasses override a creation method.

**3. When is Builder better than a constructor?**

Builder is better when an object has many optional values, defaults, or validation rules. A constructor is simpler for a few required values.

**4. How does Strategy support the Open/Closed Principle?**

A new behavior can be added as another strategy implementation without changing the context class.

**5. Can Factory, Builder, and Strategy be used together?**

Yes. They solve different problems: Factory creates an implementation, Builder assembles data, and Strategy supplies replaceable behavior.

**6. Why is `ThreadLocal<WebDriver>` needed for parallel tests?**

A single static driver is shared by all threads. ThreadLocal gives each thread its own driver, so tests do not interfere with each other.

**7. What is the difference between Decorator and Proxy?**

Decorator adds new behavior to an object, while Proxy controls access to an object (lazy loading, waiting, or access control).

**8. What is a Backdoor in test automation?**

Backdoor sets up state through a faster channel (API, DB, or localStorage) instead of clicking through the UI. It makes tests faster but the real UI flow still needs a few dedicated tests.

**9. Why should tests use `ApiSpecs.adminSpec()` instead of adding the header in every test?**

The specification with filters adds the auth header once. If the header or token logic changes, only one place needs to be updated, and parallel-safe token storage is handled in one place.

### Advanced Questions

**1. Why is a large Factory dangerous?**

It may collect unrelated setup, configuration, logging, and lifecycle logic. This breaks the Single Responsibility Principle and makes changes risky.

**2. Why can Builder defaults make tests weaker?**

Important scenario data may become invisible. A reader may not know which values affect the result.

**3. When is a conditional better than Strategy?**

A small stable conditional is better when there are only one or two simple cases and no expected growth.

**4. Why is one static `WebDriver` unsafe for parallel tests?**

Tests share mutable browser state. One test can navigate, close, or change the same driver while another test uses it.

**5. How do you decide whether to introduce a pattern?**

Identify a real change or duplication problem, compare the pattern with a simpler solution, and check whether the abstraction makes the code easier to understand and test.

**6. When would you choose Adapter over direct calls?**

When the framework must support two systems with different APIs but the same role, such as two reporting systems or two message brokers.

**7. When does Observer/Listener become risky?**

When listeners depend on order, do heavy work, or hide failures. Listeners should be small, independent, and not affect the test result beyond their purpose (for example, taking a screenshot).

**8. Why can a Singleton be dangerous in tests?**

A mutable Singleton is global state. Tests can change it and affect other tests. Make Singletons immutable or reset them explicitly, and use ThreadLocal for per-thread mutable state.

**9. How do you prevent Page Objects from becoming too large?**

Split the page into Component Objects and Element Wrappers. Each has one responsibility and is reused across pages.

### Code Questions

**1. Which implementation is returned?**

```java
WebDriver driver = BrowserFactory.create(Browser.FIREFOX);
```

**Answer:** The Simple Factory returns a `FirefoxDriver` object through the `WebDriver` interface.

**2. What values does `admin` contain?**

```java
UserData admin = new UserDataBuilder()
    .withName("Ana")
    .withEmail("ana@example.com")
    .withRole("ADMIN")
    .build();
```

**Answer:** It contains name `Ana`, email `ana@example.com`, role `ADMIN`, and the default `active` value `true`.

**3. What changes when `BasicAuthStrategy` is replaced with `BearerTokenStrategy`?**

```java
AuthStrategy auth = new BearerTokenStrategy(token);
ApiClient client = new ApiClient(baseUri, auth);
```

**Answer:** The authentication behavior changes, but `ApiClient` and its request methods do not need to change.

**4. Why is this design unsafe for parallel tests?**

```java
public final class DriverStore {
    public static WebDriver driver;
}
```

**Answer:** All tests share one mutable driver. Parallel tests can overwrite, navigate, or close the same browser session. Use a ThreadLocal driver.

**5. What is wrong with this Singleton for parallel tests?**

```java
public final class TokenHolder {
    public static String token;
}
```

**Answer:** All threads share one mutable string. Thread A can overwrite the token while Thread B is using it. Use ThreadLocal or pass the token as a parameter.

**6. What does `AuthFilter` do for every request?**

```java
RequestSpecification spec = new RequestSpecBuilder()
    .addFilter(new AuthFilter(TokenManager::getAdminToken))
    .build();

given().spec(spec)
.when().get("/api/orders");
```

**Answer:** The filter adds the `Authorization` header with the current token before every request. The test never writes the header directly.

**7. Why is this Backdoor useful?**

```java
SessionHelper.loginViaApiAs(driver, "user@example.com", "123456");
```

**Answer:** It logs in through the API and injects the token into the browser storage. The UI test starts already authenticated and saves about 5 seconds per test compared to clicking the login form.

**8. Which pattern does `ScreenshotExtension implements TestWatcher` use?**

**Answer:** Observer / Listener. The framework notifies the extension on test events. The test does not call the extension directly.

