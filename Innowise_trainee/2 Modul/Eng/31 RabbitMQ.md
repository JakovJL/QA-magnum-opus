# RabbitMQ

## Table of Contents

- [[#What is RabbitMQ]]
- [[#Main Parts]]
- [[#Message Routing]]
- [[#Acknowledgement and Redelivery]]
- [[#What to Check as a Tester]]
- [[#Typical Mistakes]]
- [[#Practical Questions]]

**Related notes:** [[AQA Java eng]]

---

## What is RabbitMQ

**RabbitMQ** is a message broker. It helps services exchange data asynchronously: the sender publishes a message, and the receiver processes it later.

**Goal:** reduce direct coupling between services and handle uneven load.

For example, an order service creates an order and sends an event. A notification service receives that event and sends an email independently from the first service.

---

## Main Parts

- **Producer** — sends a message;
- **Exchange** — receives the message and decides where to route it;
- **Queue** — stores the message until processing;
- **Consumer** — receives and processes the message;
- **Binding** — a rule that links an `exchange` to a `queue`.

```text
Producer → Exchange → Queue → Consumer
```

---

## Message Routing

The `exchange` type determines how RabbitMQ selects a queue:

- `direct` — by exact routing key;
- `topic` — by routing key pattern, e.g. `order.*`;
- `fanout` — sends a copy to every bound queue;
- `headers` — uses message headers.

Routing key is a string the producer passes when publishing. It must match the routing rules.

---

## Acknowledgement and Redelivery

After successful processing, the consumer acknowledges the message. If processing fails before acknowledgement, the broker may redeliver the message.

Therefore the handler must be **idempotent**: reprocessing the same message should not charge money twice, create a second order, or send an extra email.

A Dead Letter Queue (DLQ) is often configured for messages that cannot be processed.

---

## What to Check as a Tester

- whether the message is published with the correct body, headers, and routing key;
- whether it reaches the correct queue;
- whether the consumer processes it correctly;
- what happens on consumer failure;
- whether redelivery breaks data;
- whether an unprocessed message ends up in the DLQ;
- whether a trace exists by `correlation id`.

---

## Typical Mistakes

| Mistake | How to improve |
|---|---|
| Expecting instant results | Account for asynchrony, use conditional waits |
| Not testing redelivery | Test handler crashes and idempotency |
| Assuming message order is always guaranteed | Explicitly verify ordering requirements and consumer count |
| Ignoring DLQ | Test error scenarios and downstream processing |

---

### Practical Questions

**1. How do you check if a message was published to the correct exchange?**

Use RabbitMQ Management UI or command line: go to the Queues tab, bind to the exchange, and check the message count.

**2. How do you verify that a consumer processed a message correctly?**

Check the database or API state after the consumer runs. For example, if the consumer creates an order, query the orders collection.

**3. How do you test retry logic when message processing fails?**

Simulate a consumer crash or throw an exception in the handler. Check that the message appears in the queue again (requeued) or goes to the DLQ after the retry limit.

**4. How do you check that a bad message ends up in the DLQ?**

Send a message with invalid data. Verify that after all retry attempts the message appears in the configured DLQ.

**5. How do you test idempotency of message processing?**

Send the same message twice. Verify that the side effect (e.g., order creation, email sending) happens only once.

**6. How do you verify message headers and routing key?**

Use a test consumer or the Management UI to inspect the message properties before processing.

**7. How do you test that a message does NOT reach a queue?**

Publish a message with a routing key that should not match. Assert that the queue count stays the same.

**8. How do you test different exchange types?**

Use `direct` for exact match, `fanout` to broadcast to all queues, `topic` for pattern match. Publish a test message and verify which queue received it.

**9. How do you test multiple consumers on the same queue?**

Start two consumers on one queue. Publish several messages and assert that the load is distributed (each consumer gets a subset).

**10. How do you clean up queues and messages between tests?**

Use the Management HTTP API or the RabbitMQ Java client to purge queues in `@BeforeEach` / `@AfterEach`.
