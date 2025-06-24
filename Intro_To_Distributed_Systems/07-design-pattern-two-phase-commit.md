# Design Pattern: Two-Phase Commit (2PC)

Two-Phase Commit is a distributed algorithm that ensures all participants in a transaction either all commit or all abort. It guarantees the atomicity of a transaction across multiple services, even in the face of failures.

## The Problem: Coordinated Action and Tight Coupling

Imagine a complex operation that requires updates across several independent services. For the transaction to be successful, all services must succeed. If even one fails, all the others must roll back their changes.

**Search Engine Example:**

When you search for "New York" on a search engine, the results page is assembled from many subsystems:

-   Web Search Service (organic links)
-   Image Service
-   News Service
-   Maps Service
-   Wikipedia Summary Service

In some scenarios, all of these services *must* respond successfully to form a complete transaction. If the services call each other directly, you create a brittle, **tightly coupled** system. A failure in one service could cascade and leave the system in an inconsistent state, making rollbacks a nightmare.

This creates a "cyclical dependency" problem: Service A can't commit until B does, and B can't commit until C does. What happens if C fails?

## The Solution: A Coordinator and Two Phases

Two-Phase Commit solves this by introducing a central **Coordinator** and breaking the transaction into two distinct phases: a preparation phase and a commit phase.

### The Participants

-   **Coordinator:** The single service that manages and orchestrates the entire transaction.
-   **Participants:** All the other services involved in the transaction (e.g., Image Service, News Service).

The key is that participants only communicate with the coordinator, not with each other. This decouples them.

### Phase 1: The Prepare (or Voting) Phase

1.  The Coordinator sends a **"Prepare"** message to all participants.
2.  Upon receiving the message, each participant gets ready to commit. It performs all the necessary work, acquires any required locks, and validates that it *can* commit without errors.
3.  Crucially, it **does not** actually commit the transaction yet. It just prepares for it.
4.  Each participant then votes by sending a **"Yes"** (I am ready) or **"No"** (I cannot commit) message back to the Coordinator.

### Phase 2: The Commit (or Completion) Phase

The Coordinator collects all the votes. The outcome is decided as follows:

-   **If ALL participants voted "Yes":**
    1.  The Coordinator makes the final decision to commit.
    2.  It sends a **"Commit"** message to all participants.
    3.  Each participant receives the message and finalizes its part of the transaction.

-   **If ANY participant voted "No" (or failed to respond):**
    1.  The Coordinator makes the final decision to abort.
    2.  It sends an **"Abort"** (or Rollback) message to all participants.
    3.  Each participant receives the message and rolls back any changes it made during the prepare phase.

By separating preparation from the final commit, 2PC ensures that the system never ends up in a partially completed state. The transaction is atomic: it's all or nothing.
