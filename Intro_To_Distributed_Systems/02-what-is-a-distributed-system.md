# What is a Distributed System?

This lecture introduces the fundamental concepts of distributed systems, moving from traditional software design to the architecture of large-scale systems. It covers the core components that act as building blocks for any distributed system.

## From Software Design to System Design

-   **Traditional Software Design:** Focused on concepts within a single application, such as Object-Oriented Programming (OOP), SOLID principles, and design patterns. These principles govern the internal structure of a codebase.
-   **System Design:** Addresses the architecture of a complete system (e.g., Uber, Netflix). Such systems are too large and complex to run on a single server, which leads to the core principle of being **distributed** across multiple machines (nodes).

## The Lego Analogy for System Components

Designing a distributed system is like building with Lego blocks. The individual components are standardized, but the way you assemble them determines the final outcome—be it a search engine, a social network, or a streaming service.

Here are the fundamental "Lego blocks" of any distributed system:

### 1. Storage

Storing data is a foundational need. The choice of storage technology depends heavily on the data's structure and access patterns.

-   **Relational Databases (SQL):**
    -   **Use Case:** Best for structured, interconnected data where integrity is paramount (e.g., banking systems).
    -   **Examples:** MySQL, PostgreSQL, SQL Server.
    -   **Tradeoffs:** The rigid schema can make evolution difficult, and complex `JOIN` operations can be slow under heavy load.

-   **NoSQL Databases:**
    -   **Use Case:** Ideal for data that is not relational, is semi-structured, or requires high scalability and flexibility.
    -   **Types:**
        -   **Key-Value Stores:** Simple key-to-value mapping. Highly scalable and flexible (e.g., Redis, DynamoDB).
        -   **Document Stores:** Store data in document formats like JSON or XML (e.g., MongoDB, Couchbase).
        -   **Graph Databases:** Designed for data with complex relationships, making it easy to query connections (e.g., Neo4j for social networks).

-   **Cache:**
    -   **Use Case:** A fast, in-memory database used to store frequently accessed data, reducing latency and load on the main database. A common example is a **Content Delivery Network (CDN)**, which caches static assets (images, videos) geographically closer to users.

-   **File Storage:**
    -   **Use Case:** Storing large binary files (e.g., videos, high-resolution images). Storing these in a traditional database is highly inefficient because a single large file can span many data pages, making queries extremely slow. The standard pattern is to store the file in a dedicated file storage system and save only the metadata and a file path/URL in the database.
    -   **Examples:** Amazon S3, Azure Blob Storage, HDFS.

### 2. Computation Nodes

These are the services that contain the business logic of the application. They are the workhorses of the system, responsible for processing data and executing tasks (e.g., a payment processing service, a user authentication service).

### 3. Analytics

It's impossible to operate a large-scale system without understanding what's happening within it. Analytics services collect, process, and visualize data about user behavior, system performance, and business metrics.
-   **Example:** Google Analytics.

### 4. Load Balancers

A load balancer sits in front of the computation nodes and distributes incoming requests across them. This prevents any single server from becoming a bottleneck and improves availability and fault tolerance.
-   **Simple Strategy:** Round Robin (sends requests to servers in a cycle).
-   **Advanced Strategies:** Can route traffic based on server health, current load, or geographic location.

### 5. Queues

A queue is a data structure that follows a First-In, First-Out (FIFO) principle. In distributed systems, it acts as a buffer between services.
-   **Primary Use Case:** Decoupling a fast service from a slow one. The fast service can quickly write messages to the queue without waiting for the slow service to be ready. The slow service can then process messages from the queue at its own pace.
-   **Key Property:** Once a message is read from a queue, it is removed.
-   **Examples:** Amazon SQS, RabbitMQ.

### 6. Streams

A stream is similar to a queue but with one critical difference: messages are **not** removed when they are read. This allows multiple, independent services to consume the same message.
-   **Primary Use Case:** Fan-out scenarios where a single event needs to be processed by multiple downstream systems (e.g., a new user signs up, and the event is sent to the email service, the analytics service, and the recommendation service).
-   **Examples:** Apache Kafka, Amazon Kinesis.

---

## Scaling and System Architecture

While the components above are the building blocks, the core challenge of a distributed system is **scaling**—its ability to handle growing amounts of load.

### Computational Scaling

This refers to scaling the services that perform the actual processing. There are two fundamental approaches:

-   **Vertical Scaling (Scale-Up):**
    -   **What it is:** Increasing the resources of a single server (e.g., adding more CPU cores, RAM, or a faster disk).
    -   **Pros:** It's simple. You don't need to change your application's code.
    -   **Cons:** It's expensive and has a hard physical limit. You can't add resources indefinitely.

-   **Horizontal Scaling (Scale-Out):**
    -   **What it is:** Adding more servers to a pool of resources. Instead of one powerful machine, you have many commodity machines working together.
    -   **Pros:** This is the foundation of modern, large-scale systems (Google, Amazon, Netflix). It can scale almost infinitely and is more cost-effective.
    -   **Cons:** The system must be explicitly designed to be distributed. This adds complexity to development and operations.

### Database Scaling

Databases are often a system's biggest bottleneck. They also have two primary scaling strategies:

-   **Replication (Read Replicas):**
    -   **What it is:** Creating multiple read-only copies of the main (master) database.
    -   **Use Case:** Excellent for read-heavy workloads. All write operations go to the master, but read operations can be spread across many replicas, reducing the load on the master.

-   **Sharding:**
    -   **What it is:** Partitioning the data across multiple, independent databases. Each database (or "shard") holds a different subset of the data.
    -   **Example:** A database of national users could be sharded by city, so all Cairo users are in one database and all Alexandria users are in another.
    -   **How it works:** A routing layer is needed to inspect incoming requests and direct them to the correct shard.

> These two techniques are often used together. For example, each shard in a sharded database can have its own set of read replicas.

### Architectural Patterns: Monolith vs. Microservices

This choice has a massive impact on how a system scales.

-   **Monolith:** The entire application is built and deployed as a single, large unit.
-   **Microservices:** The application is broken down into a collection of small, independent services, each focused on a single business function (e.g., payments, search, user profiles).

**The Deciding Factor: Asymmetrical Load**

The primary reason to choose microservices is when different parts of your system have vastly different performance requirements.

-   **E-commerce Example:**
    -   **Homepage:** Receives 1,000 requests/second.
    -   **Search Service:** Receives 800 requests/second.
    -   **Product Details Service:** Receives 300 requests/second.
    -   **Payment Service:** Receives only 50 requests/second.

With a **monolith**, you must provision hardware for the peak load (1,000 req/sec). This means the code for the payment service, which is rarely used in comparison, is running on expensive hardware that it doesn't need.

With **microservices**, you can scale each service independently. You can run the homepage service on dozens of servers while running the payment service on just a few. This is significantly more efficient and cost-effective.
