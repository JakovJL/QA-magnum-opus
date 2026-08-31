# Mockito

## Table of Contents

- [[#What is Mockito]]
- [[#Mock and Stub]]
- [[#Core Annotations]]
- [[#Interaction Verification]]
- [[#ArgumentCaptor]]
- [[#Common Mistakes]]
- [[#Key Takeaways]]
- [[#Interview Questions]]
	- [[#Beginner Questions]]
	- [[#Intermediate Questions]]
	- [[#Advanced Questions]]
	- [[#Code Questions]]
	- [[#Practical Questions]]

**Related notes:** [[AQA Java eng]]

---

## What is Mockito

**Mockito** is a Java unit-test library. It creates managed test objects instead of real dependencies: databases, HTTP clients, email services, or repositories.

**Goal:** verify the logic of a single class in isolation.

Mockito does not replace JUnit 5: JUnit runs tests and provides assertions, while Mockito creates mock objects and verifies interactions.

---

## Mock and Stub

**Mock** — a replaced object whose calls can be configured and verified.

**Stub** — predefined behavior of a mock object: what result to return or what exception to throw.

```java
UserRepository repository = mock(UserRepository.class);
when(repository.findById(42L)).thenReturn(Optional.of(user));

User result = service.getUser(42L);

assertEquals("Ana", result.getName());
```

For `void` methods, a different syntax is used:

```java
doThrow(new IllegalStateException())
    .when(emailSender).send(any());
```

---

## Core Annotations

When working with JUnit 5, `MockitoExtension` is registered.

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    @Mock
    private UserRepository repository;

    @InjectMocks
    private UserService service;
}
```

- `@Mock` creates a mock object;
- `@InjectMocks` creates the tested object and injects matching mocks into it;
- `@Spy` creates an object with real behavior that can be partially overridden.

> [!caution] `@InjectMocks` does not replace understanding dependencies
> Use an explicit constructor in production code instead. Then it's clear what the class needs to work.

---

## Interaction Verification

`verify()` checks that the mock was called as expected.

```java
service.deleteUser(42L);

verify(repository).deleteById(42L);
verify(auditService).record("USER_DELETED", 42L);
```

The number of invocations can also be verified:

```java
verify(repository, times(1)).findById(42L);
verify(emailSender, never()).send(any());
```

Only verify interactions that matter for behavior. A test that verifies every internal step becomes brittle during refactoring.

---

## ArgumentCaptor

**ArgumentCaptor** allows reading the argument with which a mock was called.

```java
ArgumentCaptor<User> captor = ArgumentCaptor.forClass(User.class);

service.register("ana@example.com");

verify(repository).save(captor.capture());
assertEquals("ana@example.com", captor.getValue().getEmail());
```

Use it when you need to verify an object constructed inside the tested class.

---

## Common Mistakes

| Mistake | Better approach |
|---|---|
| Mocking the class under test | Create a real instance of the class |
| Verifying every internal call | Verify the result and key side effects |
| Returning unrealistic data | Use data close to real scenarios |
| Using `@Spy` unnecessarily | Prefer a plain object or `@Mock` |

---

## Key Takeaways

> [!summary] Key points in 30 seconds
> - Mockito is a Java unit-test library that creates managed test objects instead of real dependencies — goal: verify the logic of a single class in isolation
> - Mock — a replaced object whose calls can be configured and verified; stub — predefined behavior: what result to return or what exception to throw
> - `@Mock` creates a mock object, `@InjectMocks` creates the tested object and injects matching mocks into it, `@Spy` keeps real behavior that can be partially overridden
> - `when().thenReturn()` configures the return value; for `void` methods use `doThrow().when()`
> - `verify()` checks that the mock was called as expected — with the expected arguments and count (`times()`, `never()`)
> - ArgumentCaptor reads the argument a mock was called with — useful for objects constructed inside the tested class
> - Pitfalls: do not mock the class under test, and verify only interactions that matter — verifying every internal step makes tests brittle during refactoring
> - Most often asked: mock vs stub, `@Mock` vs `@InjectMocks`, `verify()` and ArgumentCaptor, mocking `void` methods with `doThrow()`

---

## Interview Questions

### Beginner Questions

**1. What is Mockito?**

Mockito is a Java unit-test library that creates managed test objects (mocks) instead of real dependencies.

**2. What is the difference between Mock and Stub in Mockito?**

A Mock is a replaced object whose calls can be configured and verified. A Stub is predefined behavior of a mock object — what result to return or what exception to throw.

**3. What does the @Mock annotation do?**

Creates a mock object for the annotated field.

**4. What does the @InjectMocks annotation do?**

Creates the tested object and automatically injects matching mock objects into it.

### Intermediate Questions

**1. What does the verify() method check?**

verify() checks that the mock was called as expected — with the expected arguments and the expected number of times.

**2. How do you verify that a mock method was never called?**

```java
verify(emailSender, never()).send(any());
```

**3. When should you use ArgumentCaptor?**

When you need to check an object that was constructed inside the tested class and passed to a mock.

**4. When do you use doThrow() instead of when().thenThrow()?**

For void methods, because when() requires a return value.

### Advanced Questions

**1. Why should you avoid verifying every internal interaction of a mock?**

The test becomes brittle during refactoring: an internal implementation change breaks the test even when the external behavior is correct. You should verify the result and key side effects only.

**2. Why is it dangerous to mock the class under test?**

It tests a stub of the class, not the real one. The test passes but the actual class logic is never executed. You should create a real instance of the class under test.

**3. What problem can arise when using @InjectMocks with an implicit constructor?**

If the class dependencies change, @InjectMocks may inject the wrong objects or inject nothing. An explicit constructor in production code is preferred.

### Code Questions

**1. What does this code do?**

```java
when(repository.findById(42L)).thenReturn(Optional.of(user));
```

**Answer:** Configures the mock to return `Optional.of(user)` when `findById(42L)` is called.

**2. What is the mistake in this code?**

```java
@Mock
private UserService service;
```

**Answer:** The class under test (UserService) is being mocked instead of tested as a real object. Only the dependencies should be mocked.

### Practical Questions

**1. How do you mock a repository in a unit test?**

```java
@Mock
private UserRepository repository;
```

Then use `when(repository.findById(42L)).thenReturn(...)` to define what it returns.

**2. How do you verify that a method was called with specific arguments?**

```java
verify(repository).findById(42L);
```

**3. How do you test exception handling from a dependency?**

```java
when(repository.findById(anyLong())).thenThrow(new DataNotFoundException());
```

**4. How do you check that a method was never called?**

```java
verify(repository, never()).deleteById(anyLong());
```

**5. How do you capture and inspect a complex argument passed to a mock?**

Use ArgumentCaptor:

```java
ArgumentCaptor<User> captor = ArgumentCaptor.forClass(User.class);
verify(repository).save(captor.capture());
assertEquals("ana@example.com", captor.getValue().getEmail());
```

**6. How do you mock a void method to throw an exception?**

```java
doThrow(new IllegalStateException()).when(emailSender).send(any());
```

**7. How do you mock the same method to return different values on consecutive calls?**

```java
when(repository.findById(1L)).thenReturn(Optional.of(user1), Optional.of(user2));
```

The first call returns user1, the second call returns user2.

**8. How do you check that a mock was called exactly the right number of times?**

```java
verify(repository, times(3)).findById(anyLong());
verify(repository, atLeastOnce()).save(any());
verify(repository, atMost(2)).deleteById(anyLong());
```

**9. How do you test retry logic with Mockito?**

```java
when(repository.findById(anyLong()))
    .thenThrow(new TimeoutException())
    .thenReturn(Optional.of(user));
```

This simulates a failure on the first call and success on retry.

**10. How do you reset a mock between tests?**

```java
reset(repository);
```

Or better, create a fresh mock in `@BeforeEach` so state does not leak between tests.
