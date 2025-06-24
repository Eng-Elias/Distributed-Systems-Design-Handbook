# Design Pattern: Replicated Load-Balanced Services

This is one of the most fundamental patterns in distributed systems, providing a straightforward way to achieve both scalability and high availability.

## The Problem: The Single Server Bottleneck

If a service runs on a single server, it has two major weaknesses:

1.  **It's a Single Point of Failure (SPOF):** If that server crashes or becomes unavailable, the entire service goes down.
2.  **It has a Performance Ceiling:** The server can only handle a finite amount of traffic. Once it reaches its capacity, performance will degrade, and requests will be dropped.

## The Solution: Replicate and Distribute

The solution is to run multiple identical copies (replicas) of the service on different servers and place a **Load Balancer** in front of them.

### How it Works

1.  **Replication:** Multiple instances of the same service are deployed. They are stateless and interchangeable, meaning any replica can handle any given request.

2.  **Load Balancing:** A load balancer acts as the single entry point for all incoming client requests. Its job is to distribute these requests across the pool of healthy replicas.

3.  **Distribution Strategy:** The load balancer uses a routing algorithm to decide which replica should receive the next request. A common and simple strategy is **Round Robin**, where requests are sent to replicas in a cyclical sequence (the first request to replica 1, the second to replica 2, and so on).

4.  **Health Checks:** The load balancer continuously monitors the health of the replicas, often by sending a simple "ping" request. If a replica fails to respond, the load balancer removes it from the pool of available servers and stops sending traffic to it until it becomes healthy again.

### Benefits

-   **High Availability:** If one replica fails, the load balancer automatically redirects traffic to the remaining healthy replicas, ensuring the service remains available to users.
-   **Horizontal Scalability:** To handle more traffic, you can simply add more replicas to the pool. The load balancer will automatically start distributing requests to the new instances.
