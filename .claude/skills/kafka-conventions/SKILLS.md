---
name: kafka-convention
description: The Modulr Agentic Kafka Messaging Standard (MAKMS) defines how autonomous AI agents and distributed services interact through Kafka in a consistent, reliable, observable, and scalable manner.
---

# Modulr Agentic Kafka Messaging Standard (MAKMS)


This standard reframes traditional Kafka concepts into agent-oriented design principles. It establishes conventions for:

- Designing agent swarms (consumer groups)
- Defining shard identity and routing via message keys
- Ensuring delivery guarantees (at-least-once / exactly-once)
- Managing retries and Dead Letter Topics (DLTs)
- Preserving ordering and partition ownership
- Operating safely in high-throughput, fault-tolerant environments

The goal is to ensure all Modulr AI agents behave predictably within Kafka’s distributed log architecture while maintaining strong reliability guarantees and operational clarity.

---

# Kafka Design Conventions – Agentic Summary

This document defines how AI agents and services at Modulr should use Kafka in a consistent, reliable, and observable way. It reframes Kafka concepts and design rules around autonomous, message-driven agents.

---

## 1. Core Kafka Concepts for Agents

### Cluster
A set of cooperating Kafka brokers. Agents treat the cluster as a single messaging fabric.

### Broker
A server that stores and serves messages. Partitions may be replicated across brokers for fault tolerance. One broker is “active” leader per partition at any time.

### Topic
A logical channel to which agents publish and from which they consume messages.

Topics are **immutable streams** and are **created via Terraform** (Infrastructure as Code), not ad hoc.

### Partition
A topic is sharded into partitions. Agents can process messages from different partitions in parallel, increasing throughput while preserving per-partition ordering.

### Producer (Agent role: “Emitter”)
Any agent or service that sends messages into Kafka topics.  
Example: a payments agent emitting transaction or audit events.

### Consumer Group (Agent role: “Worker Swarm”)
A coordinated set of agent instances that jointly process a topic’s partitions.

All pods/instances of the same application form one consumer group for a given topic.

### Consumer (Agent Instance)
A single running instance that pulls records for its assigned partitions.

From an agentic perspective, each consumer is a “worker clone” operating on a subset of the stream.

### Listener (Agent Behavior)
A method or function (e.g., `@KafkaListener`) containing the message-handling logic.

A listener can be configured with concurrency (threads) to run multiple consumer loops in parallel.

### Offset
A monotonically increasing index assigned to each message within a partition. Agents use offsets to know how far they have progressed.

### Offset Commit
The act of recording the last processed offset. Proper commit strategy is critical for avoiding duplicates and data loss.

---

## Consumer vs Listener (Agent View)

- **Consumer**: A running process (pod) that belongs to a consumer group and owns assigned partitions.
- **Listener**: The handler code inside that process, potentially running in multiple threads.

---

## 2. Producer / Emitter Design

### 2.1 Message Key (Routing Intelligence)

Agents use the **message key** to control partition routing and preserve ordering for related data.

Always set a key when:
- Ordering for a logical entity matters, or
- You want all messages for an entity to land on the same partition.

Choose keys that logically group data, for example:
- All events for a given **routing number** share the same key so that a single file per routing number can be generated in order.

Default producer routing:
1. If a partition is explicitly specified → use that.
2. Else, if a key is present → partition is chosen via a **hash (murmur2)** of the key.
3. Else (no key) → **Sticky Partitioner** behavior (Kafka ≥ 2.4.0; see KIP-480):  
   Fill one partition batch fully, then move to another partition, rather than pure round-robin.

**Agentic implication:**  
When designing agent workflows, treat keys as the “shard identity” for stateful agents. All stateful work for a shard should be routed by key.

---

### 2.2 Producer Configuration (Reliability Defaults)

For **Kafka ≥ 3.0**:
- `enable.idempotence=true`
- `acks=all`

These are defaults and must not be overridden unless you fully understand the implications.

These settings enable strong delivery guarantees and are required for exactly-once semantics.

Reference:  
https://cwiki.apache.org/confluence/display/KAFKA/KIP-679%3A+Producer+will+enable+the+strongest+delivery+guarantee+by+default

---

### 2.3 Retry Logic (Before Dead Letter Topics)

Agents should be resilient and self-healing.

- Implement retries before sending messages to a Dead Letter Topic (DLT).
- Use non-blocking retry mechanisms such as `@RetryableTopic` for at-least-once semantics.
- When using exactly-once semantics (transactional processing), use `DefaultErrorHandler` for blocking retries.

**Agentic pattern:**  
Agents should attempt local recovery (retries, backoff) and only surface hard failures via DLTs, never by blocking partitions indefinitely.

---

## 3. Consumer Group & Listener Design

### 3.1 Consumer Group Design (Agent Swarm Identity)

**Naming Convention**

Format:

```
<consumer-service-without-service-word>.<topic-name>
```

Example:

```
payments.transaction-events
reconciliation.settlement-events
```

This ensures:
- Clear ownership visibility
- Easier observability in monitoring systems
- Consistent naming across environments
- Predictable swarm identity