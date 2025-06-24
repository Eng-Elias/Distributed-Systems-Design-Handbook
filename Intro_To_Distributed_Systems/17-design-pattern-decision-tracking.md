# Design Principle: Decision Tracking

Decision Tracking is a principle, often used with Event Sourcing, that involves explicitly recording the reasons, context, and logic behind every state change in a system. It answers the question of not just *what* happened, but *why* it happened.

## The Problem: Lost Context

In many systems, an event tells you that something changed. For example, an `OrderStatusChangedToCancelled` event tells you an order was cancelled. However, it doesn't tell you *why*. Was it cancelled by the customer? Did it time out? Was it cancelled by a fraud detection system?

Without this context, the event log is just a record of facts with no story. This makes it difficult to:

-   **Debug complex scenarios:** Why did the system automatically flag this order as fraudulent?
-   **Perform business intelligence:** What is the most common reason for customers cancelling orders?
-   **Ensure audibility:** Can we prove that a specific decision was made in compliance with a business rule?

## The Solution: Record the 'Why'

The solution is to enrich the events with metadata that captures the decision-making process. Instead of just recording the outcome, you record the inputs and the logic that led to the outcome.

### Implementation Strategies

This is typically achieved by adding a `metadata` field to your event payloads. This metadata might include:

-   **Causation ID:** A reference to the command or event that triggered this new event.
-   **User ID:** The user who initiated the action.
-   **Business Rule Info:** The specific rule or policy that was triggered (e.g., `"rule_name": "fraud_check_v2", "score": 0.98`).
-   **Justification:** A human-readable reason for the action (e.g., `"reason": "Customer clicked 'Cancel Order' button"`).

By capturing this decision trail, you transform a simple event log into a rich, auditable, and insightful history of your system's behavior. This aligns with the **Completeness Guarantee** principle, ensuring the event log is a perfect and complete record of the system.
