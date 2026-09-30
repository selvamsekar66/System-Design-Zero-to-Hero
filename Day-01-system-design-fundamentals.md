# Day 1 — System Design Fundamentals

> **Goal:** Understand what system design is, why it matters, and the basic concepts you need before starting system design preparation.

The most important idea for Day 1:

> **System design is about deciding how different parts of a system should work together to solve a problem.**

You don't need to learn every technology before starting system design.

First, understand the **fundamentals and the reasoning behind the architecture**.

---

## Table of Contents

- [1. What Is System Design?](#1-what-is-system-design)
- [2. Why Do We Need System Design?](#2-why-do-we-need-system-design)
- [3. HLD vs LLD](#3-hld-vs-lld)
- [4. Basic Building Blocks of a System](#4-basic-building-blocks-of-a-system)
- [5. Functional vs Non-Functional Requirements](#5-functional-vs-non-functional-requirements)
- [6. What Makes a Good System?](#6-what-makes-a-good-system)
- [7. Why Do Trade-offs Matter?](#7-why-do-trade-offs-matter)
- [8. Why Should We Think About Failures?](#8-why-should-we-think-about-failures)
- [9. What Does Scalability Mean?](#9-what-does-scalability-mean)
- [10. Simple Example](#10-simple-example)
- [11. Beginner System Design Mindset](#11-beginner-system-design-mindset)
- [12. Quick Revision](#12-quick-revision)
- [13. Self-Test](#13-self-test)

---

## 1. What Is System Design?

System design is the process of deciding how different technical components should work together to solve a business or user problem.

Think of it like creating a blueprint before building a house.

A software system also needs a blueprint that explains:

- What components are needed
- How those components communicate
- Where data is stored
- How requests flow through the system
- How the system can handle growth
- What happens when something fails

A simple system may look like:

```text
User
  |
  v
Application
  |
  v
Database

A larger system may contain many more components.

Users
  |
  v
Load Balancer
  |
  v
Application Servers
  |
  +------> Cache
  |
  +------> Database
  |
  +------> Message Queue
```

The goal is not to use as many components as possible.

The goal is to build a system that satisfies its requirements.

Start with the problem, not the technology.

## 2. Why Do We Need System Design?

A simple application may work well when only a few users use it.

As the system grows, new challenges appear.

For example:

```text
10 users
   ↓
1,000 users
   ↓
100,000 users
   ↓
10 million users
```

The original design may no longer be enough.

System design helps us think about:

- Handling more users
- Handling more requests
- Keeping the system responsive
- Storing increasing amounts of data
- Handling failures
- Keeping the system available
- Protecting data
- Controlling complexity and cost

### Simple Example

Imagine a small application running on one server:

```text
Users
  |
  v
One Server
  |
  v
Database
```

What happens if the server fails?

The entire application may become unavailable.

System design helps us think about these problems before they become production issues.

System design becomes important when a simple solution is no longer enough.

## 3. HLD vs LLD

System design is often discussed at two levels.

### High-Level Design (HLD)

HLD focuses on the big picture.

It describes:

- Major components
- Services
- Databases
- APIs
- Communication between components
- Deployment architecture

Example:

```text
Users
  |
  v
Load Balancer
  |
  v
Application
  |
  v
Database
```

HLD answers:

How is the overall system structured?

### Low-Level Design (LLD)

LLD focuses on implementation details inside individual components.

It may include:

- Classes
- Objects
- Methods
- Interfaces
- Design patterns
- Detailed code structure

For example:

```text
OrderService
    |
    +-- createOrder()
    +-- updateOrder()
    +-- cancelOrder()
```

LLD answers:

How is an individual component implemented?

### Easy Way to Remember

HLD → Big picture

LLD → Implementation details

For system design preparation, start by becoming comfortable with HLD.

## 4. Basic Building Blocks of a System

You don't need to memorize every technology.

First understand the purpose of the common building blocks.

### 4.1 Client

The client is what the user interacts with.

Examples:

- Web browser
- Mobile application
- Desktop application

```text
User
  |
  v
Browser / Mobile App
```

### 4.2 Application Server

The application server contains the business logic of the system.

For example, when a user places an order, the application may:

```text
Receive request
      ↓
Validate request
      ↓
Apply business rules
      ↓
Save order
      ↓
Return response
```

### 4.3 Database

A database stores data that the application needs to keep.

Examples:

- Users
- Orders
- Products
- Payments

```text
Application
     |
     v
 Database
```

Examples of databases include:

- PostgreSQL
- MySQL
- MongoDB
- DynamoDB

You don't need to learn database selection in Day 1.

For now, understand:

A database provides persistent storage for application data.

### 4.4 Cache

A cache stores frequently accessed data so it can be returned faster.

```text
Application
     |
     v
   Cache
```

For example:

```text
Application
     |
     v
  Cache
     |
     +-- Data found → Return quickly
     |
     +-- Data not found → Database
```

The main idea is:

Cache = temporary storage used to improve access speed and reduce load on the main data store.

Caching will be covered in more detail later.

### 4.5 Load Balancer

A load balancer distributes incoming requests across multiple application servers.

```text
             Load Balancer
              /    |    \
             v     v     v
          Server Server Server
             1      2      3
```

Instead of sending every request to one server, traffic can be distributed across multiple servers.

This can help with:

- Handling more traffic
- Improving availability
- Using multiple application servers

### 4.6 Message Queue

A message queue allows one part of a system to send work to another part for processing.

```text
Application
     |
     v
Message Queue
     |
     v
Worker
```

This can be useful when some work does not need to happen immediately.

For example:

```text
User places order
       |
       v
Order created
       |
       v
Message Queue
       |
       +----> Send Email
       |
       +----> Update Notification
```

The detailed concepts of queues and asynchronous processing will be covered later.

### 4.7 CDN

A Content Delivery Network (CDN) helps deliver static content such as:

- Images
- JavaScript files
- CSS files
- Videos

from locations closer to users.

For example:

```text
User
  |
  v
 CDN
  |
  v
Static Content
```

The main idea is:

CDN = Deliver content closer to users to improve performance.

### 4.8 DNS

DNS helps translate a domain name into an IP address.

For example:

```text
example.com
     |
     v
DNS
     |
     v
IP Address
```

This allows users to access services using names instead of remembering IP addresses.

### Basic System View

Putting the basic components together:

```text
                User
                  |
                  v
              Client
                  |
                  v
                DNS
                  |
                  v
           Load Balancer
                  |
                  v
          Application Servers
             /      |      \
            v       v       v
         Cache   Database   Queue
                            |
                            v
                          Worker
```

Don't try to memorize this architecture.

Understand what each component is responsible for.

## 5. Functional vs Non-Functional Requirements

Before designing a system, we need to understand its requirements.

There are two important categories.

### Functional Requirements

Functional requirements describe:

What should the system do?

For a social media application, examples could be:

- User registration
- User login
- Upload a photo
- Follow another user
- Like a post
- Comment on a post
- View a feed

These describe the actual functionality of the product.

### Non-Functional Requirements

Non-functional requirements describe:

How well should the system work?

Examples:

- Performance
- Scalability
- Availability
- Reliability
- Security
- Maintainability

For example:

Functional:
Users can upload photos.

Non-functional:
Photo upload should complete quickly
and the system should remain available
during high traffic.

### Easy Way to Remember

```text
Functional Requirement
        ↓
What does the system do?

Non-Functional Requirement
        ↓
How well does it do it?
```

Functional requirements define what we build. Non-functional requirements influence how we build it.

## 6. What Makes a Good System?

A good system should satisfy its requirements while remaining practical to operate and maintain.

Some important qualities are:

### Scalability

Can the system handle increasing users and traffic?

### Availability

Can users access the system when they need it?

### Reliability

Does the system consistently perform the intended operation correctly?

### Performance

Does the system respond within an acceptable amount of time?

### Security

Can we protect the system and its data?

### Maintainability

Can engineers understand, change, and troubleshoot the system?

These qualities often influence each other.

For example:

```text
Higher scalability
       |
       v
May require more infrastructure
       |
       v
More operational complexity
```

This leads to an important system design concept:

There is usually no perfect design.

## 7. Why Do Trade-offs Matter?

A trade-off means accepting one disadvantage to gain another benefit.

For example:

### More Availability

Adding multiple servers can improve availability.

But:

```text
More servers
     ↓
More infrastructure
     ↓
Higher cost and complexity
```

### More Performance

Adding a cache can reduce latency.

But:

```text
Cache
  ↓
Possible stale data
  ↓
More complexity
```

### More Services

Breaking an application into multiple services can allow independent scaling.

But:

```text
More services
      ↓
More communication
      ↓
More operational complexity
```

The important question is not:

"Which technology is the best?"

Instead ask:

"Which solution fits our requirements, and what trade-offs does it introduce?"

## 8. Why Should We Think About Failures?

Production systems can fail.

Examples include:

- Application server failure
- Database failure
- Network failure
- Dependency failure
- Hardware failure

A beginner often thinks:

"What happens when everything works?"

A system designer should also ask:

"What happens when something fails?"

For example:

```text
Server 1
   X
   |
   v

Load Balancer
   |
   +----> Server 2
   |
   +----> Server 3
```

If Server 1 fails, the system may continue using the other servers.

The exact techniques for handling failures will be covered later.

For Day 1, develop the mindset:

Every important component can fail. Think about what happens next.

## 9. What Does Scalability Mean?

Scalability means the system can handle increasing workload as demand grows.

For example:

```text
100 users
   ↓
1,000 users
   ↓
100,000 users
   ↓
10 million users
```

A system designed for 100 users may not automatically work well for 10 million users.

As the system grows, we may need to change the architecture.

For example:

### Small System

```text
Users
  |
  v
Application
  |
  v
Database
```

A larger system may require:

```text
Users
  |
  v
Load Balancer
  |
  +----> Application
  |
  +----> Application
  |
  +----> Application
             |
             v
          Database
```

For now, remember:

Scalability is about preparing the system to handle increasing workload.

Detailed scaling techniques will be covered later.

## 10. Simple Example

Let's take a simple online shopping application.

A beginner might start with:

```text
User
  |
  v
Application
  |
  v
Database
```

The application handles:

- Product search
- Product details
- Cart
- Orders

As the system grows, additional components may be needed:

```text
                  Users
                    |
                    v
             Load Balancer
                    |
                    v
             Application
              /    |    \
             v     v     v
          Cache  Database Queue
                         |
                         v
                       Worker
```

The important thing is not memorizing this diagram.

Instead, ask:

- What does each component do?
- Why is it needed?
- What happens if it fails?
- What happens when traffic increases?

These questions will become more important as you continue learning system design.

## 11. Beginner System Design Mindset

As you start learning system design, develop these habits.

### 1. Start With the Problem

Don't start with:

"Which database should I use?"

Start with:

"What problem am I solving?"

### 2. Understand Before Choosing Technology

Instead of memorizing:

- Redis
- Kafka
- MongoDB
- Kubernetes

understand:

- Caching
- Messaging
- Data Storage
- Container Orchestration

Then learn the technologies that implement those concepts.

### 3. Ask "Why?"

Whenever you see a component, ask:

Why is it needed?

For example:

- Why do we need a cache?
- Why do we need multiple servers?
- Why do we need a queue?
- Why do we need a database?

### 4. Keep the Design Simple

A system does not become better just because it has more components.

Start with the simplest design that satisfies the requirements.

Then add complexity only when there is a reason.

### 5. Think About Growth

Ask:

What happens when the number of users increases?

### 6. Think About Failure

Ask:

What happens when an important component fails?

### 7. Understand Trade-offs

Ask:

What do we gain and what do we give up with this decision?

These habits are more important than memorizing architecture diagrams.

## 12. Quick Revision

| Topic | Key Idea |
| --- | --- |
| System Design | Deciding how system components work together to solve a problem |
| HLD | The big picture of the system |
| LLD | Detailed implementation of individual components |
| Client | Interface used by the user |
| Application Server | Runs business logic |
| Database | Stores persistent data |
| Cache | Stores frequently accessed data for faster access |
| Load Balancer | Distributes traffic across servers |
| Message Queue | Allows components to communicate asynchronously |
| CDN | Delivers static content closer to users |
| DNS | Resolves domain names to IP addresses |
| Functional Requirement | What the system should do |
| Non-Functional Requirement | How well the system should work |
| Scalability | Ability to handle increasing workload |
| Availability | Ability to remain accessible |
| Reliability | Ability to perform the intended operation correctly |
| Trade-off | Accepting one limitation to gain another benefit |
| Failure Thinking | Asking what happens when a component fails |

## 13. Self-Test

Before moving to Day 2, try answering these questions without looking at your notes.

### Basic Understanding

- What is system design?
- Why do we need system design?
- What is the difference between HLD and LLD?
- What is the role of an application server?
- Why do we need a database?
- What is a cache?
- What does a load balancer do?
- What is the purpose of a message queue?
- What does a CDN do?
- What does DNS do?

### Requirements

- What is a functional requirement?
- What is a non-functional requirement?
- Give two examples of each.

### System Design Thinking

- What does scalability mean?
- Why should we think about failures?
- What is a trade-off?
- Why isn't there always one perfect architecture?
- Why should we ask "Why do we need this component?"
- Why should we start with a simple design?
- What are the important qualities of a good system?

## Final Day 1 Mental Model

You don't need to memorize everything from Day 1.

Remember this:

```text
                SYSTEM DESIGN
                     |
        +------------+------------+
        |            |            |
    Requirements  Components    Quality
        |            |            |
        |            |            +-- Performance
        |            |            +-- Scalability
        |            |            +-- Availability
        |            |            +-- Reliability
        |            |            +-- Security
        |            |
        |            +-- Client
        |            +-- Application
        |            +-- Database
        |            +-- Cache
        |            +-- Load Balancer
        |            +-- Queue
        |
        +-- Functional
        +-- Non-Functional

                     |
                     v

               Trade-offs
                     |
                     v

             Simple, Practical
                Architecture
```

Understand the problem. Understand the components. Understand the requirements. Think about growth and failure. Then make decisions based on trade-offs.

Day 2 will build on this foundation by learning how to approach a system design problem step by step.