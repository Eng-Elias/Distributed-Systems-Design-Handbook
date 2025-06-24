# A Framework for System Design: The 3-Points System

This document outlines a structured, 3-Points framework for approaching any system design problem. By methodically answering the questions in each stage, you can ensure that you build the right solution for the right problem.

--- 

## Point 1: The Tactical Questions (The WHY)

This is the most critical point, yet it is the one most often overlooked by engineers. Before writing a single line of code, you must deeply understand the business context and the true problem you are trying to solve. The goal is to challenge assumptions and uncover the core motivation.

**Key Questions to Ask:**

-   **Why are we building this?** What is the fundamental business problem?
-   **Why now?** What is the urgency?
-   **Why this way?** Could the problem be solved more easily by changing the process instead of building software? (As in the example of replacing a manual Excel process by connecting directly to the source APIs).
-   **Why me/my team?** Are we the right people for this job?
-   **Why this technology?** Are we choosing a technology because it's familiar or because it's the best fit?
-   **What is the history?** What has been tried before and why did it fail?
-   **Who are the competitors?** What do they do well? What is our competitive advantage?

The output of this stage is a deep understanding of the problem space, preventing you from building a technically perfect but ultimately useless product.

--- 

## Point 2: The Strategic Questions (The WHAT)

Once you understand the "Why," you can define "What" you are going to build. This stage is about defining the scope, requirements, and constraints of the project.

This stage is divided into two parts:

**1. Business Requirements:**
-   What are the high-level goals?
-   How will we measure success? (e.g., "reduce processing time by 90%," "increase user engagement by 15%").

**2. Logistical Requirements (The Project Management Triangle):**
-   **Scope/Quality:** What is the minimum viable product? What level of quality is required?
-   **Time:** What is the delivery deadline?
-   **Cost:** What is the budget? How many people will work on this?

You must make conscious trade-offs between these three constraints.

The output of this stage is a **Requirements Document** that clearly defines what will be built and what the success criteria are.

--- 

## Point 3: The Operational Questions (The HOW)

This is the final point, where most engineers are most comfortable. It focuses on the technical implementation details.

This point is also divided into two parts:

**1. Product Requirements (Features):**
-   What are the specific features the user will interact with? (e.g., buttons, forms, offline mode).
-   What is the desired user experience?

**2. Technical Requirements (Architecture):**
-   Will this be deployed on the cloud or on-premise?
-   What technology stack will be used?
-   What is the expected load? (e.g., users per second, data volume).
-   What are the data models and database schemas?
-   What design patterns will be used?

The output of this stage is a **Technical Design Document** that provides a blueprint for the engineering team to build the system.
