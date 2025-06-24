# System Design Example: Distributed Image Processing

This document walks through the design of a cloud-based image processing service, following the **3-Points System** framework. The goal is to offload intensive filter processing from a user's device to a scalable backend.

--- 

## Stage 1: The Tactical Questions (The WHY)

-   **Question:** Why do we need to build this on the cloud instead of on the user's device?
-   **Answer:** On-device processing is slow, drains the battery, and is limited to simple filters. A cloud-based system allows for more complex processing, offers a better user experience, enables analytics, and is far more scalable.

--- 

## Stage 2: The Strategic Questions (The WHAT)

-   **Question:** What are the core features and APIs?
-   **Answer:** The system needs several key APIs:
    1.  **User-Facing:**
        -   `listFilters()`: Get a list of all available filters.
        -   `applyFilter(image, filterId)`: The main entry point. The user submits an image and a desired filter, which kicks off the processing workflow.
        -   `downloadImage(imageId)`: Get a processed image.
    2.  **Admin-Facing:**
        -   `uploadFilter(filterName, filterBinary)`: Allows administrators to add new filters to the system.
    3.  **Internal:**
        -   A notification system to inform the user when their image is ready.

-   **Question:** What are the non-functional requirements and constraints?
-   **Answer:**
    -   **Scope:** Photos only, not video.
    -   **Scale:** Must work globally.
    -   **Input Size:** Supports resolutions up to 20 megapixels.
    -   **Extensibility:** The number of filters is not limited.
    -   **Data Policy:** Images are deleted after 3 months.

--- 

## Stage 3: The Operational Questions (The HOW)

The architecture is asynchronous and composed of several microservices.

### High-Level Logical Diagram

*<-- Placeholder for a diagram showing: User -> Orchestrator. Orchestrator communicates with Image Storage (S3), a Photo Processor service, and a Filter Manager service. An Admin also communicates with the Filter Manager. -->*

1.  **Orchestrator Service:** The main entry point that coordinates the entire workflow.
2.  **Image Storage:** A blob store like **AWS S3** for storing original and processed images.
3.  **Filter Manager Service:** Manages the filter metadata (stored in a NoSQL DB like DynamoDB) and the filter binaries (stored in S3).
4.  **Photo Processor Service:** The core compute engine that applies filters to images.

### The Asynchronous Processing Workflow

The most critical part of the design is handling the `applyFilter` request without making the user wait. This is a classic use case for a message queue.

*<-- Placeholder for a diagram showing: Orchestrator -> SQS Queue -> Photo Processor Cluster -->*

1.  **Request:** The user sends an image and `filterId` to the **Orchestrator**.
2.  **Upload & Enqueue:** The Orchestrator first uploads the raw image to S3 to get an `imageId`. It then places a message containing the `imageId` and `filterId` onto a queue (e.g., **AWS SQS**).
3.  **Acknowledge:** The Orchestrator immediately returns a `202 Accepted` response to the user, confirming the request was received and is being processed.
4.  **Process:** The **Photo Processor** is a cluster of worker nodes that pull jobs from the queue.
    -   A worker fetches the job message.
    -   It downloads the original image from S3.
    -   It fetches the correct filter binary from the Filter Manager.
    -   It applies the filter.
    -   It uploads the new, processed image to S3.
5.  **Notify & Complete:** Once the processed image is uploaded, the worker calls a **Notification Service** (which sends a push notification or email to the user) and then, finally, **deletes the message** from the queue. 

### Handling Failures

Using a queue is essential for reliability. If a Photo Processor node crashes mid-task, the message it was working on is not deleted. After a visibility timeout, the message becomes visible again on the queue, and another healthy worker node will pick it up and retry the job, ensuring the user's request is eventually fulfilled.
