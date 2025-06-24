# Distributed Systems Design Handbook

This handbook is a collection of notes, design patterns, and practical examples compiled from a course on distributed systems. It aims to provide a clear and structured guide to designing and building robust, scalable, and maintainable distributed systems.

Course Reference: [Introduction to Distributed Systems by Mohamed Mishref](https://www.youtube.com/playlist?list=PLBNC0xAA8XsjEDF_lnSolsqSD-IJ_JU8S)

---

## Table of Contents

### Part 1: Fundamental Concepts

- [00: How to Pass System Design Interviews](./00-how-to-pass-system-design-interviews.md)
- [01: Introduction to Distributed Systems](./01-introduction-to-distributed-systems.md)
- [02: What is a Distributed System?](./02-what-is-a-distributed-system.md)
- [03: A Framework for System Design: The 3-Points System](./03-framework-the-3-points-system.md)
- [04: Introduction to Distributed Design Patterns](./04-introduction-to-distributed-design-patterns.md)

### Part 2: Distributed Systems Design Patterns

- [05: Timestamp Pattern](./05-design-pattern-timestamp.md)
- [06: Command Query Responsibility Segregation (CQRS) Pattern](./06-design-pattern-cqrs.md)
- [07: Two-Phase Commit (2PC) Pattern](./07-design-pattern-two-phase-commit.md)
- [08: Saga Pattern](./08-design-pattern-saga.md)
- [09: Sidecar Pattern](./09-design-pattern-sidecar.md)
- [10: Scatter-Gather Pattern](./10-design-pattern-scatter-gather.md)
- [11: Sharded Services Pattern](./11-design-pattern-sharded-services.md)
- [12: Replicated Load-Balanced Services Pattern](./12-design-pattern-replicated-load-balanced-services.md)
- [13: Passage of Time Event Pattern](./13-design-pattern-passage-of-time-event.md)
- [14: Event Sourcing Pattern](./14-design-pattern-event-sourcing.md)
- [15: Explicit Public Events (Claim Check) Pattern](./15-design-pattern-explicit-public-events.md)
- [16: Segregated Events Layer Pattern](./16-design-pattern-segregated-events-layer.md)
- [17: Decision Tracking Pattern](./17-design-pattern-decision-tracking.md)
- [18: Completeness Guarantee Principle](./18-design-pattern-completeness-guarantee.md)

### Design Patterns at a Glance

| Pattern | Main Idea | Common Use Cases |
| :--- | :--- | :--- |
| **Timestamp** | Synchronizing clocks across nodes using a centralized Time Service to ensure correct event ordering. | Debugging/tracing, business logic dependent on time, preventing data inconsistency (last write wins). |
| **CQRS** | Separating the model for writing data (Commands) from the model for reading data (Queries) to optimize and scale them independently. | High-performance applications, independent scaling of reads/writes, systems with complex business logic. |
| **Two-Phase Commit (2PC)** | An algorithm with a central Coordinator that ensures all services in a transaction either all commit or all abort, guaranteeing atomicity. | Systems requiring strong consistency across services, like distributed databases or financial transactions. |
| **Saga** | Managing long-lived transactions via a sequence of local transactions coordinated by asynchronous events, using compensating transactions to handle failures. | Microservices architectures, e-commerce order processing, booking systems (flights, hotels). |
| **Sidecar** | Deploying auxiliary components (e.g., logging, proxying) into a separate container that runs alongside the main application. | Service mesh proxies (Envoy), logging, monitoring, adding HTTPS to legacy services. |
| **Scatter-Gather** | An Aggregator broadcasts a request to multiple services, then gathers and consolidates their responses into a single result. | Search engine aggregators, travel booking sites, e-commerce marketplaces. |
| **Sharded Services** | A state-aware load balancer that distributes work based on the real-time load of service instances, not just round-robin. | Optimizing resource use in systems with highly variable or long-running tasks. |
| **Replicated Load-Balanced Services** | Running multiple identical, stateless replicas of a service behind a load balancer to distribute traffic. | Achieving high availability and horizontal scalability for any stateless service (e.g., web servers). |
| **Passage of Time Event** | A central service emits a regular "clock tick" event, allowing other services to trigger scheduled actions without managing individual timers. | Scheduling recurring tasks (newsletters), session timeouts, retrying failed operations after a delay. |
| **Event Sourcing** | Storing the full sequence of immutable state-changing events for an entity, rather than just its current state. | Systems requiring a complete audit trail, financial ledgers, temporal queries, debugging complex state changes. |
| **Explicit Public Events (Claim Check)** | Storing a sensitive/large payload in a secure external store and publishing an event containing only a reference (claim check) to it. | Handling PII/sensitive data (GDPR), avoiding message bus size limits, securing data from unauthorized consumers. |
| **Segregated Events Layer** | Embedding authorization metadata into an event to control access to its payload, enabling fine-grained, multi-level security. | Systems with multiple levels of data confidentiality (e.g., user, manager, admin) in a single event stream. |
| **Decision Tracking** | Enriching events with metadata that records the context and reasoning (*why*) behind a state change, not just the outcome (*what*). | Auditing, debugging complex workflows, business intelligence, compliance, understanding system behavior. |
| **Completeness Guarantee** | The principle of recording every single event that occurs in a system, including errors and cancellations, to create a complete and accurate history. | Accurate debugging, rich analytics of user behavior, total system audibility. |

### Part 3: Practical System Design

- [19: Example: Distributed Image Processing](./19-system-design-example-distributed-image-processing.md)
- [20: Example: Video Streaming Service (Netflix)](./20-system-design-example-video-streaming-service.md)
- [21: Example: Search Engine Aggregator](./21-system-design-example-search-engine-aggregator.md)
- [22: Example: Hotel Booking System](./22-system-design-example-hotel-booking-system.md)
