# Connections

When designing systems there are different ways of communication between services via APIs.

## REST

- REST (Representational State Transfer) is a simple and flexible way to design APIs around resources rather than operations.
- Clients interact with resources using standard HTTP methods—GET to read, POST to create, PUT/PATCH to update, and DELETE to remove—often exchanging data as JSON.
- Resources can be nested to represent relationships, and RESTful APIs map naturally to core entities in a system.
- While not the most performant for extremely high-throughput services, REST is widely used, easy to understand, and a solid default choice for API design in system design interviews.

## GraphQL

- GraphQL is an API paradigm that allows clients to request exactly the data they need, solving problems of under-fetching (requiring multiple requests) and over-fetching (receiving unnecessary data) common in REST APIs.
- Clients specify the fields and nested objects they want, and the backend returns data in that shape, reducing latency and payload size.
- GraphQL is especially useful for complex frontends or mobile apps where requirements change frequently, but it can add backend complexity and isn’t always necessary for fixed, well-defined system designs.

## gRPC

- gRPC is a high-performance RPC (Remote Procedure Call) framework from Google (the "g") that uses HTTP/2 and Protocol Buffers.
- gRPC is a high-performance, binary RPC framework that uses HTTP/2 and Protocol Buffers for efficient, strongly-typed communication between services.
- It’s faster and more compact than JSON over HTTP, supports streaming, deadlines, and client-side load balancing, and is ideal for internal service-to-service communication in microservice architectures where performance is critical.
- While gRPC is great for internal APIs, REST is usually preferred for public-facing APIs due to its simplicity and wide client support.

## Server-Sent Events (SSE)

- Server-Sent Events (SSE) allow a server to push multiple messages to a client over a single HTTP connection, enabling real-time streaming updates.
- Unlike standard HTTP responses that deliver a single payload, SSE sends data in chunks, letting clients process each event as it arrives.
- SSE is useful for scenarios like live notifications or auction updates, though connections can be closed by servers or proxies, and clients need to handle reconnections.
- It’s simpler than gRPC streaming for browser-based or external clients, but comes with some practical limitations in unreliable networks.

## WebSockets: Real-Time Bidirectional Communication

- WebSockets provide a persistent, bidirectional connection between client and server.
- Enabling real-time communication in both directions over a single TCP connection.
- Unlike SSE, which is server-to-client only, WebSockets let clients and servers push messages anytime without repeated HTTP requests.

How it works:
- They start with an HTTP handshake
- Upgrade the connection to the WebSocket protocol
- Then stay open until closed.

Use cases:
- Real-time apps (chat, games, live updates)

Trade-offs:
- Stateful connections increase infrastructure complexity and cost
- Overkill if simple request-response or SSE is sufficient

## WebRTC: Peer-to-Peer Communication

WebRTC enables direct peer-to-peer communication between browsers, allowing real-time audio, video, or data sharing without relying on an intermediary server for the actual data. Because most clients are behind NATs, WebRTC uses STUN (to discover public addresses) and TURN (to relay data if direct connection fails) to establish connectivity. Connections start via a signaling server to exchange peer information, then data flows directly over UDP. Ideal use-cases are video/audio calls, conferencing, and real-time collaboration, but for most collaborative apps, a server-based approach like WebSockets is simpler and more scalable.

