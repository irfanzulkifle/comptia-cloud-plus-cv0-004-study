## SECTION 1 — Exam Objectives

- **Objective 5.3 — Explain concepts related to integration of systems.** Modern cloud applications are composed of many services that must talk to each other. This objective covers the *patterns and protocols* used to connect systems: reacting to events, calling web services, maintaining live connections, and querying data via a graph API. Expect "which integration pattern fits scenario X" questions.
- **5.3.1 Event-driven architectures** — Systems react to events/messages asynchronously
- **5.3.2 Web services (REST/SOAP/RPC)** — Request/response APIs over HTTP
- **5.3.3 Web sockets** — Persistent, full-duplex real-time connections
- **5.3.4 GraphQL** — A query language/API for flexible data fetching

---

## SECTION 2 — EXAM KNOWLEDGE

### 5.3.1 Event-Driven Architectures
- **Core idea:** Components communicate via events—discrete facts emitted by a producer and consumed by one or more subscribers, usually through a broker/event bus, decoupling producers from consumers.
- **Key components:** event, producer/publisher, consumer/subscriber, broker (Kafka, RabbitMQ, AWS SNS/SQS/EventBridge), topic (pub/sub), and queue (point-to-point).
- **Patterns & benefits:** pub/sub (broadcast) and event streaming (ordered, replayable logs like Kafka); benefits include loose coupling, elasticity, fault isolation, and easy scaling of consumers.
- **Trade-offs & exam note:** eventual consistency, harder debugging (no direct call chain), and requires idempotent consumers; AWS EventBridge is the serverless event bus routing events by rules.

### 5.3.2 Web Services (REST / SOAP / RPC)
- **REST:** Uses standard HTTP verbs (GET/POST/PUT/DELETE), resource-oriented URLs, stateless requests, and JSON/XML; the default for modern cloud APIs.
- **SOAP:** Older, stricter XML-envelope protocol with a WSDL contract and built-in WS-Security/reliability; favored in legacy enterprise/finance.
- **RPC:** Invokes a remote function as if local (XML-RPC, JSON-RPC, gRPC over HTTP/2 binary Protobuf); high-performance internal calls.
- **Exam contrast:** All three are request/response and synchronous (caller waits), unlike EDA; greenfield cloud → REST/gRPC, legacy banking → SOAP; AWS API Gateway exposes/secures these.

