# Design Pattern: Passage of Time Event

This pattern provides a scalable mechanism for handling scheduled, recurring, or delayed actions in a distributed system. It allows a system to manage a vast number of time-based triggers without dedicating a resource (like a timer or a thread) to each one.

## The Problem: Scalable Scheduling

Many systems need to perform actions at specific times or after a certain delay. For example:

-   Sending a weekly newsletter.
-   Retrying a failed operation after a 5-minute delay.
-   Ending a user's free trial 30 days after they sign up.

A naive approach would be to create a separate timer for each scheduled task. This works for a small number of tasks but does not scale. A system with millions of users would quickly run out of resources trying to manage millions of individual timers.

## The Solution: A Centralized Clock Tick

The pattern decouples the *scheduling* of an event from the *triggering* of that event. This is achieved by having a central service that emits a generic **"Passage of Time"** event at a regular, granular interval (e.g., every second or every minute). Other services can then subscribe to this "clock tick" to drive their own time-based logic.

### Implementation Strategies

Once a service receives a "clock tick" event, it needs to determine which, if any, of its tasks are now due. There are several ways to do this, with different performance trade-offs.

**1. The Naive Approach: Scan on Tick**

-   **How it works:** The service maintains a database of all its scheduled tasks. On every clock tick, it scans the entire table, calculating for each task whether it is due to run. For a recurring task, it would check if `(current_time - last_run_time) >= interval`.
-   **Pros:** Very simple to implement.
-   **Cons:** Extremely poor performance. Scanning a large table every minute is highly inefficient.

**2. The Pre-Calculation Approach: Future Event Queue**

This is a much more performant and scalable approach.

-   **How it works:** When a recurring task is scheduled (e.g., "run every hour"), the system pre-calculates and stores the *next N* future occurrences as concrete records in a database, each with a precise execution timestamp.
-   The service then simply has to query the database for records where the `execution_timestamp` is now (or in the past minute). This query is very fast as the timestamp column can be indexed.
-   A background job periodically runs to clean up completed events and generate new future events to keep the queue populated.
-   **Pros:** Highly performant and scalable.
-   **Cons:** Uses more storage to hold the future event records.

**3. The Single Next Event Approach**

-   **How it works:** A variation of the pre-calculation approach where only the *single next* future event is stored. When the scheduler processes that event, its final action is to calculate and insert the next occurrence.
-   **Pros:** Keeps the database table small.
-   **Cons:** Can be slightly more complex to manage than the batch pre-calculation approach.
