# Day 2 — System Design Approach

**Goal:** Learn a simple and repeatable way to approach a system design problem.

The most important lesson for Day 2 is:

> Understand the problem first. Design the solution second.

Do not start by choosing technologies such as databases, caches, queues, or Kubernetes.

Start by asking:

```text
What are we building?
        ↓
What does the system need to do?
        ↓
How big is the system?
        ↓
What should the architecture look like?
        ↓
What are the difficult parts?
        ↓
How will the system handle growth and failures?
        ↓
Why did we make these choices?
```

## Table of Contents

1. [What Interviewers Evaluate](#1-what-interviewers-evaluate)
2. [The RESHADED Framework](#2-the-reshaded-framework)
3. [How to Structure a 45-Minute Interview](#3-how-to-structure-a-45-minute-interview)
4. [Clarifying Requirements](#4-clarifying-requirements)
5. [Estimation](#5-estimation)
6. [System Interface](#6-system-interface)
7. [High-Level Design](#7-high-level-design)
8. [API and Data Model](#8-api-and-data-model)
9. [Deep Dive](#9-deep-dive)
10. [Evolution and 10× Growth](#10-evolution-and-10x-growth)
11. [Trade-offs](#11-trade-offs)
12. [Common Mistakes](#12-common-mistakes-to-avoid)
13. [Beginner System Design Checklist](#13-beginner-system-design-checklist)
14. [Key Takeaways](#14-key-takeaways)

---

## 1. What Interviewers Evaluate

A system design interview is not mainly about remembering technologies.

The interviewer wants to understand how you think about a problem.

There are five important areas.

### 1.1 Problem Clarity

Can you understand the problem before proposing a solution?

For example, instead of immediately designing an application, first ask:

- Who will use it?
- What should the system do?
- What are the most important features?
- How many users are expected?

### 1.2 Breadth of Knowledge

Do you understand the purpose of common system components?

For example:

- Database
- Cache
- Load balancer
- Message queue
- Application server

You don't need to know every technology in detail.

You should understand when and why a component may be needed.

### 1.3 Structured Thinking

Can you approach the problem step by step?

Avoid jumping randomly between technologies.

A better approach is:

```text
Requirements
     ↓
Estimation
     ↓
Interface
     ↓
High-Level Design
     ↓
Deep Dive
     ↓
Evolution
     ↓
Trade-offs
```

### 1.4 Trade-off Awareness

There is usually no single perfect architecture.

Every decision has advantages and disadvantages.

For example:

```text
Caching
   ↓
Lower latency
   ↓
But
   ↓
Possible stale data
```

A good answer explains both sides.

### 1.5 Scalability Thinking

You should be able to think beyond the current requirements.

Ask:

What happens if the number of users or requests increases significantly?

For example:

```text
1,000 requests/sec
        ↓
       10×
        ↓
10,000 requests/sec
```

The architecture may need to change as the system grows.

---

## 2. The RESHADED Framework

A simple way to remember the system design process is:

- R → Requirements
- E → Estimation
- S → System Interface
- H → High-Level Design
- A → API & Data Model
- D → Deep Dive
- E → Evolution
- D → Discussion

This gives you a structured path from the initial problem to the final discussion.

### R — Requirements

First, understand what needs to be built.

Ask:

- Who are the users?
- What problem are we solving?
- What are the important features?
- What is the expected scale?
- What is out of scope?

### Example

Suppose you are asked to design an e-commerce system.

Start by identifying the main user actions:

```text
User
 ├── Search products
 ├── View product
 ├── Add to cart
 ├── Place order
 ├── Make payment
 └── Track order
```

You don't need to design the entire system immediately.

First understand the core use cases.

### E — Estimation

Next, understand how large the system needs to be.

Think about:

- Number of users
- Requests per second
- Peak traffic
- Read/write ratio
- Storage
- Bandwidth

For example:

```text
Users             → 10 million
Average traffic   → 5,000 requests/sec
Peak traffic      → 20,000 requests/sec
Read/Write ratio  → 90:10
```

These numbers are estimates.

They don't need to be perfectly accurate.

The purpose is to understand the rough size of the problem.

Estimation helps us understand how big the system needs to be.

Detailed capacity calculations can be learned separately.

### S — System Interface

Now think about how users or other systems will interact with your system.

For example:

```text
Client
   ↓
API
   ↓
Application
```

You may identify important operations such as:

- Create order
- Get order
- Cancel order
- Update order

At this stage, focus on the main interactions.

You don't need to design every API.

### H — High-Level Design

Now create a simple architecture using the major components.

For example:

```text
             Users
                |
                ↓
          Load Balancer
                |
                ↓
        Application Servers
          /      |       \
         ↓       ↓        ↓
      Cache   Database   Queue
                           |
                           ↓
                        Workers
```

The purpose of a high-level design is to show:

- What the major components are
- How they communicate
- How a request moves through the system

Don't worry about implementation details yet.

For every component, ask:

Why do we need this?

### A — API & Data Model

Now go one level deeper.

Think about the main APIs and the important data.

Example APIs:

```text
POST /orders
GET  /orders/{id}
DELETE /orders/{id}
```

Example data:

```text
User
 ├── userId
 ├── name
 └── email

Order
 ├── orderId
 ├── userId
 ├── items
 └── status
```

You don't need to design every table, field, or API.

Focus on the data and interfaces required for the main use cases.

---

## 3. How to Structure a 45-Minute Interview

A simple time allocation is:

| Phase | Time | What to Do |
| --- | --- | --- |
| Clarify Requirements | 5 min | Understand users, features and constraints |
| Estimation | 5 min | Estimate traffic, storage and bandwidth |
| High-Level Design | 10 min | Draw the main components and request flow |
| Deep Dive | 15 min | Focus on the 2–3 hardest areas |
| Trade-offs & Wrap-up | 5 min | Explain decisions, alternatives and limitations |

> Important
>
> Don't spend the entire interview on the first step.
>
> Clarify enough to understand the problem, then start designing.

---

## 4. Clarifying Requirements

Before designing, ask questions that can change your architecture.

You don't need to ask every possible question.

Choose the questions that matter for the problem.

### Users and Scale

- How many users do we need to support?
- How many daily active users are expected?
- What is the expected traffic?
- What is the peak traffic?

### Usage

- What are the most important features?
- Is the system mainly read-heavy or write-heavy?
- Which operations are used most frequently?

### Performance

- What latency is expected?
- Are there strict response-time requirements?

### Consistency

- Does the data need to be immediately consistent?
- Is some delay in data updates acceptable?

### Availability

- What availability is required?
- Is downtime acceptable?

### Scope

- What is the most important functionality?
- What can be excluded from the initial design?

> Simple Rule
>
> Don't design the solution until you understand the problem.

---

## 5. Estimation

Estimation gives you an idea of the system's scale.

You may need to estimate:

- Traffic
  - Requests per second
  - Peak requests per second
  - Read requests
  - Write requests
- Storage
  - Data generated per day
  - Data generated per year
  - Data retention
- Bandwidth
  - Request volume
  - Average request/response size

The goal is not perfect mathematics.

The goal is to understand whether you are designing for:

- 1,000 users

or:

- 10 million users

That difference can significantly affect the architecture.

---

## 6. System Interface

The system interface describes how clients and other systems interact with your system.

For example:

```text
User
  ↓
API
  ↓
Application
```

For a simple order system:

```text
POST /orders
GET  /orders/{id}
PUT  /orders/{id}
DELETE /orders/{id}
```

At this stage, focus on the important operations.

You don't need to spend too much time on API details.

The main question is:

What does the outside world need to communicate with our system?

---

## 7. High-Level Design

After understanding the requirements and scale, draw the major components.

Start simple.

```text
User
  ↓
Application
  ↓
Database
```

Then add components only when there is a reason.

For example:

```text
User
  ↓
Load Balancer
  ↓
Application
  ↓
Database
```

If another requirement introduces the need for additional components, add them.

For example:

```text
User
  ↓
Load Balancer
  ↓
Application
  ├── Cache
  ├── Database
  └── Message Queue
```

The important thing is not the number of boxes.

The important thing is:

Can you explain why every important component exists?

---

## 8. API and Data Model

Once the high-level design is clear, think about the main data and interfaces.

For example, for an order system:

```text
API
POST /orders
GET /orders/{id}
```

```text
Order
 ├── orderId
 ├── userId
 ├── items
 ├── totalAmount
 └── status
```

Think about:

- What information needs to be stored?
- How will the application access it?
- What operations need to be supported?

Keep it focused on the requirements.

---

## 9. Deep Dive

After the high-level design, don't spend equal time explaining every component.

Choose the 2–3 most important or difficult areas.

For example:

```text
E-commerce System

1. Order processing
2. Database
3. Payment processing
```

Then explore those areas in more detail.

For each one, ask:

```text
How does it work?
      ↓
What can become a bottleneck?
      ↓
What happens if it fails?
      ↓
How can it scale?
      ↓
What trade-offs exist?
```

This helps you demonstrate deeper system design thinking without trying to explain everything.

---

## 10. Evolution and 10× Growth

A useful question during system design is:

What happens if the system becomes 10× larger?

For example:

```text
Current traffic
1,000 requests/sec

       ↓ 10×

Future traffic
10,000 requests/sec
```

Now think about each major area.

### Application

Can we run more application instances?

### Database

Will the database become the bottleneck?

### Storage

Can it handle the additional data?

### Network

Can it handle the additional traffic?

### Other Components

Can other components handle the increased load?

The goal is not to immediately solve every scaling problem.

The goal is to identify where the current design may stop working.

---

## 11. Trade-offs

There is rarely a perfect system design.

Every important decision has a benefit and a cost.

Use this simple structure:

```text
I chose X
     ↓
because of Y
     ↓
The trade-off is Z
```

### Example

I would use caching because the data is frequently requested. This can reduce latency and database load. The trade-off is that cached data may become stale.

This is much better than simply saying:

> "We'll use Redis."

The interviewer wants to understand your reasoning.

### A Useful Question

Whenever you add a component, ask:

Why do I need this component?

For example:

```text
Need a cache?
     ↓
Why?

Need a queue?
     ↓
Why?

Need multiple servers?
     ↓
Why?

Need a database replica?
     ↓
Why?
```

This habit will help you avoid unnecessary complexity.

---

## 12. Common Mistakes to Avoid

### 1. Jumping to Solutions

Avoid starting with:

> "Let's use Kafka, Redis and MongoDB."

Instead:

> "Let me first understand the requirements and expected scale."

### 2. Not Clarifying Requirements

Don't make assumptions about everything.

Ask questions about:

- Users
- Features
- Scale
- Performance
- Availability
- Scope

### 3. Being Too Vague

Avoid:

> "We'll use a database."

Instead, when you reach the database decision, explain the reason:

> "I would choose a relational database because the system requires structured relationships and transactions."

The important part is the reasoning.

### 4. Ignoring Failure Scenarios

Don't assume everything will always work.

Ask:

- What happens if the application fails?
- What happens if the database fails?
- What happens if a dependency becomes unavailable?
- What happens if traffic suddenly increases?

### 5. Skipping Estimation

Even rough estimates are useful.

The goal is to understand the order of magnitude of the system.

### 6. Adding Too Many Components

More components don't automatically mean better architecture.

Start simple:

```text
Client
  ↓
Application
  ↓
Database
```

Then add components when a requirement justifies them.

---

## 13. Beginner System Design Checklist

Use this checklist whenever you practice a new system design problem.

### Requirements

- What problem are we solving?
- Who are the users?
- What are the core features?
- What is out of scope?
- What are the important constraints?

### Estimation

- How many users?
- What is the request rate?
- What is the peak traffic?
- What is the read/write ratio?
- How much data will be generated?

### System Interface

- What are the main APIs?
- How will users interact with the system?
- How will different parts of the system communicate?

### High-Level Design

- What are the major components?
- How do they communicate?
- What is the request flow?
- Why is each major component needed?

### Deep Dive

- What are the 2–3 hardest components?
- What could become a bottleneck?
- What happens if something fails?
- How could these components scale?

### Evolution

- What happens at 10× traffic?
- Which component becomes the bottleneck?
- What would need to change?

### Trade-offs

- Why did I choose this approach?
- What problem does it solve?
- What trade-off does it introduce?
- What alternative could I consider?

---

## 14. Key Takeaways

- Understand the problem before designing the solution.
- Clarify requirements before choosing technologies.
- Estimate the scale before making major architecture decisions.
- Start with a simple high-level design.
- Focus your deep dive on the most important components.
- Think about what happens when the system grows 10×.
- Every major component should have a reason.
- Explain why you made each important decision.
- Understand the trade-offs of your decisions.
- System design is about structured problem-solving, not memorizing architectures.

## Day 2 Mental Model

Keep this simple flow in your mind:

```text
Understand the Problem
        ↓
Clarify Requirements
        ↓
Estimate the Scale
        ↓
Define the Interface
        ↓
Create High-Level Design
        ↓
Define APIs & Data
        ↓
Deep Dive into Important Areas
        ↓
Think About 10× Growth
        ↓
Explain Trade-offs
```

Understand the problem → identify the constraints → design the solution → explain why → discuss trade-offs.