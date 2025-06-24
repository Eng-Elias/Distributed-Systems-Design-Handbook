# How to Pass the System Design Interview

This document summarizes a strategy for successfully navigating system design interviews, based on the insights from M. Moshrif's course.

## The Core Problem with System Design Interviews

System design interviews are inherently different from standard algorithmic problem-solving. Key challenges include:

- **Subjectivity:** Unlike coding problems with a clear right or wrong answer, system design solutions are open-ended and subject to the interviewer's opinion.
- **Time Constraint:** You are asked to design a system in 35-45 minutes that would take weeks or months to develop in reality.
- **Ambiguity:** The process is a complex, iterative one condensed into a short, high-pressure session.

The goal isn't to produce a perfect, production-ready design, but to **demonstrate that you know what it takes to create one.**

## The Three Pillars of a Successful Interview

Success in a system design interview hinges on mastering three key areas:

1.  **Time Management:** Failing to cover critical aspects because you ran out of time is equivalent to not knowing them at all.
2.  **Structure:** A logical, well-defined structure prevents you from jumping randomly between high-level concepts and low-level details.
3.  **Asking the Right Questions:** The quality of your design is directly proportional to the quality of your questions. You build the right system by first asking the right questions.

---

## A Proposed Framework for Structure and Time Management

The key is to present a clear plan to the interviewer at the beginning. This manages expectations and demonstrates a structured approach.

### Two Types of System Design Interviews

1.  **Product Design:** Broad and open-ended (e.g., "Design Netflix"). Requires significant scoping.
2.  **System Design (Feature-focused):** Narrow and specific (e.g., "Design YouTube's view counter"). Focuses more on scalability and technical details.

### The Interview Checklist

Present this checklist to your interviewer. It shows you have a plan and allows them to customize it based on what they want to focus on.

-   [ ] **Questions** (5 mins): Ask the right questions to understand the problem (Why -> What -> How).
-   [ ] **Personas** (2 mins): Identify the types of users (e.g., User, Admin, Power User).
-   [ ] **UX Experience Flow** (3 mins): For each persona, outline their journey in 3-4 simple steps.
-   [ ] **Functional Requirements** (2 mins): Define the features that users will directly interact with.
-   [ ] **Non-Functional Requirements** (2 mins): Define the qualities of the system (Scalability, Availability, Latency, Consistency, Durability, Security).
-   [ ] **Core Problem** (3 mins): What is the single most challenging part of this system? (e.g., bandwidth for a streaming service, consistency for a banking app).
-   [ ] **Entities** (1 min): A flat list of the main data objects (e.g., Photo, Video, Filter, User).
-   [ ] **APIs** (2 mins): High-level function signatures.
-   [ ] **Data Models** (3 mins): Database table structures. No need for deep detail on data types initially.
-   [ ] **Trade-offs** (2 mins): Discuss different approaches to solving the core challenge.
-   [ ] **System Flow** (3 mins): A narrative description of the system diagram.
-   [ ] **Low Level Calculations** (2 mins): (More critical for feature-focused design) Estimate storage, RPS, etc.
-   [ ] **System Diagram** (5 mins): The final drawing.

> **Note:** The order is flexible. For a feature-focused design, `Low-Level Calculations` might come much earlier. **Always confirm the order with your interviewer.**

> Try my system design interview timer tool: [www.npmjs.com/package/@eng-elias/system-design-interview-timer](https://www.npmjs.com/package/@eng-elias/system-design-interview-timer)

---

## A Framework for Asking Questions

Structure your questions to cover all bases and avoid missing critical information.

### 1. Ask "Why?" (The Purpose)

This is especially critical for senior roles.

-   **Challenge the Request:** "Why do we need to build this? Could we solve the problem another way?" This demonstrates critical thinking and that you're not just a task-taker.
-   **Gain Deeper Context:** Understanding the business motivation provides valuable information for your design choices.

### 2. Ask "What?" (The Scope)

-   **Business Requirements:** Scope the problem down as much as possible.
    -   *Example (for Google Search):* "Are we searching text, images, videos, or shopping? For a specific country or the whole world?"
-   **Logistical Requirements:**
    -   Is this an MVP, a prototype, or a full-scale product?
    -   Will it run in a single data center or multiple? (This implies cross-DC replication, latency issues, etc.)

### 3. Ask "How?" (The Implementation Details)

-   **Product Requirements (Features):** Get specific about features.
    -   *Example (for Snapchat):* "What kind of filters are we applying? Can users create their own?"
-   **Technical Requirements (Infrastructure):**
    -   Are we restricted to on-premise servers, or can we use the cloud?
    -   Are there any preferred technologies or languages?

### The Biggest Mistake: Not Asking About the Core Problem

A common failure is to start designing without understanding the most fundamental part of the system. If you're asked to design a "filter service," you must first ask, **"What is a filter?"** Is it a binary file? A set of instructions? User-generated? Your entire design depends on this answer.
