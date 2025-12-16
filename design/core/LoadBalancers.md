# Load Balancing

- [Load Balancing](#load-balancing)
  - [Client-Side Load Balancing](#client-side-load-balancing)
  - [Layer 4 Load Balancer](#layer-4-load-balancer)
  - [Layer 7 Load Balancer](#layer-7-load-balancer)
  - [Health Checks and Fault Tolerance](#health-checks-and-fault-tolerance)
  - [Load Balancing Algorithms](#load-balancing-algorithms)


Load balancing is the practice of distributing incoming network traffic across multiple servers to ensure no single server becomes a bottleneck, improving performance, availability, and reliability of applications. A load balancer acts as a traffic manager, deciding which server should handle each request based on factors like current load, response times, or health checks. This is especially important in high-traffic systems to prevent downtime and ensure consistent response times for users.

There are two main approaches to scaling for load balancing: vertical scaling and horizontal scaling.

Vertical scaling (scaling up) means adding more resources—CPU, RAM, or storage—to a single server to handle increased load. It's simple but limited by hardware constraints and can become expensive.

Horizontal scaling (scaling out) involves adding more servers to the system and distributing traffic among them. This approach is more flexible, allows for better fault tolerance, and is often used in cloud-native architectures with load balancers automatically routing requests to healthy servers.

## Client-Side Load Balancing

Client-side load balancing is a strategy where the client decides which server to connect to by consulting a service registry or directory that lists available servers. This allows the client to choose the fastest or most appropriate server directly, reducing the overhead of routing through a centralized load balancer. Examples include Redis Cluster, where clients hash keys to determine the target node, and DNS, which rotates IP addresses for a domain to distribute traffic among servers.

This approach works well for controlled clients or scenarios where updates can tolerate some delay, as clients need to stay informed about server availability. It is especially useful for internal microservices (e.g., gRPC) and can reduce latency, but for large-scale public clients or dynamic environments, a dedicated server-side load balancer is usually preferred.

## Layer 4 Load Balancer

Layer 4 load balancers operate at the transport layer (TCP/UDP) and route traffic based only on IP addresses and ports, without inspecting application data. They are extremely fast and efficient, maintaining persistent TCP connections so that once a client is connected, all traffic in that session goes to the same backend server. Because they don’t understand HTTP or request content, they can’t do content-based routing, but they’re ideal for high-performance workloads and persistent connections like WebSockets.

## Layer 7 Load Balancer

Layer 7 load balancers work at the application layer, understanding HTTP and routing requests based on content like URLs, headers, and cookies rather than just IPs and ports. They terminate client connections and create new ones to backend servers, enabling powerful features such as path-based routing, session affinity, and API-style traffic splitting. This flexibility comes at the cost of higher CPU overhead due to deep packet inspection. L7 load balancers are ideal for HTTP-based traffic, while persistent, connection-oriented protocols like WebSockets typically fit better with Layer 4 load balancing.

## Health Checks and Fault Tolerance

Health checks are how load balancers enable fault tolerance and high availability by continuously monitoring backend servers and automatically removing unhealthy ones from traffic rotation. They can be simple Layer 4 checks (e.g., “can I open a TCP connection?”) or deeper Layer 7 checks (e.g., “does an HTTP endpoint return 200 OK?”). When a server crashes or misbehaves, the load balancer detects it and routes traffic around the failure without user impact, then resumes sending traffic once the server recovers.

## Load Balancing Algorithms

Dedicated load balancers give you control over how traffic is distributed. Simple algorithms like round robin or random work best for stateless services and scale naturally when new servers are added. For stateful or long-lived connections (like SSE or WebSockets), smarter algorithms such as least connections help prevent one server from becoming overloaded with persistent clients, improving stability and fairness.
