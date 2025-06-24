# Design Pattern: Scatter-Gather

The Scatter-Gather pattern provides a robust way to query multiple services concurrently and aggregate their responses into a single, meaningful result. It is particularly useful when a single request requires data from various independent sources.

## The Problem: Combining Data from Multiple Sources

Many applications need to consolidate information from different subsystems to answer a user's query. The challenge is to do this efficiently without creating dependencies between the source services.

**Common Examples:**

-   **E-commerce Marketplace (like Amazon):** When you search for a product, the system needs to find all the different vendors selling that item, get the price and availability from each, and then present the best offer to you.
-   **Travel Aggregator:** A request to find a flight from New York to London must be sent to dozens of different airline systems simultaneously.
-   **Search Engine (like Google):** A search results page is an aggregation of responses from many specialized services: web search, image search, news search, video search, etc.

## The Solution: An Aggregator

The pattern introduces a central component, often called an **Aggregator** or a **Controller**, that orchestrates the entire process. The workflow is divided into two phases:

### 1. Scatter Phase

The Aggregator receives the initial request from the client. It then re-broadcasts this request to all the relevant downstream services. This is the "scatter" part of the pattern—a single request is scattered out to multiple targets.

### 2. Gather Phase

The Aggregator then waits for the responses to come back from the downstream services. As the responses arrive, it collects them. This is the "gather" phase.

Once the responses are gathered, the Aggregator applies some form of processing logic to create the final, consolidated response to send back to the client. This logic can vary depending on the use case:

-   **Take the Best:** In the e-commerce example, the aggregator would find the vendor with the lowest price and feature that one.
-   **Combine All:** In the search engine example, the aggregator combines the results from all services into a single, rich results page.
-   **Take the First:** In a system for booking time-sensitive inventory (like concert tickets), the aggregator might simply take the first successful response it receives and ignore the rest.

By using this pattern, the downstream services remain completely decoupled from one another, and the client is shielded from the complexity of interacting with multiple systems.
