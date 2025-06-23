# A System Design Strategy

System design is the process of defining the architecture, components, modules, interfaces, and data for a system to satisfy specified requirements. It is a creative process that requires a deep understanding of technical principles and trade-offs.

This guide provides a structured strategy for designing real-world, scalable, and resilient systems.

## 1. Start with Why (The Purpose): Understand the Core Problem

Before writing a single line of code or drawing a single box on a whiteboard, it's crucial to understand the fundamental purpose of the system. Answering these "Purpose" questions will form the foundation of your design decisions.

-   **Why are we building this system?** What is the primary user pain point or business need it addresses?
-   **What is the core value proposition?** What makes this system unique or better than existing solutions?
-   **Who are the users?** Understanding the user personas helps in tailoring the user experience and functionality.
-   **How will we measure success?** Define key performance indicators (KPIs) upfront (e.g., user engagement, revenue, system uptime).

## 2. Define the Scope: Requirements Engineering

With a clear purpose, the next step is to define the system's boundaries and capabilities. Requirements are typically divided into three categories:

### Functional Requirements (FRs)

These define what the system *does*. They are the features that users will directly interact with.

-   **Example:** For a photo-sharing app, functional requirements would be: user can upload a photo, user can view a photo, user can add a comment.

### Non-Functional Requirements (NFRs)

These define the qualities of the system. They are critical for the system's success and dictate many architectural choices.

| NFR Category  | Description                                       | Key Questions                                                              |
| :------------ | :------------------------------------------------ | :------------------------------------------------------------------------- |
| **Scalability** | Ability to handle growing amounts of load.          | How many users do we expect? What is the expected growth rate?               |
| **Availability**| The proportion of time the system is operational. | What is the acceptable downtime (e.g., 99.99% uptime)?                     |
| **Latency**     | The time it takes to process a request.           | What is the maximum acceptable response time for critical operations?      |
| **Consistency** | Ensures all nodes see the same data at the same time. | How critical is it for data to be perfectly consistent across the system?  |
| **Durability**  | Ensures that committed data is never lost.        | What is the tolerance for data loss in case of a failure?                  |
| **Security**    | Protection against unauthorized access and data breaches. | What are the potential security threats? How will we handle user data? |

### Extended Requirements (ERs)

These are "nice-to-have" features that might be considered for future versions or if time permits.

-   **Example:** Analytics dashboard, automated abuse detection, ... .

## 3. Estimation and Constraints: Back-of-the-Envelope Calculations

Quantify the NFRs to understand the scale of the system. These estimations are crucial for capacity planning and technology choices.

### Formulas for Estimation

-   **Traffic:**

    - Read-Heavy or Write-Heavy

    - Read/Write Ratio

    - Generated Data per Month

    - Reads/Writes per Month

-   **Requests Per Second (RPS):**

    **Write RPS:**

    `Write RPS = (Total Monthly Write Requests) / (30 days * 24 hours/day * 3600 seconds/hour)`

    **Read RPS:**

    `Read RPS = (Total Monthly Read Requests) / (30 days * 24 hours/day * 3600 seconds/hour)`

    OR

    `Read RPS = (Read/Write Ratio * Write RPS)`


-   **Bandwidth:**

    `Write Bandwidth (bytes/sec) = Write RPS * (Average Request Size in bytes)`

    `Read Bandwidth (bytes/sec) = Read RPS * (Average Request Size in bytes)`

-   **Storage:**

    - Total Number of Stored Records in 10 years:

    `Writes per Month * 12 months/year * 10 years`

    - Total Storage:

    `Total Number of Stored Records in 10 years * Size per Record`

-   **Cache Memory:**

    For caching, we will follow the 80/20 rule (unless otherwise specified). This means that 80% of the requests are for 20% of the data, so we can cache around 20% of our requests.

    Requests per Day:

    `Read RPS * 30 days * 24 hours/day * 3600 seconds/hour`

    Cache Memory:

    `Requests per Day * 20% * Average Object Size`

