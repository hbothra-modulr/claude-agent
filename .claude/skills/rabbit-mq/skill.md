# RabbitMQ Messaging Standards

This document defines the messaging architecture, naming conventions, operational rules, and best practices for RabbitMQ usage across all services. These standards must be followed by all teams when creating producers, consumers, queues, exchanges, and routing keys.

---

# 1. Exchange Strategy

## Mandatory Rule
All new queues must **stop using `modulo-exchange`**.

Each **producer service owns its exchanges**.

Producer services must reuse the same:

- Direct exchange
- Dead letter exchange

Additionally:

- A **new fanout exchange** must be created per **business event**.
- A **new topic exchange** must be created per **topic name**.

### Business Event Definition
A business event may represent:

- A **single event** (example: 3DS secure request)
- A **group of logically connected events** (example: payment status updates)

---

# 2. Exchange Types

RabbitMQ supports multiple exchange types. The system standardizes on the following:

- Direct Exchange
- Fanout Exchange
- Topic Exchange

---

# 2.1 Direct Exchange

### Purpose
Direct exchanges route messages to queues where the **binding key exactly matches the routing key**.

### Behaviour

Producer publishes message → Exchange checks routing key → Message delivered to queue with identical binding key.

### Characteristics

- Deterministic routing
- One specific consumer queue receives the message
- Ideal for targeted message delivery

---

# 2.2 Fanout Exchange

### Purpose
Fanout exchanges **broadcast messages**.

### Behaviour

- Routing keys are ignored.
- Message is sent to **all queues bound to the exchange**.

### Example

If **N queues** are bound to the fanout exchange:

- A producer publishes **1 message**
- RabbitMQ sends **1 copy to each of the N queues**

### Characteristics

- Broadcast style messaging
- Useful for distributing business events to multiple consumers

---

# 2.3 Topic Exchange

### Purpose
Topic exchanges route messages using **pattern matching on routing keys**.

### Behaviour

Routing is based on **patterns and wildcards** rather than exact matches.

Messages are delivered when:

```
routing key matches routing pattern
```

### Routing Key Format

Routing keys must be:

- Words separated by `.` (period)

Example

```
payment.status.updated
```

### Wildcards

- `*` → matches exactly one word
- `#` → matches zero or more words

Example Pattern

```
payment.status.*
```

### Consumer Behaviour

1. Consumer creates a queue
2. Consumer binds the queue to exchange with a **routing pattern**
3. Messages matching the pattern are delivered to the queue

Messages remain in the queue **until the consumer processes them**.

### Important Note

Using `.` in names is **discouraged with topic exchanges** because it interacts with wildcard characters (`*` and `#`).

---

# 3. Naming Conventions

Strict naming conventions must be followed for all exchanges, queues, and routing keys.

---

# 3.1 Exchange Naming

Exchange names must follow specific conventions depending on the **exchange type**.

Different exchange types have **different naming structures**.

---

## Direct Exchange

Format:

```
<producer-service>.dx
```

Purpose:

Used for direct routing where the routing key must exactly match the queue binding key.

Example

```
card.dx
payments.dx
```

---

## Dead Letter Exchange

Format:

```
<producer-service>.dlx
```

Purpose:

Handles messages that cannot be processed successfully and are routed to dead letter queues.

Example

```
card.dlx
payments.dlx
```

---

## Fanout Exchange

Format:

```
<producer-service>_<event-name>.fx
```

Purpose:

Broadcasts a business event to **all subscribed consumer queues**.

Rules:

- One fanout exchange per **business event**
- `<event-name>` must be **hyphenated** and descriptive

Example

```
account_customer-address-update.fx
payment_payment-status-update.fx
```

---

## Topic Exchange

Format:

```
<consumer-service>_<topic-name>.tx
```

Purpose:

Used when routing is based on **topic patterns and wildcards**.

Rules:

- One topic exchange per **topic domain**
- `<topic-name>` must be **hyphenated** and descriptive
- Routing patterns must follow topic routing rules

Example

```
reporting_payment-status.tx
analytics_transaction-updates.tx
```

---

## Exchange Type Suffix Reference