### 5.3.3 Web Sockets
- **What it is:** A single long-lived, full-duplex TCP connection after an initial HTTP Upgrade handshake (ws:// or wss://), enabling server push without repeated requests.
- **vs HTTP/REST:** HTTP is half-duplex, request-driven, connection-per-call; web sockets are full-duplex, persistent, server-push-capable, reducing handshake overhead.
- **Use cases:** chat, live dashboards, multiplayer games, streaming prices—real-time bidirectional updates.
- **Considerations:** needs connection-state management (sticky/WS-aware load balancers), harder to scale (stateful), must use wss:// (TLS); complements—not replaces—REST; SSE is a one-way alternative.

### 5.3.4 GraphQL
- **What it is:** A query language/API where the client specifies exactly which fields it wants; one flexible endpoint returns only those fields (vs REST's fixed endpoint shapes).
- **Schema & operations:** Strong type-system schema as a contract; supports queries (read), mutations (write), and subscriptions (real-time, often over web sockets).
- **Benefits:** front-end flexibility, fewer round-trips, self-documenting schema (introspection); ideal for mobile apps minimizing payloads.
- **Trade-offs & exam contrast:** harder caching (no URL per resource), costly queries need rate/depth limits, complex resolver logic; vs REST on fetch efficiency and endpoint count; query-oriented, not event-oriented.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **S3 → Lambda → DynamoDB (EDA):** A user uploads a profile picture to S3; an S3 event notifies EventBridge, which triggers a Lambda that resizes the image and writes metadata to DynamoDB—no polling, fully decoupled.
- **REST microservices behind API Gateway:** A mobile app calls `GET /orders/{id}` through AWS API Gateway, which authenticates the request and routes it to a containerized order service returning JSON—classic stateless REST.
- **Live trading dashboard (Web Sockets):** A financial dashboard keeps a `wss://` socket open to a price service so tick updates stream to the browser instantly without repeated polling.
- **GraphQL BFF for mobile:** A retail app uses a GraphQL backend-for-frontend so the client fetches a product's price, reviews, and stock in one request, trimming mobile payloads.
- **Legacy SOAP integration:** A cloud app bridges to an on-premises bank core via SOAP/WSDL for a payment transaction that requires WS-Security guarantees REST alone doesn't provide.

---

## SECTION 4 — COMPARISON TABLES

**Table 4.1 — Integration Patterns: Sync vs. Async**

| Pattern | Direction | Coupling | Example |
|---------|-----------|----------|---------|
| Web service (REST/RPC) | Request/response | Tight (caller waits) | API call |
| Event-driven | Publish/subscribe | Loose (async) | SNS/SQS |
| Web socket | Full-duplex persistent | Stateful | Live chat |

**Table 4.2 — REST vs. SOAP vs. RPC**

| Trait | REST | SOAP | RPC (gRPC) |
|-------|------|------|------------|
| Format | JSON/XML | XML only | Protobuf |
| Contract | Loose | WSDL strict | Proto strict |
| Best for | Cloud APIs | Legacy enterprise | High-perf internal |

**Table 4.3 — REST vs. GraphQL**

| Aspect | REST | GraphQL |
|--------|------|---------|
| Endpoints | Many, fixed | One, flexible |
| Over/under-fetch | Common | Avoided |
| Caching | Easy (URL) | Harder |

**Table 4.4 — HTTP vs. Web Sockets**

| Aspect | HTTP/REST | Web Sockets |
|--------|-----------|-------------|
| Connection | Per-request | Persistent |
| Duplex | Half | Full |
| Server push | No | Yes |

---

## SECTION 5 — AWS PROVIDER MAPPING

AWS-native services that map to Objective 5.3 integration concepts:

- **Amazon EventBridge** — A serverless **event bus** that is the AWS embodiment of event-driven architectures (5.3.1). Producers emit events; EventBridge routes them to targets (Lambda, SQS, Step Functions) by rules. It also delivers AWS service events and SaaS partner events, making it the central EDA hub on AWS.
- **Amazon API Gateway** — The managed service for exposing and securing **web services** (5.3.2). It fronts REST (and HTTP/WebSocket) APIs, handling auth, throttling, and routing to backends like Lambda or ECS. API Gateway also supports **WebSocket APIs** (5.3.3), so a single AWS service covers both request/response web services and persistent real-time connections.

> Note: GraphQL (5.3.4) is typically implemented on AWS via a Lambda or AppSync (GraphQL service) behind API Gateway; EventBridge covers EDA and API Gateway covers web services + web sockets. These two providers satisfy the full 5.3 mapping.

---

## SECTION 6 — PRACTICE QUESTIONS

1. Event-driven architectures are best characterized by:
   A. Synchronous request/response
   B. Loose coupling via events/async messaging
   C. Single monolith
   D. Direct database sharing

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** EDA decouples producers/consumers through async events via a broker.
> **Why A is wrong:** Synchronous request/response is the web-service model, not EDA.
> **Why C is wrong:** EDA favors distributed services, not a single monolith.
> **Why D is wrong:** Direct DB sharing couples systems; EDA avoids that via events.

2. In pub/sub, a message is delivered to:
   A. Exactly one consumer
   B. All subscribers of a topic
   C. The database only
   D. The producer

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Pub/sub broadcasts each message to every subscriber of the topic.
> **Why A is wrong:** Exactly-one delivery is a queue (point-to-point), not pub/sub.
> **Why C is wrong:** A message is not delivered to the database by the pattern itself.
> **Why D is wrong:** The producer emits; it does not receive its own message.

3. Which AWS service is the serverless event bus?
   A. API Gateway
   B. EventBridge
   C. DynamoDB
   D. CloudFront

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** EventBridge is the serverless event bus routing events to targets by rules.
> **Why A is wrong:** API Gateway fronts web services/WebSockets, not an event bus.
> **Why C is wrong:** DynamoDB is a database, not an event bus.
> **Why D is wrong:** CloudFront is a CDN, not an event router.

4. REST uses which HTTP verbs?
   A. Only GET
   B. GET/POST/PUT/DELETE
   C. Only POST
   D. PING

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** REST is resource-oriented using standard HTTP verbs including GET/POST/PUT/DELETE.
> **Why A is wrong:** REST uses multiple verbs, not GET only.
> **Why C is wrong:** POST alone is not sufficient; REST uses several verbs.
> **Why D is wrong:** PING is a network diagnostic, not an HTTP verb.

5. SOAP primarily uses which data format?
   A. JSON
   B. XML
   C. CSV
   D. Protobuf

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** SOAP is an XML-envelope protocol with a WSDL contract.
> **Why A is wrong:** JSON is typical of REST, not SOAP.
> **Why C is wrong:** CSV is not a SOAP format.
> **Why D is wrong:** Protobuf is used by gRPC/RPC, not SOAP.

6. gRPC is a variant of which integration type?
   A. SOAP
   B. REST
   C. RPC
   D. GraphQL

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** gRPC is a high-performance RPC using HTTP/2 and Protobuf.
> **Why A is wrong:** gRPC is not SOAP (which is XML/WSDL).
> **Why B is wrong:** gRPC is RPC, not REST.
> **Why D is wrong:** It is not GraphQL; GraphQL is a query language.

7. Which protocol provides a persistent full-duplex connection?
   A. REST
   B. SOAP
   C. Web sockets
   D. FTP

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Web sockets keep one long-lived full-duplex TCP connection after an HTTP Upgrade.
> **Why A is wrong:** REST is half-duplex, request-per-connection.
> **Why B is wrong:** SOAP is request/response over HTTP, not persistent duplex.
> **Why D is wrong:** FTP is a file-transfer protocol, not a real-time duplex channel.

8. Web sockets begin with which handshake?
   A. TCP only
   B. An HTTP Upgrade request
   C. SMTP
   D. DNS

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** A web socket opens via an HTTP Upgrade handshake before becoming ws://.
> **Why A is wrong:** It starts as HTTP (Upgrade), not raw TCP only.
> **Why C is wrong:** SMTP is email; unrelated to web sockets.
> **Why D is wrong:** DNS resolves names; it is not the socket handshake.

9. The secure variant of web sockets is:
   A. http://
   B. wss://
   C. ftp://
   D. tcp://

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** `wss://` is the TLS-secured web socket scheme (analogous to https).
> **Why A is wrong:** `http://` is plaintext web, not a socket scheme.
> **Why C is wrong:** `ftp://` is file transfer, not web sockets.
> **Why D is wrong:** `tcp://` is not a web socket scheme.

10. GraphQL lets the client:
    A. Only GET data
    B. Specify exactly which fields to return
    C. Use SOAP
    D. Avoid a schema

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** GraphQL clients write a query requesting only the fields they need.
> **Why A is wrong:** GraphQL supports queries, mutations, and subscriptions—not read-only.
> **Why C is wrong:** It is not SOAP; it has its own query language.
> **Why D is wrong:** GraphQL is schema-first; it requires a schema/type system.

11. Compared to REST, GraphQL reduces:
    A. Security
    B. Over/under-fetching
    C. Schema use
    D. Endpoint count increase

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Clients fetch precisely the fields they need, avoiding extra or missing data.
> **Why A is wrong:** GraphQL introduces its own security considerations (query depth limits).
> **Why C is wrong:** GraphQL relies on a strong schema, more than REST.
> **Why D is wrong:** GraphQL reduces endpoint count (one endpoint), not increases it.

12. A key downside of GraphQL is:
    A. Too many endpoints
    B. Harder caching and costly queries
    C. No schema
    D. No mutations

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** One flexible endpoint complicates URL-based caching; bad queries can be expensive.
> **Why A is wrong:** GraphQL uses few/one endpoint, not too many.
> **Why C is wrong:** GraphQL has a strict schema, not "no schema."
> **Why D is wrong:** GraphQL supports mutations for writes.

13. Idempotent consumers matter in EDA because:
    A. Events may be delivered more than once
    B. REST requires it
    C. SOAP needs it
    D. Web sockets need it

> [!note]- Reveal Answer
> **Correct: A**
> **Why correct:** Brokers can redeliver events, so consumers must handle duplicates safely.
> **Why B is wrong:** REST does not inherently drive the EDA idempotency requirement.
> **Why C is wrong:** SOAP does not create the need for EDA idempotency.
> **Why D is wrong:** Web sockets are not the reason; at-least-once delivery is.

14. Which is an example of eventual consistency?
    A. Synchronous DB write
    B. Event processed later by a queue
    C. REST GET
    D. SOAP call

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** An async queue processes events after emission, so state converges later.
> **Why A is wrong:** A synchronous DB write is immediately consistent.
> **Why C is wrong:** A REST GET reads current state, not eventual consistency.
> **Why D is wrong:** A SOAP call is synchronous request/response.

15. API Gateway on AWS primarily exposes:
    A. Virtual machines
    B. REST/WebSocket web services
    C. Disk volumes
    D. IAM users

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** API Gateway fronts REST and WebSocket APIs, handling auth/routing.
> **Why A is wrong:** It exposes APIs, not virtual machines.
> **Why C is wrong:** It does not manage disk volumes.
> **Why D is wrong:** IAM users are managed by IAM, not API Gateway.

16. A queue (vs. topic) delivers a message to:
    A. Many subscribers
    B. Exactly one consumer
    C. The producer
    D. Everyone

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Point-to-point queues deliver each message to a single consumer.
> **Why A is wrong:** Many subscribers is pub/sub topics, not queues.
> **Why C is wrong:** The message goes to a consumer, not back to the producer.
> **Why D is wrong:** "Everyone" describes broadcast, not queue semantics.

17. Web sockets are best for:
    A. One-time data fetch
    B. Real-time bidirectional updates
    C. Batch jobs
    D. File storage

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Persistent full-duplex sockets suit live chat/dashboards/streaming.
> **Why A is wrong:** One-time fetches are better via REST, not a persistent socket.
> **Why C is wrong:** Batch jobs do not need real-time bidirectional channels.
> **Why D is wrong:** File storage is unrelated to web sockets.

18. Event streaming (e.g., Kafka) provides:
    A. Point-to-point only
    B. Ordered, replayable logs
    C. SOAP envelopes
    D. GraphQL schema

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Streaming platforms keep an ordered, replayable commit log of events.
> **Why A is wrong:** Kafka is pub/sub/streaming, not point-to-point only.
> **Why C is wrong:** SOAP envelopes are unrelated to event streaming.
> **Why D is wrong:** A GraphQL schema is not a streaming-log concept.

19. The contract in GraphQL is called a:
    A. WSDL
    B. Schema/type system
    C. Topic
    D. Queue

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** GraphQL's contract is its schema/type system defining queries and types.
> **Why A is wrong:** WSDL is the SOAP contract, not GraphQL's.
> **Why C is wrong:** A topic is an EDA/pub-sub concept, not GraphQL.
> **Why D is wrong:** A queue is a messaging concept, not a GraphQL contract.

20. Server-sent events (SSE) differ from web sockets because SSE is:
    A. Full-duplex
    B. One-way (server to client)
    C. SOAP-based
    D. Always REST

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** SSE is a unidirectional server→client stream; web sockets are full-duplex.
> **Why A is wrong:** SSE is one-way, not full-duplex.
> **Why C is wrong:** SSE is not SOAP-based; it is a simple HTTP stream.
> **Why D is wrong:** SSE is its own mechanism, not "always REST."
