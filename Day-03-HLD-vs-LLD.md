# Day 3 — High-Level Design vs Low-Level Design

> **Goal:** Understand the difference between HLD and LLD, what to focus on at each level, and how they work together when designing a system.

> [!IMPORTANT]
> The main idea for Day 3 is simple:
>
> **HLD = Zoom out and understand the overall system**
>
> **LLD = Zoom in and understand how one component works internally**

You don't need to learn complex design patterns or implementation techniques at this stage.

For now, focus on understanding:

- What HLD means
- What LLD means
- When to use HLD
- When to use LLD
- How HLD and LLD are connected
- How to think about them in system-design interviews

---

## Table of Contents

- [1. HLD vs LLD — The Big Picture](#1-hld-vs-lld--the-big-picture)
- [2. What is HLD?](#2-what-is-hld)
- [3. What is LLD?](#3-what-is-lld)
- [4. HLD vs LLD — Quick Comparison](#4-hld-vs-lld--quick-comparison)
- [5. What Does HLD Focus On?](#5-what-does-hld-focus-on)
- [6. What Does LLD Focus On?](#6-what-does-lld-focus-on)
- [7. How HLD and LLD Connect](#7-how-hld-and-lld-connect)
- [8. Example — URL Shortener](#8-example--url-shortener)
- [9. HLD Example — URL Shortener](#9-hld-example--url-shortener)
- [10. LLD Example — URL Shortener](#10-lld-example--url-shortener)
- [11. When Should I Use HLD vs LLD?](#11-when-should-i-use-hld-vs-lld)
- [12. HLD Thinking in System Design Interviews](#12-hld-thinking-in-system-design-interviews)
- [13. Common Mistakes](#13-common-mistakes)
- [14. Day 3 Quick Revision](#14-day-3-quick-revision)
- [15. Day 3 Self-Test](#15-day-3-self-test)
- [16. Key Takeaway](#16-key-takeaway)

---

## 1. HLD vs LLD — The Big Picture

When designing a system, there are two different levels at which we can think.

```text
                    System
                      │
              ┌───────┴───────┐
              │               │
             HLD             LLD
              │               │
         Zoom Out         Zoom In
              │               │
       Overall System    Internal Details
```

### HLD — High-Level Design

HLD looks at the overall system.

It answers questions like:

- What components do we need?
- What services do we need?
- Where should the data be stored?
- How do components communicate?
- How does the request flow through the system?
- What happens when traffic increases?
- What happens if a major component fails?

### LLD — Low-Level Design

LLD looks inside a specific component.

It answers questions like:

- What classes do we need?
- What methods do we need?
- What interfaces are required?
- How should the component work internally?
- How should the code be structured?

> [!TIP]
> A simple way to remember:
>
> HLD = What does the system look like?
>
> LLD = How does a specific component work?

## 2. What is HLD?

HLD = High-Level Design

Think of HLD as looking at a system from a distance.

You are not thinking about individual classes or methods yet.

Instead, you are thinking about the major building blocks.

For example:

```text
User
  ↓
API
  ↓
URL Service
  ↓
Cache
  ↓
Database
```

At this level, we are asking:

- What components exist?
- Why do we need them?
- How do they communicate?
- How does data move through the system?
- Where could scalability become a problem?
- What happens if something fails?

> [!IMPORTANT]
> HLD = Overall system structure and how major components work together.

HLD usually focuses on:

- Services
- APIs
- Databases
- Cache
- Queues
- Load balancing
- Request flow
- Data flow
- Scalability
- Reliability
- Major failure points

The goal is to understand the big picture.

## 3. What is LLD?

LLD = Low-Level Design

Now we zoom into one of the components from our HLD.

For example:

URL Service

At HLD level, we only know that the URL Service exists.

At LLD level, we start asking:

How should this URL Service actually work?

We may define something like:

```text
URLShortener
│
├── shorten()
├── expand()
└── generateCode()
```

Now we are thinking about:

- Classes
- Methods
- Interfaces
- Objects
- Data structures
- Internal implementation

> [!IMPORTANT]
> LLD = Internal design and implementation of a specific component.

## 4. HLD vs LLD — Quick Comparison

| Area | HLD | LLD |
| --- | --- | --- |
| Full Form | High-Level Design | Low-Level Design |
| Focus | Overall system | Individual component |
| Scope | Multiple services/components | Service/module/class |
| Main Question | How is the system structured? | How does this component work? |
| Audience | Architects, Tech Leads, Engineers | Developers |
| Typical Artifacts | Architecture diagrams, data flow | Class diagrams, interfaces, methods |
| Focus Area | Services and communication | Classes and implementation |
| Example | URL Service + Cache + Database | URLShortener class |
| Interview Type | System Design | LLD / OOD |

### Easy Mental Model

Think about building a house.

#### HLD

- How many floors?
- Where are the rooms?
- Where are the stairs?
- Where are the entrances?

#### LLD

- How does the door work?
- How does the lock work?
- How are the switches connected?

So:

HLD = Structure

LLD = Implementation

## 5. What Does HLD Focus On?

You have already started learning many of these concepts in Day 1 and Day 2.

Day 3 is about understanding them specifically from the HLD perspective.

### 5.1 Components

First identify the major building blocks.

For example:

```text
Users
  ↓
Load Balancer
  ↓
Application
  ↓
Cache
  ↓
Database
```

Each component should have a reason for existing.

Ask:

Why do we need this component?

Don't add components just because they are popular technologies.

### 5.2 Service Boundaries

Ask:

What functionality belongs to which service?

For example:

```text
E-Commerce System

Product Service
Cart Service
Order Service
Payment Service
```

The goal is not to create as many services as possible.

First understand the functionality and requirements.

> [!TIP]
> Start with the problem and requirements first. Then decide what components or services are actually needed.

### 5.3 Data Flow

Ask:

How does a request travel through the system?

For example:

```text
User
  ↓
Load Balancer
  ↓
Application
  ↓
Cache
  ↓
Database
  ↓
Response
```

You should be able to explain what happens at each step.

A diagram alone is not enough.

You should understand the flow.

### 5.4 Communication

Components need to communicate with each other.

For example:

```text
Service A
    │
    ↓
Service B
```

At this stage, don't worry about learning every communication technology.

The important question is:

Why do these components need to communicate?

### 5.5 Scalability

Ask:

Which component could become a bottleneck when traffic increases?

For example:

```text
             Load Balancer
             /     |     \
            ↓      ↓      ↓
         Server  Server  Server
```

Instead of depending on a single application instance, multiple instances can handle more traffic.

This is part of HLD thinking.

### 5.6 Failure

For every important component, ask:

What happens if this component fails?

For example:

```text
Application 1  ❌
       │
       X
       │
Application 2
       │
       ↓
   Database
```

If one application instance fails, another instance may continue serving requests.

You don't need to design every failure scenario yet.

Just develop the habit of asking:

What happens if this component fails?

## 6. What Does LLD Focus On?

LLD starts when we zoom into a specific component.

Suppose our HLD contains:

URL Service

Now we ask:

How should the URL Service work internally?

We might define:

```text
URLShortener
│
├── shorten(url)
├── expand(code)
└── generateCode(url)
```

We might also have:

```text
URLRepository
│
├── save()
└── find()
```

```text
Cache
│
├── get()
└── set()
```

Now we are getting closer to implementation.

LLD focuses on things such as:

- Classes
- Methods
- Interfaces
- Objects
- Internal logic
- Data structures

> [!NOTE]
> You don't need to go deep into design patterns at this stage.

The important idea is simply:

HLD shows the component. LLD explains the inside of the component.

## 7. How HLD and LLD Connect

HLD and LLD are not two completely separate designs.

LLD is a deeper view of a part of the HLD.

Think of it like zooming into a map.

```text
                         HLD
                          │
             ┌────────────┼────────────┐
             ↓            ↓            ↓
            API      URL Service       DB
                          │
                          ↓
                         LLD
                          │
                ┌─────────┼─────────┐
                ↓         ↓         ↓
             Class A    Class B    Class C
```

The flow

A practical approach is:

```text
Problem
   ↓
Requirements
   ↓
HLD
   ↓
Major Components
   ↓
Choose a Component
   ↓
LLD
   ↓
Classes / Methods / Interfaces
```

This connects directly with the system-design approach from Day 2.

> [!IMPORTANT]
> Don't start by designing every class.
>
> First understand the overall system.
>
> Then zoom into the component that needs more detail.

## 8. Example — URL Shortener

Let's use a simple URL shortener to understand both HLD and LLD.

### Requirement

Users should be able to:

```text
Long URL
   ↓
Short URL
```

And:

```text
Short URL
   ↓
Original URL
```

For example:

```text
https://example.com/very/long/url
                ↓
        https://short.ly/abc123
```

The exact implementation is not the important part here.

The important part is understanding how we look at the system at different levels.

## 9. HLD Example — URL Shortener

At HLD level, we think about the major components.

```text
              User
                │
                ↓
           API / Gateway
                │
                ↓
         URL Shortener
           /        \
          ↓          ↓
       Cache      Database
```

The main questions are:

- Where does the request enter?
- Which service handles the request?
- Where is the URL stored?
- Can frequently accessed URLs be cached?
- What happens if the cache is unavailable?
- What happens if traffic increases?

This is HLD thinking.

### 9.1 Create Short URL — Request Flow

When a user creates a short URL:

```text
User
  ↓
API
  ↓
URL Service
  ↓
Database
  ↓
Short URL returned
```

The URL service generates the short identifier and stores the relationship between:

Short URL → Original URL

### 9.2 Open Short URL — Request Flow

When a user opens the short URL:

```text
User
  ↓
API
  ↓
URL Service
  ↓
Cache
  ↓
Database if cache misses
  ↓
Original URL
```

The important thing is understanding the request and data flow.

> [!TIP]
> When explaining an HLD, don't just draw boxes.
>
> Walk through one request from the user to the response.

## 10. LLD Example — URL Shortener

Now let's zoom inside the:

URL Service

At LLD level, we may have:

```text
URLShortener
│
├── shorten()
├── expand()
└── generateCode()
```

We could also have:

```text
URLRepository
│
├── save()
└── find()
```

And:

```text
Cache
│
├── get()
└── set()
```

Now we are defining the internal structure of the service.

```text
HLD
User
  ↓
URL Service
  ↓
Database
LLD
URL Service
     │
     ├── URLShortener
     │      ├── shorten()
     │      ├── expand()
     │      └── generateCode()
     │
     ├── URLRepository
     │      ├── save()
     │      └── find()
     │
     └── Cache
            ├── get()
            └── set()
```

Same system. Different level of detail.

## 11. When Should I Use HLD vs LLD?

### Use HLD when:

- Starting a new system
- Discussing overall architecture
- Designing multiple services
- Thinking about scalability
- Thinking about data flow
- Discussing system-level failures
- Preparing for a system-design interview

### Use LLD when:

- Designing a specific service
- Designing classes or modules
- Defining methods
- Defining interfaces
- Discussing implementation details
- Solving an object-oriented design problem
- Preparing for an LLD/OOD interview

### Simple Rule

> [!TIP]
> "What components do we need?" → HLD
>
> "How should this component work?" → LLD

## 12. HLD Thinking in System Design Interviews

For system-design interviews, don't jump into classes and methods too early.

Suppose the interviewer asks:

"Design a URL shortener."

A good starting flow is:

1. Understand the problem
2. Identify requirements
3. Understand scale
4. Identify major components
5. Explain data flow
6. Decide where data is stored
7. Think about scalability
8. Think about failures
9. Discuss trade-offs

This is primarily HLD thinking.

Only go deeper into:

- Classes
- Methods
- Interfaces
- Implementation

when the interviewer asks for LLD or wants you to zoom into a particular component.

> [!IMPORTANT]
> Start broad → explain the architecture → then zoom in when needed.

## 13. Common Mistakes

### Mistake 1 — Starting With Code

When asked:

"Design an e-commerce system."

Don't immediately start with:

```text
class Order
class Product
class Customer
```

You are jumping into LLD.

Instead, first understand the system:

```text
Users
  ↓
Application
  ↓
Services
  ↓
Database
```

Then go deeper if required.

### Mistake 2 — Starting With Technologies

Don't immediately start saying:

```text
Kafka
Redis
MongoDB
Kubernetes
```

Instead ask:

What problem are we trying to solve?

Then decide which component or technology makes sense.

> [!TIP]
> This follows an important lesson from Day 1 and Day 2:
>
> Requirements first → Architecture next → Technology choices after that.

### Mistake 3 — Creating Too Many Services

Don't turn every feature into a separate service just because you can.

Start simple.

```text
Users
  ↓
Application
  ↓
Database
```

Then introduce additional components when there is a clear reason.

### Mistake 4 — Ignoring Data Flow

A diagram full of boxes isn't enough.

You should be able to explain:

"The request comes here, then goes there, this component reads the data, and finally the response comes back."

For example:

```text
User
 ↓
API
 ↓
Service
 ↓
Cache
 ↓
Database
 ↓
Response
```

### Mistake 5 — Ignoring Failure

Don't explain only the happy path.

Ask:

- What if the cache fails?
- What if the database fails?
- What if one application instance fails?

You started learning this mindset in Day 1 and Day 2.

Keep carrying it forward.

## 14. Day 3 Quick Revision

If you have only two minutes before moving to Day 4, remember these points.

| Topic | Remember |
| --- | --- |
| HLD | Overall system structure |
| LLD | Internal implementation of a component |
| HLD question | What components do we need? |
| LLD question | How does this component work? |
| HLD scope | Multiple services/components |
| LLD scope | Service/module/class |
| HLD diagram | Services, databases, APIs, communication |
| LLD diagram | Classes, interfaces, methods |
| HLD focus | System-level architecture |
| LLD focus | Implementation details |
| Relationship | LLD is a deeper view of an HLD component |
| Interview mindset | Start broad, then zoom in |

### One-line memory trick

- HLD = Zoom Out
- LLD = Zoom In

## 15. Day 3 Self-Test

Before moving to Day 4, try answering these without looking at your notes.

### Basic Understanding

- What is HLD?
- What is LLD?
- What is the main difference between HLD and LLD?
- What kind of questions does HLD answer?
- What kind of questions does LLD answer?

### Architecture Thinking

- What kind of components would you normally show in an HLD diagram?
- Why is data flow important in HLD?
- Why should we think about failures while designing a system?
- Why shouldn't we start with technologies immediately?

### HLD vs LLD

- How are HLD and LLD connected?
- If you have an Order Service in your HLD, what would you design when you zoom into that service?
- If an interviewer asks for the overall architecture, should you start with classes and methods? Why?

### Practical Exercise

Try this:

Design a simple URL shortener at HLD level.

Draw:

```text
User
  ↓
?
  ↓
?
  ↓
?
```

Then explain:

- What each component does
- Why you need it
- How the request flows
- Where the data is stored
- What happens if one component fails

Then pick one component and ask:

"If I zoom into this component, what would its LLD look like?"

## 16. Key Takeaway

The most important thing to remember from Day 3 is:

> [!IMPORTANT]
> Don't start with implementation. Start with the system.

Use this mental model:

```text
                    Problem
                       ↓
                 Requirements
                       ↓
                      HLD
                       ↓
              Major Components
                       ↓
                Choose a Component
                       ↓
                      LLD
                       ↓
             Classes / Methods /
                  Interfaces
```

### Remember

```text
┌───────────────────────────────┐
│            HLD                │
│                               │
│        Zoom OUT               │
│                               │
│   Overall System              │
│   Services                    │
│   Data Flow                   │
│   Architecture                │
│   Scalability                 │
│   Failures                    │
└───────────────┬───────────────┘
                │
                │ Zoom In
                ↓
┌───────────────────────────────┐
│            LLD                │
│                               │
│   Individual Component        │
│   Classes                     │
│   Methods                     │
│   Interfaces                  │
│   Implementation              │
└───────────────────────────────┘
```

HLD tells us what the system looks like.

LLD tells us how a specific part of that system works.

### Day 3 — Final Memory Line

HLD = Zoom Out → Understand the System

LLD = Zoom In → Understand the Component