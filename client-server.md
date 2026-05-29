# Chapter 2: The Life of a Web Request

---

# Why This Chapter Matters

Before designing distributed systems, microservices, databases, caches, or CDNs, we must understand how data travels across the internet.

Every system we build ultimately serves requests coming from users or other systems.

Whether it is:

* YouTube playing a video
* WhatsApp sending a message
* Uber booking a ride
* Amazon placing an order
* Netflix streaming a movie

Every interaction follows the same fundamental pattern:

```text
Client → Request → Server → Response
```

Understanding this journey is the foundation of system design.

---

# What We Will Cover

This chapter introduces the key building blocks involved in every web request:

1. Client-Server Model
2. DNS (Domain Name System)
3. IP Addresses and Ports
4. TCP and UDP
5. HTTP and HTTPS
6. Complete Request-Response Lifecycle
7. Latency and Round Trip Time (RTT)

---

# Why This Matters For System Design

| Concept           | Where It Appears In Real Systems                |
| ----------------- | ----------------------------------------------- |
| DNS               | Global traffic routing, failover, CDN routing   |
| TCP vs UDP        | Chat systems, gaming platforms, video streaming |
| HTTP/HTTPS        | REST APIs, web applications, microservices      |
| Latency           | Performance optimization, caching strategies    |
| Request Lifecycle | Understanding bottlenecks and scalability       |

---

# The Key Question

Consider a simple action:

> You open YouTube on your phone and press Play.

Within a few seconds a video starts playing.

What happened?

The request likely traveled through:

```text
Mobile App
    ↓
DNS Resolution
    ↓
Internet
    ↓
Load Balancer
    ↓
Application Servers
    ↓
Metadata Services
    ↓
Databases
    ↓
CDN
    ↓
Video Playback
```

Every major system design topic eventually becomes a part of this request journey.

This chapter introduces the first building block:

**The Client-Server Model**

---

# Client-Server Model

The client-server model is the foundation of almost every distributed system.

A client initiates communication.

A server receives requests, processes them, and sends back responses.

```text
Client
   |
Request
   |
   V
Server
   |
Response
   |
   V
Client
```

---

# What Is A Client?

A client is any device, application, or service that initiates communication.

Examples:

* Chrome Browser
* Safari
* Mobile Applications
* Desktop Applications
* Postman
* Another Backend Service

Examples from real systems:

| System   | Client               |
| -------- | -------------------- |
| YouTube  | Mobile App / Browser |
| WhatsApp | Mobile App           |
| Netflix  | Smart TV App         |
| Uber     | Rider App            |
| Amazon   | Web Browser          |

The defining characteristic:

> A client starts the conversation.

---

# What Is A Server?

A server waits for incoming requests.

When a request arrives:

1. Accept the request
2. Process the request
3. Return a response

Example:

```text
Client
   |
GET /video/123
   |
   V
Server
   |
Returns Video Metadata
   |
   V
Client
```

The server owns the data and business logic.

---

# Types Of Servers

In real systems, multiple server types exist.

## Web Server

Responsible for serving static content.

Examples:

* Nginx
* Apache

Typical responsibilities:

* Images
* CSS files
* JavaScript files
* Static HTML

---

## Application Server

Contains business logic.

Examples:

* Spring Boot
* Node.js
* Django
* Express.js

Typical responsibilities:

* User Authentication
* Order Processing
* Video Recommendations
* Payment Validation

---

## Database Server

Stores and retrieves data.

Examples:

* PostgreSQL
* MySQL
* MongoDB
* Cassandra

Typical responsibilities:

* User Profiles
* Orders
* Messages
* Video Metadata

---

# Important Interview Concept

## A Server Can Also Be A Client

This concept becomes extremely important in microservices.

Example:

```text
Browser
   |
   V
API Gateway
   |
   V
User Service
   |
   V
Database
```

At different points:

| Component                        | Acting As |
| -------------------------------- | --------- |
| Browser                          | Client    |
| API Gateway                      | Server    |
| API Gateway calling User Service | Client    |
| User Service                     | Server    |
| User Service calling Database    | Client    |
| Database                         | Server    |

Therefore:

