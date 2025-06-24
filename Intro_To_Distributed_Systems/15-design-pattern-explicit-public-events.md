# Design Pattern: Explicit Public Events (The Claim Check Pattern)

This pattern provides a secure way to handle sensitive data within a public event stream, ensuring that private information is not exposed to services that shouldn't have access to it. It is essential for building systems that comply with privacy regulations like GDPR (General Data Protection Regulation).

## The Problem: Sensitive Data in Public Events

In an event-driven architecture, events are often published to a central stream that is consumed by many different services. However, some events may need to contain sensitive data, such as Personally Identifiable Information (PII), financial details, or other confidential information.

Placing this data directly into a public event is a major security risk. It violates the principle of least privilege, as services that don't need the sensitive data would still have access to it. It also makes it very difficult to comply with privacy laws.

## The Solution: The Claim Check Pattern

Instead of putting the large or sensitive payload directly in the message, you store it in an external, secure data store. The message itself then contains a reference—a "claim check"—that allows interested parties to retrieve the payload.

### How it Works

1.  **Store the Payload:** The service that originates the event takes the sensitive data payload and stores it in a secure, access-controlled database or blob store.

2.  **Generate a Claim Check:** The service generates a unique key or token (the claim check) that can be used to look up the stored payload.

3.  **Publish the Event:** The service publishes a public event that contains the claim check instead of the actual sensitive data. The public event can contain all the other non-sensitive information as usual.

4.  **Retrieve the Payload:** A downstream service that is authorized to view the sensitive data consumes the public event. It extracts the claim check and uses it to query the secure data store and retrieve the full payload.

### Benefits

-   **Security:** Sensitive data is never exposed on the public event stream. Access is tightly controlled at the secure data store.
-   **Compliance:** Helps systems adhere to strict data privacy regulations.
-   **Efficiency:** Services that don't need the sensitive data can process the event without the overhead of handling a large or complex payload.
