# Day 2: System Design Approach

> Goal: Learn a repeatable way to approach a system design problem before choosing technologies or drawing complex diagrams.

The most important lesson from this topic is:

> Good system design starts with understanding the problem, not with picking tools.

---

## Table of contents

- [1. How to approach a system design problem](#1-how-to-approach-a-system-design-problem)
- [2. Understand the problem](#2-understand-the-problem)
- [3. Functional requirements](#3-functional-requirements)
- [4. Non-functional requirements](#4-non-functional-requirements)
- [5. Clarify the scale](#5-clarify-the-scale)
- [6. Capacity estimation](#6-capacity-estimation)
- [7. High-level architecture](#7-high-level-architecture)
- [8. Request and data flow](#8-request-and-data-flow)
- [9. Database selection](#9-database-selection)
- [10. Why do we need caching?](#10-why-do-we-need-caching)
- [11. Why do we need messaging?](#11-why-do-we-need-messaging)
- [12. API design](#12-api-design)
- [13. Scalability](#13-scalability)
- [14. What happens when traffic increases 10×?](#14-what-happens-when-traffic-increases-10x)
- [15. Availability](#15-availability)
- [16. Reliability](#16-reliability)
- [17. Fault tolerance](#17-fault-tolerance)
- [18. Security](#18-security)
- [19. Observability](#19-observability)
- [20. SLI and SLO](#20-sli-and-slo)
- [21. Architecture trade-offs](#21-architecture-trade-offs)
- [22. Failure scenarios](#22-failure-scenarios)
- [23. Real-world example: e-commerce](#23-real-world-example--e-commerce)
- [24. Architect checklist](#24-architect-checklist)
- [25. Interview questions](#25-interview-questions)
- [26. What to learn next](#26-what-to-learn-next)
- [Key takeaways](#key-takeaways)

---

## 1. How to approach a system design problem

A clean process looks like this:

```text
1. Understand the problem
   ↓
2. Clarify requirements
   ↓
3. Estimate scale
   ↓
4. Identify core use cases and APIs
   ↓
5. Design the high-level architecture
   ↓
6. Design data and storage
   ↓
7. Think about scalability
   ↓
8. Think about reliability and failures
   ↓
9. Add security and observability
   ↓
10. Explain trade-offs
```

Do not start with:

> "Let's use Kafka, Redis, Kubernetes, and MongoDB."

Instead, ask:

> What are we solving, what are the constraints, and why does each component belong in the design?

---

## 2. Understand the problem

Before designing anything, understand what the system is supposed to do.

Ask yourself:

- Who are the users?
- What problem are we solving?
- What are the most important user journeys?
- What operations should the system support?
- What is out of scope?

### Example

For an e-commerce platform:

```text
User
├── Search products
├── View product
├── Add to cart
├── Place order
├── Make payment
└── Track order
```

Do not try to design the entire Amazon platform at once.

Start with the core use cases and grow from there.

---

## 3. Functional requirements

Functional requirements describe:

> What the system should do.

Example for an e-commerce system:

- user registration and login
- product search
- product details
- add and remove cart items
- place orders
- payment processing
- order tracking

Focus first on the critical functionality.

---

## 4. Non-functional requirements

Non-functional requirements describe:

> How well the system should work.

Important NFRs include:

- scalability
- availability
- reliability
- performance
- security
- durability
- maintainability
- observability

### Easy way to remember

```text
Functional requirements     → WHAT the system does
Non-functional requirements → HOW WELL it does it
```

> [!NOTE]
> Functional requirements define the product. Non-functional requirements define the quality bar.

---

## 5. Clarify the scale

Architecture depends heavily on scale.

Ask:

- How many users do we expect?
- How many daily active users?
- What is the request rate per second?
- What is the peak traffic?
- What is the read/write ratio?
- How much data is generated?
- How quickly is the data growing?
- What should the system support in the future?

### Example

```text
Users              → 10 million
Average traffic    → 5,000 RPS
Peak traffic       → 20,000 RPS
Read/write ratio   → 90:10
```

The exact numbers are not the main point.

The important thing is understanding the order of magnitude.

---

## 6. Capacity estimation

Before choosing infrastructure, estimate:

### Traffic

```text
requests per second
peak requests per second
read vs. write traffic
```

### Storage

```text
data generated per day
data generated per year
retention period
```

### Bandwidth

```text
requests × average response size
```

The goal is not perfect mathematics.

The goal is to answer:

> How big does this system need to be?

---

## 7. High-level architecture

Once requirements and scale are clear, design the simplest architecture that satisfies them.

A basic architecture might look like:

```text
                 Users
                   |
                   ↓
             Load Balancer
                   |
                   ↓
          Application Servers
             /      |      \
            ↓       ↓       ↓
         Cache   Database   Queue
                              |
                              ↓
                           Workers
```

Every component should have a purpose.

Ask:

> Why is this component here?

---

## 8. Request and data flow

Do not just draw boxes.

You should be able to explain what happens when a request enters the system.

Example flow:

```text
User
 ↓
Load Balancer
 ↓
Application Server
 ↓
Cache
 ↓
Database
 ↓
Response
```

### Cache hit

```text
Application
     ↓
   Redis
     ↓
 Cache hit
     ↓
 Response
```

### Cache miss

```text
Application
     ↓
   Redis
     ↓
 Cache miss
     ↓
 Database
     ↓
 Store result in cache
     ↓
 Response
```

Understanding the request flow is more important than memorizing a diagram.

---

## 9. Database selection

Do not choose a database because it is popular.

Choose it based on:

```text
Data model
+
Access pattern
+
Consistency requirements
+
Scale
```

### Relational databases

Examples:

- PostgreSQL
- MySQL
- Oracle

Useful when you need:

- transactions
- strong consistency
- relationships
- structured schema
- complex queries

### NoSQL databases

Examples:

- DynamoDB
- Cassandra
- MongoDB

Useful when you need:

- very high scale
- flexible schema
- distributed storage
- predictable access patterns

### Important question

> Why is this database the right fit for this workload?

---

## 10. Why do we need caching?

Caching is useful when the same data is requested repeatedly.

```text
Client
  ↓
Application
  ↓
Redis
  ↓
Cache hit → return data

Cache miss
  ↓
Database
  ↓
Update cache
  ↓
Return data
```

### Benefits

- lower latency
- reduced database load
- higher throughput

### Problems introduced

- cache invalidation
- stale data
- TTL management
- eviction
- cache consistency
- cache failure

> [!IMPORTANT]
> Caching improves performance but introduces consistency and operational complexity.

---

## 11. Why do we need messaging?

Some operations do not need to happen synchronously.

Example: when an order is placed

```text
Order Service
      |
      ↓
   Message Queue
      |
 ┌────┼──────────┐
 ↓    ↓          ↓
Email Inventory Notification
```

The system can process some work asynchronously instead of making the user wait for every downstream operation.

### Benefits

- decoupling
- asynchronous processing
- better resilience
- better scalability
- absorbs traffic spikes

### New problems

- duplicate messages
- message ordering
- retry handling
- dead-letter queues
- eventual consistency

> [!IMPORTANT]
> Asynchronous processing improves decoupling, but it also increases distributed-system complexity.

---

## 12. API design

Common communication patterns include:

### REST

Good for general-purpose APIs.

```text
GET    /products
POST   /orders
GET    /orders/{id}
DELETE /cart/{itemId}
```

### gRPC

Useful for:

- service-to-service communication
- high-performance communication
- strongly typed contracts

### GraphQL

Useful when clients need flexible querying across many resources.

Do not ask:

> Which technology is best?

Ask:

> Which communication pattern fits this use case?

---

## 13. Scalability

There are two basic approaches.

### Vertical scaling

Increase power in a single machine.

```text
Small server
     ↓
Bigger server
```

Increase:

- CPU
- RAM
- storage

### Limitation

There is a physical and economic limit.

### Horizontal scaling

Add more machines.

```text
             Load Balancer
             /     |     \
            ↓      ↓      ↓
          App-1  App-2  App-3
```

### Benefits

- higher capacity
- better fault tolerance
- easier distributed scaling

For many large systems, horizontal scaling is a major building block.

---

## 14. What happens when traffic increases 10×?

This should become a standard question in system design.

Suppose:

```text
Current traffic → 1,000 RPS
Future traffic  → 10,000 RPS
```

Ask yourself:

### Application

Can we add more instances?

### Load balancer

Can it distribute the increased traffic?

### Database

Will it become the bottleneck?

### Cache

Can we reduce database load?

### Queue

Can async processing absorb demand spikes?

### Storage

Can it handle the increased writes?

### Network

Can bandwidth handle the extra traffic?

### Observability

Can monitoring handle 10× more telemetry?

---

## 15. Availability

Availability asks:

> Can users access the system when they need it?

Example:

```text
Application Server 1 ❌
        |
        ↓
Load Balancer
      /   \
     ↓     ↓
 Server 2 Server 3
```

The failed server must not take down the whole application.

Common techniques:

- multiple application instances
- load balancing
- replication
- multi-AZ deployment
- health checks
- automatic failover

---

## 16. Reliability

Reliability asks:

> Does the system consistently perform the intended operation correctly?

A system can be available but still unreliable.

Example:

```text
HTTP 200
   ↓
Incorrect data
```

The service is technically responding, but the user experience is still broken.

---

## 17. Fault tolerance

Always ask:

> What happens when something fails?

### Application failure

Use multiple instances.

### Database failure

Consider:

- replicas
- failover
- backups
- recovery

### Cache failure

The application may fall back to the database.

### Queue failure

Consider:

- durable messages
- replication
- retry
- dead-letter queue

### Network failure

Consider:

- timeout
- retry
- circuit breaker
- graceful degradation

---

## 18. Security

Security should be considered during design, not added at the end.

Think about:

### Authentication

> Who are you?

### Authorization

> What are you allowed to do?

### Encryption

```text
Data in transit → TLS
Data at rest    → Encryption
```

### Secrets

Never hardcode:

```text
passwords
API keys
cloud credentials
database credentials
```

Use proper secret management.

### Other considerations

- rate limiting
- input validation
- WAF
- least privilege
- audit logging
- network segmentation

---

## 19. Observability

A production architecture is incomplete without observability.

Think about the three pillars:

```text
Metrics
Logs
Traces
```

### Metrics

Examples:

```text
request rate
error rate
latency
CPU
memory
database connections
queue depth
cache hit ratio
```

### Logs

Useful for detailed events and failures.

### Traces

Useful for tracking a request across services.

```text
API
 ↓
Service A
 ↓
Service B
 ↓
Database
```

---

## 20. SLI and SLO

### SLI

Service Level Indicator.

What are we measuring?

Example:

```text
successful requests / total requests
```

### SLO

Service Level Objective.

What target do we want?

Example:

```text
99.9% successful requests
```

Think:

```text
SLI → measurement
SLO → target
```

---

## 21. Architecture trade-offs

There is rarely a perfect architecture.

Every decision has benefits and costs.

| Decision | Benefit | Trade-off |
| --- | --- | --- |
| Cache | Lower latency | Stale data and invalidation |
| Replication | Better availability and read scaling | Replication lag |
| Queue | Decoupling and async processing | Eventual consistency |
| Microservices | Independent scaling | Distributed complexity |
| Strong consistency | More predictable data | Higher latency and lower availability |
| Horizontal scaling | Higher capacity | More infrastructure complexity |

The important interview skill is not saying:

> This technology is better.

Instead, explain:

> I chose this because of X, and the trade-off is Y.

---

## 22. Failure scenarios

For every architecture component, ask:

```text
What happens if it fails?
```

Example:

```text
              Load Balancer
                   |
          ┌────────┴────────┐
          ↓                 ↓
       App-1              App-2
          ❌
                            ↓
                         Database
```

Questions to ask:

- Can the system continue?
- Is there a fallback?
- Is data lost?
- Can the component recover automatically?
- How will users be affected?
- How will engineers know about the issue?

---

## 23. Real-world example: e-commerce

A simplified architecture:

```text
                       Users
                         |
                         ↓
                  Load Balancer
                         |
                         ↓
                    API Layer
                         |
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
     Product Service  Cart Service  Order Service
          |              |              |
          ↓              ↓              ↓
        Cache          Cache         Database
                                         |
                                         ↓
                                    Message Queue
                                         |
                           ┌─────────────┼────────────┐
                           ↓             ↓            ↓
                       Payment       Inventory   Notification
```

Now challenge the design:

- What happens if traffic increases 10×?
- What happens if the database becomes unavailable?
- Which data can be cached?
- Which operations can be asynchronous?
- Which operations require strong consistency?
- Where would you add replicas?
- Where would you add monitoring?
- What happens if the queue is unavailable?

---

## 24. Architect checklist

Use this checklist whenever beginning a new system design problem.

### Requirements

- [ ] What problem are we solving?
- [ ] Who are the users?
- [ ] What are the core use cases?
- [ ] What is out of scope?
- [ ] What are the non-functional requirements?

### Scale

- [ ] Number of users?
- [ ] Requests/sec?
- [ ] Peak traffic?
- [ ] Read/write ratio?
- [ ] Data growth?
- [ ] Storage requirements?

### Architecture

- [ ] API layer?
- [ ] Load balancer?
- [ ] Application or service layer?
- [ ] Cache?
- [ ] Database?
- [ ] Queue?
- [ ] Workers?

### Data

- [ ] SQL or NoSQL?
- [ ] Why?
- [ ] Access patterns?
- [ ] Consistency requirements?
- [ ] Replication?
- [ ] Partitioning?

### Reliability

- [ ] What happens if a service fails?
- [ ] What happens if the database fails?
- [ ] Retry strategy?
- [ ] Timeout strategy?
- [ ] Circuit breaker?
- [ ] Backup and recovery plan?
- [ ] Disaster recovery?

### Security

- [ ] Authentication?
- [ ] Authorization?
- [ ] Encryption?
- [ ] Secrets?
- [ ] Rate limiting?
- [ ] Audit logging?

### Observability

- [ ] Metrics?
- [ ] Logs?
- [ ] Traces?
- [ ] SLIs?
- [ ] SLOs?
- [ ] Alerts?

### Trade-offs

- [ ] Why did I choose this component?
- [ ] What problem does it solve?
- [ ] What complexity does it introduce?
- [ ] What alternative could I use?
- [ ] What happens at 10× scale?

---

## 25. Interview questions

### Beginner

1. What is the difference between functional and non-functional requirements?
2. Why should requirements be clarified before designing?
3. What is horizontal scaling?
4. What is vertical scaling?
5. Why do we use a load balancer?
6. Why do we use caching?
7. Why do we use a message queue?
8. What is synchronous vs. asynchronous communication?
9. When would you choose SQL vs. NoSQL?
10. What is eventual consistency?

### Intermediate

11. What happens when traffic increases 10×?
12. How would you handle a database bottleneck?
13. What happens if Redis goes down?
14. What happens if the message broker goes down?
15. How would you make a service highly available?
16. How would you design for failure?
17. Where would you introduce asynchronous processing?
18. How would you monitor the system?
19. How would you define an SLI and SLO?
20. What trade-offs did you make?

### Architecture-level

21. How do you identify the bottleneck in a distributed system?
22. How do you choose between synchronous and asynchronous processing?
23. When is eventual consistency acceptable?
24. How would you redesign the system for 10× traffic?
25. How would you design for multi-region availability?
26. How would you approach disaster recovery?
27. How would you balance performance, reliability, and cost?

---

## 26. What to learn next

The natural learning path from this topic is:

```text
System Design Approach
        ↓
Capacity Estimation
        ↓
Scalability
        ↓
Load Balancing
        ↓
Caching
        ↓
Databases
        ↓
Replication
        ↓
Partitioning / Sharding
        ↓
Messaging
        ↓
Consistency
        ↓
Fault Tolerance
        ↓
Distributed Systems
        ↓
Real-World System Designs
```

Do not try to learn every technology at once.

First understand why a component is needed.

Then learn the technology that implements that concept.

---

## Key takeaways

1. Understand the problem before designing the solution.
2. Separate functional requirements from non-functional requirements.
3. Clarify scale before choosing architecture.
4. Every architecture component should have a reason.
5. Choose databases based on data model, access patterns, consistency, and scale.
6. Caching improves performance but introduces consistency challenges.
7. Messaging provides decoupling but adds distributed-system complexity.
8. Always ask what happens when a component fails.
9. Always ask what happens when traffic becomes 10×.
10. System design is largely about understanding and communicating trade-offs.

---

## My system design rule

> Do not memorize architectures.
>
> Understand the problem → identify constraints → choose components → explain why → understand the trade-offs.

This mindset is useful whether you are designing a small application, a cloud platform, a site reliability system, or a large distributed architecture.
