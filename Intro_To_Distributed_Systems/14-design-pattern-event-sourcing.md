# Design Pattern: Event Sourcing

Event Sourcing is a powerful pattern that changes the way we think about data. Instead of storing just the *current state* of an entity, we store the full sequence of state-changing events that have ever occurred. The current state is then derived by replaying these events.

## The Problem: Loss of History

In a traditional Create, Read, Update, Delete (CRUD) model, when we update a record, the previous state is lost forever. We overwrite the data.

**E-commerce Order Example:**

Consider an order in a database with a `status` field. The status might change over time:

1.  `RECEIVED`
2.  `PAID`
3.  `SHIPPED`
4.  `DELIVERED`

If the current status is `DELIVERED`, a simple CRUD model tells us nothing about the journey. We have lost valuable information:

-   *When* was the order paid for?
-   How long did it take to go from `PAID` to `SHIPPED`?
-   *Who* was the employee who marked it as shipped?

This loss of history makes auditing, debugging, and business analysis extremely difficult.

## The Solution: Store Events, Not State

With Event Sourcing, we treat every state change as an immutable event and append it to a log. The state of the order is not a single row in a table, but a sequence of events:

-   `{ event: "OrderCreated", timestamp: ..., data: {...} }`
-   `{ event: "OrderPaid", timestamp: ..., data: { payment_method: 'visa' } }`
-   `{ event: "OrderShipped", timestamp: ..., data: { employee_id: 'E789', warehouse: 'W2' } }`
-   `{ event: "OrderDelivered", timestamp: ..., data: { courier: 'fedex' } }`

This approach is analogous to how a bank ledger or a version control system (like Git) works. You have a complete, unchangeable history of everything that has happened.

### Reconstructing State

To get the current state of the order, we simply replay the events in sequence. This gives us a powerful ability to not only see the current state but also to see the state of the entity at *any point in time*.

## Benefits of Event Sourcing

-   **Complete Audit Trail:** Provides a full, immutable history of every change in the system, which is invaluable for investigation and compliance.
-   **Powerful Analytics:** The rich historical data is a goldmine for data science, business intelligence, and training machine learning models.
-   **Debugging and Error Analysis:** We can easily trace the exact sequence of events that led to a bug or an inconsistent state.
-   **Temporal Queries:** It becomes trivial to ask questions about the state of the system at any point in the past.

Event Sourcing is a foundational concept that enables many other advanced patterns and provides a much more robust and insightful way to handle data in complex systems.
