# System Design Example: Video Streaming Service (Netflix)

This document outlines the design for a large-scale video streaming service, following the **3-Points System** framework.

--- 

## Stage 1: The Tactical Questions (The WHY)

-   **Question:** What is the business goal?
-   **Answer:** A large telecom company wants to create a new video streaming service to gain a competitive advantage and provide a value-add for its existing customers. The service will operate in multiple countries.

--- 

## Stage 2: The Strategic Questions (The WHAT)

-   **Question:** What are the core features for the Minimum Viable Product (MVP)?
-   **Answer:** The system must support two primary use cases:
    1.  **Video Upload:** A workflow for content producers to upload and process new videos.
    2.  **Video Streaming:** A workflow for end-users to browse and watch videos seamlessly.

-   **Question:** What are the most important non-functional requirements?
-   **Answer:**
    -   **High Availability & Durability:** The service must be resilient to failures, and uploaded videos must never be lost.
    -   **Low Latency:** Video playback must start quickly, and buffering should be minimal.
    -   **Scalability:** The system must handle millions of users and a massive library of video content.

--- 

## Stage 3: The Operational Questions (The HOW)

The system can be broken down into two distinct workflows: the **Video Processing Pipeline** (offline) and the **Video Streaming Workflow** (real-time).

### 1. Video Processing Pipeline

This is the asynchronous workflow that prepares a video for streaming after it's uploaded.

*<-- Placeholder for a diagram showing: Producer -> S3 -> SQS -> Transcoder Workers -> S3 & Metadata DB -->*

1.  **Upload:** A content producer uploads a single, high-resolution video file to a raw storage bucket (e.g., AWS S3).

2.  **Trigger Processing:** The upload to S3 triggers an event notification, which places a message into a queue (e.g., AWS SQS). This message contains information about the video file.

3.  **Transcoding & Chunking:** A fleet of worker nodes (e.g., EC2 instances or a serverless function like AWS Lambda) pulls messages from the queue. For each video, the workers perform two critical tasks:
    -   **Transcoding:** The original video is converted into multiple formats and resolutions (e.g., 1080p, 720p, 480p). This is essential for **Adaptive Bitrate Streaming**.
    -   **Chunking:** Each transcoded video is broken down into small, 2-10 second segments or chunks.

4.  **Store Processed Chunks:** The resulting video chunks are stored in a separate, public-facing storage bucket.

5.  **Update Metadata:** Once processing is complete, the worker updates a metadata database (e.g., DynamoDB or Cassandra). This database stores information about the video, including its title, description, and the locations (URLs) of all its corresponding chunks.

### 2. Video Streaming Workflow

This is the real-time workflow that delivers video to the end-user.

*<-- Placeholder for a diagram showing: User -> Streaming Service -> CDN -> User's Player -->*

1.  **User Clicks Play:** The user's client application (e.g., a web browser or mobile app) makes a request to our **Streaming Service**.

2.  **Fetch Metadata:** The Streaming Service retrieves the video's metadata from the database, including the list of all available quality levels and their chunk locations.

3.  **Content Delivery Network (CDN):** The most critical component for low-latency streaming is a CDN (e.g., Cloudflare, Akamai, or AWS CloudFront). The processed video chunks from the S3 bucket are distributed and cached at CDN **edge locations** around the world, physically close to users.

4.  **Redirect to CDN:** The Streaming Service sends the list of chunk URLs to the client, pointing to the nearest CDN edge location.

5.  **Adaptive Bitrate Streaming:** The client's video player begins downloading the video chunks sequentially from the CDN. The player continuously monitors the user's network bandwidth and latency. 
    -   If the network is fast, it requests higher-quality (higher bitrate) chunks for a better viewing experience.
    -   If the network is slow, it requests lower-quality chunks to avoid buffering and ensure smooth playback.
