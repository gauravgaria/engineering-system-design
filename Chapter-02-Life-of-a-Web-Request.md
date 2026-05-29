# Chapter 2: The Life of a Web Request

## Why This Chapter Matters

Before designing distributed systems, microservices, databases, caches, or CDNs, we must understand how data travels across the internet.

Every system we build ultimately serves requests coming from users or other systems.

Whether it is:

- YouTube playing a video
- WhatsApp sending a message
- Uber booking a ride
- Amazon placing an order
- Netflix streaming a movie

Every interaction follows the same fundamental pattern:

```text
Client → Request → Server → Response
```

## What We Will Cover

1. Client-Server Model
2. DNS (Domain Name System)
3. IP Addresses and Ports
4. TCP and UDP
5. HTTP and HTTPS
6. Complete Request-Response Lifecycle
7. Latency and Round Trip Time (RTT)

## Client-Server Model

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

## What Is A Client?

A client is any device, application, or service that initiates communication.

Examples:

- Chrome Browser
- Safari
- Mobile Applications
- Desktop Applications
- Postman
- Another Backend Service

## What Is A Server?

A server waits for incoming requests.

When a request arrives:

1. Accept the request
2. Process the request
3. Return a response

## Important Interview Concept

### A Server Can Also Be A Client

A component can be both a client and a server depending on the direction of communication.

## Why Single Server Architecture Breaks

A typical server may support:

- 1,000 - 10,000 concurrent connections
- 500 - 5,000 requests/sec
- 8 - 64 GB RAM

At internet scale, a single server becomes a bottleneck and a single point of failure.

## Key Takeaways

1. Every distributed system is built on the client-server model.
2. Clients initiate communication.
3. Servers process requests and return responses.
4. A server can also act as a client.
5. Single-server architectures eventually hit scaling limits.
6. System design begins when one server is no longer enough.
