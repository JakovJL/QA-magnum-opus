# Mockito

## Содержание

- [[#Что такое Mockito]]
- [[#Mock и Stub]]
- [[#Основные аннотации]]
- [[#Проверка взаимодействий]]
- [[#ArgumentCaptor]]
- [[#Типичные ошибки]]
- [[#Вопросы на собеседовании]]
	- [[#Вопросы начального уровня]]
	- [[#Вопросы среднего уровня]]
	- [[#Вопросы повышенного уровня]]
	- [[#Вопросы с кодом]]
	- [[#Практические вопросы]]

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

---

## Вопросы на собеседовании

### Вопросы начального уровня

**1. Что такое Mockito?**

Mockito — библиотека для unit-тестов Java, которая создаёт управляемые тестовые объекты (mocks) вместо настоящих зависимостей.

**2. Чем отличается Mock от Stub в Mockito?**

Mock — подменённый объект для проверки вызовов. Stub — заранее настроенное поведение mock-объекта (какой результат вернуть или исключение выбросить).

**3. Что делает аннотация @Mock?**

Создаёт mock-объект для указанного поля.

**4. Что делает аннотация @InjectMocks?**

Создаёт тестируемый объект и автоматически передаёт в него подходящие mock-объекты.

### Вопросы среднего уровня

**1. Что проверяет метод verify()?**

verify() проверяет, что mock был вызван ожидаемым образом — с ожидаемыми аргументами и нужное количество раз.

**2. Как проверить, что метод mock-объекта не был вызван?**

```java
verify(emailSender, never()).send(any());
```

**3. Когда нужно использовать ArgumentCaptor?**

Когда важно проверить объект, который сформирован внутри тестируемого класса и передан в mock.

**4. В каком случае используют doThrow() вместо when().thenThrow()?**

Для void-методов, так как when() требует возвращаемое значение.

### Вопросы повышенного уровня

**1. Почему не стоит проверять каждый внутренний вызов mock-объекта?**

Тест становится хрупким при рефакторинге: изменение внутренней реализации ломает тест, хотя внешнее поведение остаётся верным. Стоит проверять результат и ключевые побочные эффекты.

**2. Почему опасно mock-ить сам тестируемый класс?**

Это тестирует не настоящий класс, а его заглушку. Тест проходит, но реальная логика класса не проверяется. Следует создавать настоящий объект тестируемого класса.

**3. Какая проблема может возникнуть при использовании @InjectMocks с неявным конструктором?**

Если зависимости класса изменились, @InjectMocks может подставить неправильные объекты или не подставить ничего. Лучше использовать явный конструктор в production-коде.

### Вопросы с кодом

**1. Что делает этот код?**

```java
when(repository.findById(42L)).thenReturn(Optional.of(user));
```

**Ответ:** Настраивает mock: при вызове `findById(42L)` вернуть `Optional.of(user)`.

**2. Какая ошибка допущена?**

```java
@Mock
private UserService service;
```

**Ответ:** Mock-ится сам тестируемый класс. UserService нужно создавать как настоящий объект, а mock-ить только его зависимости.

### Практические вопросы

**1. Как замокать репозиторий в юнит-тесте?**

```java
@Mock
private UserRepository repository;
```

Затем через `when(repository.findById(42L)).thenReturn(...)` задать поведение.

**2. Как проверить, что метод был вызван с конкретными аргументами?**

```java
verify(repository).findById(42L);
```

**3. Как протестировать обработку исключения от зависимости?**

```java
when(repository.findById(anyLong())).thenThrow(new DataNotFoundException());
```

**4. Как убедиться, что метод ни разу не вызывался?**

```java
verify(repository, never()).deleteById(anyLong());
```

**5. Как захватить и проверить сложный аргумент, переданный в mock?**

Использовать ArgumentCaptor:

```java
ArgumentCaptor<User> captor = ArgumentCaptor.forClass(User.class);
verify(repository).save(captor.capture());
assertEquals("ana@example.com", captor.getValue().getEmail());
```

**6. Как замокать void-метод, чтобы он выбросил исключение?**

```java
doThrow(new IllegalStateException()).when(emailSender).send(any());
```

**7. Как вернуть разные значения при последовательных вызовах одного метода?**

```java
when(repository.findById(1L)).thenReturn(Optional.of(user1), Optional.of(user2));
```

Первый вызов вернёт user1, второй — user2.

**8. Как проверить, что mock вызвали ровно нужное количество раз?**

```java
verify(repository, times(3)).findById(anyLong());
verify(repository, atLeastOnce()).save(any());
verify(repository, atMost(2)).deleteById(anyLong());
```

**9. Как протестировать retry-логику с Mockito?**

```java
when(repository.findById(anyLong()))
    .thenThrow(new TimeoutException())
    .thenReturn(Optional.of(user));
```

Имитируется ошибка при первом вызове и успех при повторном.

**10. Как сбросить состояние mock-объекта между тестами?**

```java
reset(repository);
```

Но лучше создавать свежий mock в `@BeforeEach`, чтобы состояние не перетекало между тестами.
