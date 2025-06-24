# System Design Example: Search Engine Aggregator

This document outlines the design for the aggregator/controller layer of a modern search engine, following the **3-Points System** framework. This component is responsible for orchestrating queries across multiple specialized backend services.

--- 

## Stage 1: The Tactical Questions (The WHY)

-   **Question:** What is the business goal?
-   **Answer:** To build a generic, world-class search engine to compete with services like Google and Bing.

--- 

## Stage 2: The Strategic Questions (The WHAT)

-   **Question:** What specific part of the system should we focus on?
-   **Answer:** We will focus on the **Controller / Aggregator**. Modern search results are composed of "widgets" from many different sources (weather, news, images, videos, web search). The controller is the system that fetches data from all these sources and assembles the final page.

-   **Question:** What are the key requirements?
-   **Answer:**
    1.  **Parallel Queries:** The controller must query many backend subsystems in parallel.
    2.  **Strict Latency SLA:** The entire process must be extremely fast (e.g., under 500ms). Any backend that doesn't respond in time is ignored.
    3.  **Service Discovery:** The controller needs to know about all available backend subsystems (e.g., their API endpoints).
    4.  **Aggregation:** It must gather all the responses and format them into a single, consistent API response for the frontend to render.

--- 

## Stage 3: The Operational Questions (The HOW)

### High-Level Logical Diagram (Scatter-Gather)

The core of the design is the **Scatter-Gather** pattern.

*<-- Placeholder for a diagram showing: User -> Controller -> (Parallel calls to Weather, News, Images, etc. Subsystems) -> Controller aggregates results -> User -->*

1.  **Configuration:** The Controller starts by loading the configuration of all available backend subsystems (e.g., from a simple Config Database like DynamoDB or a configuration file). This data is heavily cached.
2.  **Scatter:** When a user query (e.g., "what to see in New York") arrives, the Controller "scatters" it by sending it to all registered subsystems in parallel.
3.  **Gather:** The Controller waits for a fixed timeout (e.g., 300ms). It "gathers" all the responses that arrive within this window. If a subsystem fails or is too slow, it's simply excluded from the final result.
4.  **Aggregate:** The Controller combines the collected JSON responses into a single array and sends it back to the client.

### System Architecture

*<-- Placeholder for a diagram of the Controller's physical architecture: Load Balancer -> Cluster of stateless EC2 instances, each with a local cache for the subsystem config. -->*

-   **Load Balancer:** Distributes incoming user traffic across the fleet of Controller instances.
-   **Controller Service:** A cluster of identical, stateless servers (e.g., EC2 instances). Because they are stateless, they can be easily scaled horizontally to handle any amount of traffic.
-   **Configuration Cache:** Each Controller instance maintains an in-memory cache of the subsystem endpoints. This cache is refreshed periodically (e.g., once a day), avoiding the need for a highly-scalable, real-time configuration database.

### Optimization: Intelligent Routing

The simple approach is to send every query to every subsystem. This is wasteful. A more advanced design adds an intelligence layer.

-   **Machine Learning Model:** A lightweight ML model can be embedded within each Controller instance. This model's job is to predict which subsystems are relevant for a given query.
-   **Example:**
    -   Query: "Mel Gibson" -> The model predicts this is about a person and routes the query to the Knowledge Base, Movies, and Images subsystems, but *not* the Weather subsystem.
    -   Query: "Weather in London" -> The model routes the query only to the Weather subsystem.
-   **Benefit:** This dramatically reduces the load on the backend subsystems, saving cost and computational resources.