### High-level estimate

Here is our high-level estimate:

| Type	| Estimate |
| :---- | :------- |
| Write RPS |  |
| Read RPS |  |
| Incoming Bandwidth (Write) |  |
| Outgoing Bandwidth (Read) |  |
| Storage (10 years) |  |
| Cache Memory per Day |  |


## 4. Data Model Design

Define the data entities, their attributes, and the relationships between them. This is the blueprint for your database.

-   **Identify Entities:** What are the main nouns in your system (e.g., User, Product, Order)?
-   **Define Relationships:** How do they connect 1-to-1, 1-to-many, or many-to-many (e.g., a User *has many* Orders)?
-   **Choose Attributes:** What information does each entity hold?

***
**Diagram Placeholder:** draw **Entity-Relationship Diagram (ERD)** to visualize the data model.
***

### Choosing the Right Database

| Database Type | Best For                                                              | Examples                               |
| :------------ | :-------------------------------------------------------------------- | :------------------------------------- |
| **SQL**       | Structured data, ACID compliance, complex queries, and transactions.  | PostgreSQL, MySQL, Amazon RDS          |
| **NoSQL**     | Unstructured/semi-structured data, high scalability, and flexibility. | MongoDB, Cassandra, Amazon DynamoDB    |

## 5. API Design (Contract First)

Design the API endpoints that will expose your system's functionality. A "contract-first" approach ensures clarity between frontend and backend teams.

### API Design Template (REST)

**Endpoint:** `POST /api/v1/resource`

-   **Description:** Creates a new resource.
-   **Request Body:**

    ```json
    {
      "attribute1": "value1",
      "attribute2": "value2"
    }
    ```

-   **Response (201 Created):**

    ```json
    {
      "id": "resource_id",
      "attribute1": "value1",
      "attribute2": "value2"
    }
    ```

-   **Error Responses:**
    -   `400 Bad Request`: Invalid input.
    -   `401 Unauthorized`: Missing or invalid authentication.
    -   `500 Internal Server Error`: Server-side issue.

## 6. High-Level Architecture

Draft the initial high-level design by identifying the major components and how they interact.

-   **Core Components:** Load Balancer, Web Servers, Application Servers, Database, Cache, Message Queue, CDN.

***
**Diagram Placeholder:** Draw a **High-Level Architecture Diagram** to show the main components and data flow.
***

### Monolithic vs. Microservices

| Approach        | Pros                                       | Cons                                                 |
| :-------------- | :----------------------------------------- | :--------------------------------------------------- |
| **Monolithic**  | Simple to develop, test, and deploy initially. | Tightly coupled, hard to scale, single point of failure. |
| **Microservices** | Scalable, independently deployable, resilient. | Complex, distributed system challenges, operational overhead. |

## 7. Detailed Design: Deep Dive

Zoom into the critical components of your high-level design.

-   **Data Partitioning (Sharding):** How will you distribute data across multiple database servers to scale?
-   **Caching Strategy:** Where will you introduce a cache (e.g., Redis, Memcached) to reduce latency and database load? What cache eviction policy (LRU, LFU) will you use?
-   **Asynchronous Workflows:** For long-running tasks, use a message queue (e.g., RabbitMQ, Kafka) to decouple services and improve responsiveness.

***
**Diagram Placeholder:** Draw **Detailed Diagrams** for complex components like the caching layer or a specific microservice.
***

## 8. Identify and Resolve Bottlenecks

Proactively identify potential weaknesses in your design and plan for failure.

-   **Single Points of Failure (SPOF):** Ensure every component has redundancy.
-   **Load Balancing:** Distribute traffic effectively across your services.
-   **Database Replication:** Use primary-secondary replicas for read scaling and high availability.
-   **Monitoring and Alerting:** Implement robust monitoring (e.g., Prometheus, Grafana) to detect issues before they impact users.

***
**Diagram Placeholder:** Draw a final, **Resilient System Architecture Diagram** showing load balancers, replicas, and failover mechanisms.
***

