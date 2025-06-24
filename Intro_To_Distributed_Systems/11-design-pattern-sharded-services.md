# Design Pattern: Sharded Services (Stateful Load Balancing)

While the term "Sharding" usually refers to partitioning data, in this context, it's used to describe a method of partitioning *work* based on the real-time state of service instances. This is a more intelligent form of load balancing, often known as **Stateful Load Balancing** or **Least Load Balancing**.

## The Problem: Inefficient Load Balancing

The standard Round Robin load balancing pattern works well when all requests are roughly equal in the resources they consume. However, it becomes very inefficient when tasks have highly variable workloads.

Consider a service where some requests take 1 second to process, while others take 12 hours. If a load balancer distributes these requests using a simple round-robin strategy, it could easily send all the long-running tasks to one set of servers and all the short tasks to another. This would result in some servers being completely overwhelmed while others sit idle—a poor use of resources.

## The Solution: A State-Aware Load Balancer

To solve this, the load balancer needs to be more intelligent. Instead of blindly distributing requests, it needs to have knowledge (state) about the current load on each of the backend service replicas.

### How it Works

1.  **Utilization Table:** The load balancer maintains a table that tracks the current utilization of each replica. This "utilization score" could be based on various metrics:
    -   CPU load
    -   Memory usage
    -   Active I/O operations
    -   The number of active connections or in-progress requests.

2.  **Keeping the Table Updated:** The central challenge is keeping this utilization table accurate and up-to-date. There are two common approaches:
    -   **Node Self-Reporting:** Each replica is responsible for periodically reporting its own health and load metrics to a shared location that the load balancer can read.
    -   **Central Controller:** A separate controller service is responsible for actively polling all the replicas, collecting their metrics, and updating the central utilization table for the load balancer.

3.  **Intelligent Routing:** When a new request arrives, the load balancer consults the utilization table and routes the request to the replica that is currently the least busy. This ensures that the workload is distributed much more evenly across the entire pool.

### Benefits

-   **Optimal Resource Utilization:** Prevents servers from being overloaded while others are underutilized.
-   **Improved Throughput and Performance:** By sending work to the servers most capable of handling it, the overall system performance is improved.
-   **Robustness:** It's a more robust way to handle unpredictable, heterogeneous workloads.