> A component can be both a client and a server depending on the direction of communication.

This concept appears frequently in system design interviews.

---

# One Server, Many Clients

The simplest architecture looks like:

```text
Many Clients
      |
      V
   Server
      |
      V
  Database
```

Example:

```text
1000 Users
      |
      V
Application Server
      |
      V
Database
```

For small traffic volumes, this architecture works perfectly.

---

# Why Single Server Architecture Breaks

Suppose traffic grows dramatically.

Instead of:

```text
1,000 Users
```

Now we have:

```text
1,000,000 Users
```

arriving simultaneously.

Questions:

* Can one CPU handle all requests?
* Can one machine store all data?
* Can one network connection support all traffic?
* What happens if the server crashes?

Eventually the answer becomes:

**No.**

---

# Capacity Limits Of A Single Server

A typical server may support:

```text
1,000 - 10,000 Concurrent Connections

500 - 5,000 Requests Per Second

8 - 64 GB RAM

1 - 10 TB Storage
```

Actual capacity depends on workload complexity.

---

# Real World Example: YouTube

YouTube serves billions of users globally.

Approximate platform characteristics:

```text
Hundreds of thousands of requests per second

Massive video upload volume

Global traffic across multiple continents
```

No single machine can handle this workload.

A single server would quickly fail because of:

* CPU exhaustion
* Memory exhaustion
* Disk limitations
* Network bandwidth limitations
* Single point of failure

---

# Real Production Problem

Imagine a single-server YouTube architecture.

```text
Users
   |
   V
YouTube Server
   |
   V
Database
```

Now suppose the server crashes.

Result:

```text
No Uploads

No Video Playback

No Search

No Recommendations

No Login
```

The entire business becomes unavailable.

This is called a:

> Single Point Of Failure (SPOF)

Eliminating SPOFs is one of the primary goals of system design.

---

# The Fundamental Problem Of System Design

At small scale:

```text
Clients
   |
Server
   |
Database
```

works well.

At internet scale:

```text
Millions Of Clients
```

require:

* Multiple Servers
* Multiple Databases
* Load Balancers
* Caches
* CDNs
* Distributed Storage

The core question becomes:

> How do we serve millions of users reliably, quickly, and cost-effectively?

This question drives the entire field of system design.

---

# How Architectures Evolve

Stage 1

```text
Clients
   |
Server
   |
Database
```

Stage 2

```text
Clients
   |
Load Balancer
   |
   +---- Server 1
   |
   +---- Server 2
   |
   +---- Server 3
```

Stage 3

```text
Clients
   |
CDN
   |
Load Balancer
   |
Application Tier
   |
Cache
   |
Database
```

Every modern architecture is an evolution of the same client-server foundation.

---

# Architect's Perspective

Junior engineers often think:

```text
Frontend → Backend
```

Architects think:

```text
Requester → Responder
```

Everything in distributed systems becomes easier when viewed as request-response communication.

Whether it is:

* Browser to Backend
* Service to Service
* Backend to Database
* Backend to Cache

The same pattern repeats.

---

# Common Interview Questions

## Why Can't YouTube Run On A Single Powerful Server?

Expected discussion:

* Hardware limits
* Geographic latency
* Network bandwidth limits
* Storage limits
* Availability requirements
* Single point of failure risks

---

## Can A Server Also Be A Client?

Yes.

A microservice receiving requests from another service acts as a server.

When it calls a downstream service, it becomes a client.

---

## What Is The Main Goal Of System Design?

To serve growing numbers of users while maintaining:

* Scalability
* Reliability
* Availability
* Performance
* Cost Efficiency

---

# Key Takeaways

1. Every distributed system is built on the client-server model.
2. Clients initiate communication.
3. Servers process requests and return responses.
4. A server can also act as a client.
5. Single-server architectures eventually hit scaling limits.
6. Single-server systems introduce single points of failure.
7. Modern architectures evolve by distributing load across multiple components.
8. Understanding request flow is the foundation for every future system design topic.

---

# Revision Notes

Remember these three statements:

```text
Client initiates communication.

Server processes requests.

System Design begins when one server is no longer enough.
```

Everything else in this course will be a solution to the problems created by that last statement.
