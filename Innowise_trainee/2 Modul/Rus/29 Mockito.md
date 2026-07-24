# Mockito

## Содержание

- [[#Что такое Mockito]]
- [[#Mock и Stub]]
- [[#Основные аннотации]]
- [[#Проверка взаимодействий]]
- [[#ArgumentCaptor]]
- [[#Типичные ошибки]]

**Связанные заметки:** [[AQA Java rus]]

---

## Что такое Mockito

**Mockito** — библиотека для unit-тестов Java. Она создаёт управляемые тестовые объекты вместо настоящих зависимостей: базы данных, HTTP-клиента, почтового сервиса или репозитория.

**Цель:** изолированно проверить логику одного класса.

Mockito не заменяет JUnit 5: JUnit запускает тесты и предоставляет assertions, а Mockito создаёт mock-объекты и проверяет вызовы.

---

## Mock и Stub

**Mock** — подменённый объект, вызовы которого можно настроить и проверить.

**Stub** — заранее настроенное поведение mock-объекта: какой результат вернуть или какое исключение выбросить.

```java
UserRepository repository = mock(UserRepository.class);
when(repository.findById(42L)).thenReturn(Optional.of(user));

User result = service.getUser(42L);

assertEquals("Ana", result.getName());
```

Для `void`-методов используется другой синтаксис:

```java
doThrow(new IllegalStateException())
    .when(emailSender).send(any());
```

---

## Основные аннотации

При работе с JUnit 5 подключают `MockitoExtension`.

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    @Mock
    private UserRepository repository;

    @InjectMocks
    private UserService service;
}
```

- `@Mock` создаёт mock-объект;
- `@InjectMocks` создаёт тестируемый объект и передаёт в него подходящие mocks;
- `@Spy` создаёт объект с настоящим поведением, которое можно частично подменить.

> [!caution] `@InjectMocks` не заменяет понимание зависимостей
> Лучше использовать явный конструктор в production-коде. Тогда видно, что именно нужно классу для работы.

---

## Проверка взаимодействий

`verify()` проверяет, что mock был вызван ожидаемым образом.

```java
service.deleteUser(42L);

verify(repository).deleteById(42L);
verify(auditService).record("USER_DELETED", 42L);
```

Можно проверить количество вызовов:

```java
verify(repository, times(1)).findById(42L);
verify(emailSender, never()).send(any());
```

Проверяй только взаимодействия, важные для поведения. Тест, который проверяет все внутренние шаги, становится хрупким при рефакторинге.

---

## ArgumentCaptor

**ArgumentCaptor** позволяет прочитать аргумент, с которым был вызван mock.

```java
ArgumentCaptor<User> captor = ArgumentCaptor.forClass(User.class);

service.register("ana@example.com");

verify(repository).save(captor.capture());
assertEquals("ana@example.com", captor.getValue().getEmail());
```

Используй его, когда важно проверить объект, сформированный внутри тестируемого класса.

---

## Типичные ошибки

| Ошибка | Как сделать лучше |
|---|---|
| Mock-ить сам тестируемый класс | Создавать настоящий объект класса |
| Проверять каждый внутренний вызов | Проверять результат и ключевые побочные эффекты |
| Возвращать нереалистичные данные | Использовать данные, близкие к настоящим сценариям |
| Использовать `@Spy` без необходимости | Предпочитать обычный объект или `@Mock` |