| Exchange Type | Suffix |
|---------------|-------|
| Direct Exchange | `.dx` |
| Dead Letter Exchange | `.dlx` |
| Fanout Exchange | `.fx` |
| Topic Exchange | `.tx` |

---

### Example Exchanges

```
card.dx
card.dlx
account_customer-address-update.fx
```

---

# 3.2 Queue Naming

Queue naming format:

```
<consumer-service>_<consumer-queue-purpose>
```

### Components

#### <consumer-service>

Consumer service name without the word "service".

#### <consumer-queue-purpose>

The **business operation performed when the message is received**.

---

### Dead Letter Queue Rule

Dead letter queues must end with:

```
.dlq
```

---

### Queue Examples

```
gps-provider_3d-secure-request-processing
bacs_mandate-creation.dlq
```

---

# 3.3 Routing Key Naming

Routing key format:

```
<consumer-service>_<consumer-queue-purpose>
```

### Components

#### <consumer-service>

Consumer service name (without "service").

#### <consumer-queue-purpose>

Business operation triggered by the message.

---

### Routing Key Examples

```
gps-provider_3d-secure-request-processing
bacs_mandate-creation
```

---

# 4. Idempotency

Consumers **should check whether a message has already been processed** before processing it again.

This is important because duplicate messages can occur.

### Why Duplicates Happen

Example scenario:

1. Consumer receives message
2. Consumer starts processing
3. Queue crashes or restarts
4. Broker does not receive ACK
5. Broker resends the message

Result:

The **same message may be processed twice**.

Consumers must therefore ensure processing is **idempotent**.

Example approaches:

- Database uniqueness constraints
- Processed message tracking
- Idempotency keys

Note: "should" is used because systems or database constraints may not always enforce this requirement.

---

# 5. Event Based Modelling

Further guidance to be provided.

---

# 6. Removing Queues

Follow the documented process for deleting existing resources.

Resources include:

- Queues
- Exchanges
- Bindings
- Virtual hosts
- Users

Reference documentation:

```
Managing Queues, Exchanges, Binding, virtual hosts and users | Deleting existing resources
```

---

# 7. Additional Rules and Best Practices

## Naming Restrictions

The following words must **NOT** appear in:

- exchange names
- routing keys
- queue names

Prohibited words:

```
event
notification
message
```

Reason:

These concepts are already implied in messaging systems.

---

## Service Naming Rules

- Do not include the word **"service"**.
- Do not abbreviate service names.

---

## Naming Style

Use **kebab-case**.

Example

```
customer-address-update
```

---

## Routing Key / Queue Overlap

There will often be overlap between:

- routing keys
- consumer queue names

This is expected when using **direct exchanges**.

---

## Identifier Naming

Identifiers must follow the **API coding standards**.

Use:

```
id
```

Instead of:

```
bid
```

---

## Queue Message Structure Rule

A queue must accept **only a single message structure**.

Implication:

```
1 Queue = 1 Message Structure
```

This enforces strong message contracts.

---

# 8. Message Processing Time Constraint

Each message must be processed within:

```
2 minutes or less
```

### Reason

Containerized pods are drained every **24 hours** with **2 minutes notice**.

### Prefetch Constraint

The following condition must be respected:

```
(prefetch count × time to process each message) <= 2 minutes
```

Otherwise messages may be interrupted during pod draining.

---

# 9. Class Naming Guidelines

Classes that publish or handle messages are usually named after the **trigger event**.

Examples

```
UserCreatedEvent
StatusChangeEvent
```

### Reason

The term **Message** is considered generic (similar to Object). More specific naming improves readability and intent.

Exceptions may exist depending on context.

---

# 10. Exchange and Routing Key Declaration in Code

Exchange names and routing keys must be defined as **string literals directly in the code that publishes the message**.

### Do NOT

Avoid placing them in configuration or properties classes.

### Reasons

- Easier to validate during code review
- Ensures correctness during unit testing
- Simplifies operational support
- Reduces configuration drift

---

# 11. Terminology

## Prefetch

Prefetch defines the:

```
Maximum number of unacknowledged messages allowed on a channel
```

Meaning:

