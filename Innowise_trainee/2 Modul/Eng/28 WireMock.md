# WireMock

## Table of Contents

- [[#What is WireMock]]
- [[#When It Is Needed]]
- [[#Stub and Request Verification]]
- [[#Running in a Test]]
- [[#Error Scenarios]]
- [[#Common Mistakes]]
- [[#Interview Questions]]
	- [[#Beginner Questions]]
	- [[#Intermediate Questions]]
	- [[#Advanced Questions]]
	- [[#Code Questions]]

**Related notes:** [[AQA Java eng]]

---

## What is WireMock

**WireMock** is a library that starts a test HTTP server and returns predefined responses. Such a server is called a stub.

**Goal:** test a client or service without the real external system.

WireMock is useful when the external API is paid, unstable, unavailable in the test environment, or too slow.

---

## When It Is Needed

Use WireMock when you need to check how the application handles:

- a successful response from an external API;
- a `404`, `429`, or `500` error;
- a slow response or timeout;
- specific headers, parameters, and request body;
- retry attempts.

WireMock does not verify the real contract of the external provider. Separate integration or contract tests are needed for that.

---

## Stub and Request Verification

A stub defines a request condition and the response the test server will return.

```java
stubFor(get(urlEqualTo("/users/42"))
    .willReturn(aResponse()
        .withStatus(200)
        .withHeader("Content-Type", "application/json")
        .withBody("{\"id\":42,\"name\":\"Ana\"}")));
```

After the application action, it is useful to verify that the request was actually sent:

```java
verify(getRequestedFor(urlEqualTo("/users/42")));
```

Also check important headers, query parameters, and the body. Otherwise the test may pass even though the application is sending an incorrect request.

---

## Running in a Test

In JUnit 5 the server is usually started before the test and stopped after it. The WireMock address is passed to the tested client's configuration instead of the real API address.

```java
WireMockServer server = new WireMockServer(options().dynamicPort());
server.start();
String baseUrl = server.baseUrl();
```

A dynamic port helps avoid conflicts, especially when running tests in parallel.

---

## Error Scenarios

A good test suite includes more than just a successful response:

- `401` or `403` — the application should report missing access;
- `404` — handle a missing resource correctly;
- `429` — apply a retry or show a clear error message;
- `500` — do not expose technical details to the user;
- response delay — check whether the configured timeout fired.

---

## Common Mistakes

| Mistake | Better approach |
|---|---|
| The stub responds to any request | Constrain the method, URL, and important parameters |
| Only checking the method result | Also call `verify()` for the request |
| Using WireMock instead of integration tests | Keep separate tests with the real system |
| Leaving the server running | Stop it in `@AfterEach` or `@AfterAll` |

---

## Interview Questions

### Beginner Questions

**1. What is WireMock?**

WireMock is a library that starts a test HTTP server and returns predefined responses. This server is called a stub.

**2. What is a stub in WireMock?**

A stub is a rule that matches an incoming request condition and returns a predefined response.

**3. How do you verify that the application sent the expected request?**

Use `verify(getRequestedFor(urlEqualTo("...")))` after the application action.

**4. What is the purpose of WireMock?**

To test how a client or service behaves when interacting with an external API, without using the real system.

### Intermediate Questions

**1. Why use a dynamic port for WireMock?**

A dynamic port avoids port conflicts, which is especially important when running tests in parallel.

**2. When is WireMock not appropriate?**

WireMock does not verify the real contract of the external provider. It cannot replace separate integration or contract tests that run against the actual system.

**3. What error scenarios should a good test suite include?**

401/403 (access denied), 404 (not found), 429 (rate limit / retry), 500 (server error), and response delays (timeout handling).

**4. What can you verify besides whether the request was sent?**

Headers, query parameters, and the request body. Without these checks a test may pass even though the application sends wrong data.

### Advanced Questions

**1. Why is checking only the method result insufficient when using WireMock?**

The method may return a correct value even if the outgoing request had wrong headers, parameters, or body. Calling `verify()` confirms the exact request that the application sent.

**2. What is the risk of a stub that matches any request?**

It can silently mask incorrect requests from the application, making the test pass when the client sends a wrong URL or method. Stubs should be constrained by method, URL, and important parameters.

**3. Why should WireMock not replace integration tests?**

WireMock simulates the API, but it does not validate the real contract. The real system may behave differently — different status codes, response formats, or error handling. Integration tests with the actual API catch those differences.

### Code Questions

**1. What does this stub configure?**

```java
stubFor(get(urlEqualTo("/users/42"))
    .willReturn(aResponse()
        .withStatus(200)
        .withHeader("Content-Type", "application/json")
        .withBody("{\"id\":42,\"name\":\"Ana\"}")));
```

**Answer:** It configures the WireMock server to respond to `GET /users/42` with status `200`, `Content-Type: application/json`, and the JSON body `{"id":42,"name":"Ana"}`.

**2. What does `verify()` check in this example?**

```java
verify(getRequestedFor(urlEqualTo("/users/42")));
```

**Answer:** It checks that the application sent exactly one `GET` request to `/users/42` during the test.

**3. What mistake does this test make?**

```java
stubFor(any(anyUrl())
    .willReturn(aResponse().withStatus(200)));

// ... application action ...

verify(postRequestedFor(urlEqualTo("/data")));
```

**Answer:** The stub matches any request (`any(anyUrl())`), so even if the application sends a wrong URL or method, it still gets a `200` response. The `verify()` call would fail, but the application may have made multiple or different requests before the expected one. Stubs should be constrained to the expected method and URL.
