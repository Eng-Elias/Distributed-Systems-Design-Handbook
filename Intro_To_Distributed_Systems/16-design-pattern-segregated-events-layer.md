# Design Pattern: Segregated Events Layer

This pattern is an advanced evolution of the **Explicit Public Events (Claim Check)** pattern. It provides a mechanism for handling fine-grained access control when a single event stream contains data with multiple different levels of privacy and authorization.

## The Problem: Multi-Level Data Privacy

The Claim Check pattern is excellent for separating public data from private data. However, in many complex systems, "private data" is not a single category. There can be multiple tiers of confidentiality:

-   Data only the user should see.
-   Data that customer support can see.
-   Data that senior managers can see.
-   Data that only system administrators can see.

Placing all this data behind a single claim check is insufficient, as it doesn't distinguish between these different authorization levels.

## The Solution: Embed Authorization Metadata

The Segregated Events Layer pattern solves this by embedding the required authorization level directly into the event payload, alongside the encrypted data or claim check.

### How it Works

1.  When an event is created, any sensitive data is encrypted.
2.  Alongside the encrypted data, the event includes a metadata field specifying the **authorization level** required to decrypt it (e.g., `"required_auth_level": "ACCOUNT_MANAGER"`).
3.  A downstream service consumes the event.
4.  Before attempting to decrypt the data, the service first checks the `required_auth_level` field.
5.  The service then compares this required level against its own credentials. It only proceeds with decryption if it has the necessary permissions.

### Benefits

-   **Fine-Grained Access Control:** It allows for multiple levels of security within a single, unified event stream.
-   **Decoupling and Flexibility:** The event producer doesn't need to know which specific consumers can see the data. It only needs to classify the data's sensitivity. New consumers can be added later without changing the producer, as long as they have the correct authorization credentials.
-   **Enhanced Security:** It prevents services from even attempting to access data they are not authorized to see.