The broker will not deliver additional messages once this limit is reached **until acknowledgements are received**.

This prevents consumers from being overloaded.

---

# Summary

These standards enforce:

- Domain ownership of exchanges
- Predictable routing
- Consistent naming
- Idempotent consumers
- Strong message contracts
- Safe container shutdown handling

All teams must follow this specification when implementing RabbitMQ messaging.


---

# 12. AI Agentic Enforcement Rules

The following rules define how AI agents, automation tools, or code‑generation systems must behave when interacting with this messaging standard. These rules ensure that automated systems consistently follow the architectural conventions defined in this document.

AI systems generating RabbitMQ related code, infrastructure configuration, or documentation **must always follow these rules**.

---

## 12.1 ALWAYS Rules

AI agents **must always**:

### Exchange Ownership

- Always create exchanges owned by the **producer service**.
- Always reuse the same **direct exchange (`<producer>.dx`)** for targeted routing.
- Always reuse the same **dead letter exchange (`<producer>.dlx`)** for dead letter routing.

### Fanout Exchanges

- Always create a **new fanout exchange per business event**.
- Always name fanout exchanges using:

```
<producer-service>_<event-name>.fx
```

### Topic Exchanges

- Always create a **new topic exchange per topic name**.
- Always ensure routing patterns follow topic routing rules.

### Naming Conventions

AI agents must always enforce:

- kebab-case naming
- full service names (no abbreviations)
- removal of the word "service" from names

### Exchange Naming

Always follow:

```
<producer-service>.dx
<producer-service>.dlx
<producer-service>_<event-name>.fx
<consumer-service>_<topic-name>
```

### Queue Naming

Always follow:

```
<consumer-service>_<consumer-queue-purpose>
```

### Dead Letter Queues

Always ensure:

```
.dlq
```

suffix is used for dead letter queues.

### Routing Keys

Always follow:

```
<consumer-service>_<consumer-queue-purpose>
```

### Message Contract

AI agents must always enforce:

```
1 Queue = 1 Message Structure
```

Queues must not accept multiple message structures.

### Idempotency

AI generated consumers must always:

- Check if a message has already been processed
- Implement idempotent message processing

Suggested approaches:

- idempotency keys
- database uniqueness constraints
- processed message tracking

### Message Processing Time

AI generated consumers must always ensure:

```
message processing time <= 2 minutes
```

And enforce:

```
(prefetch × processing time) <= 2 minutes
```

### Code Implementation

AI agents must always:

- Declare exchange names as **string literals in the code**
- Declare routing keys as **string literals in the code**

They must be defined **at the point of use**.

### Class Naming

AI generated classes must always use **event‑based naming** when appropriate.

Examples:

```
UserCreatedEvent
StatusChangeEvent
```

---

## 12.2 NEVER Rules

AI agents must **never** violate the following constraints.

### Exchange Usage

AI agents must never:

- Use `modulo-exchange` for new queues

### Naming Violations

AI agents must never include the following words in:

- exchange names
- queue names
- routing keys

Forbidden words:

```
event
notification
message
```

### Service Naming

AI agents must never:

- Include the word `service` in exchange, queue, or routing key names
- Abbreviate service names

### Queue Design

AI agents must never:

- Assign multiple message structures to a single queue

### Configuration Management

AI agents must never:

- Store exchange names in configuration files
- Store routing keys in properties classes

### Topic Exchange Misuse

AI agents must never:

- Use `.` in non-topic naming where it conflicts with wildcard routing
- Break topic routing pattern rules

### Processing Violations

AI agents must never generate implementations where:

```
(prefetch × processing time) > 2 minutes
```

### Messaging Architecture

AI agents must never:

- Create exchanges owned by the consumer service when they are meant to be owned by producers

---

# 13. AI Compliance Expectation

Any AI‑generated implementation must be validated against this document before merge or deployment.

AI generated outputs should be reviewed to ensure:

- Naming conventions are respected
- Exchange ownership is correct
- Queue structure follows the 1 message rule
- Idempotency is implemented
- Processing limits are respected

Failure to comply with these rules may lead to unstable messaging systems, duplicate processing, or operational failures.