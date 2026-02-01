
- [Load Balancing](#load-balancing)
  - [Vertical Scaling (Scale Up)](#vertical-scaling-scale-up)
  - [Horizontal Scaling (Scale Out)](#horizontal-scaling-scale-out)
  - [Client-Side Load Balancing](#client-side-load-balancing)
  - [Layer 4 Load Balancer](#layer-4-load-balancer)
  - [Layer 7 Load Balancer](#layer-7-load-balancer)
  - [Health Checks and Fault Tolerance](#health-checks-and-fault-tolerance)
  - [Load Balancing Algorithms](#load-balancing-algorithms)

# Load Balancing

- Load balancing is the practice of distributing incoming network traffic across multiple servers to ensure no single server becomes a bottleneck, improving performance, availability, and reliability of applications.
- A load balancer acts as a traffic manager, deciding which server should handle each request based on factors like current load, response times, or health checks.
- This is especially important in high-traffic systems to prevent downtime and ensure consistent response times for users.

## Vertical Scaling (Scale Up)
- Increase resources (CPU, RAM, storage) on a single server.
- Simple to implement but limited by hardware and can be costly.

## Horizontal Scaling (Scale Out)
- Add more servers and distribute traffic among them.
- More flexible, improves fault tolerance, and commonly used in cloud-based systems.

## Client-Side Load Balancing
- Client-side load balancing means the client picks which server to talk to using a list of available servers.
- This removes the need for a central load balancer and can reduce latency.
- Common examples: Redis Cluster (key hashing) and DNS-based load distribution.
- Works best for internal or controlled clients where server lists can be kept reasonably up to date.
- Less suitable for public or highly dynamic systems, where server-side load balancers are usually better.

## Layer 4 Load Balancer
- Layer 4 load balancers work at the TCP/UDP level using IPs and ports.
- They’re very fast and efficient, with persistent connections per session.
- No visibility into HTTP or request content → no content-based routing.
- Best suited for high-performance traffic and long-lived connections (e.g. WebSockets).

## Layer 7 Load Balancer
- Layer 7 load balancers operate at the application layer and understand HTTP.
- They route traffic based on URLs, headers, cookies, etc.
- Enable features like path-based routing, session affinity, and API traffic splitting.
- Higher CPU overhead due to request inspection.
- Best for HTTP traffic; long-lived connections (e.g. WebSockets) usually suit Layer 4 better.

## Health Checks and Fault Tolerance

- Health checks let load balancers detect unhealthy servers and maintain high availability.
- Can be simple TCP checks (Layer 4) or full HTTP checks (Layer 7).\Unhealthy servers are removed from rotation; traffic is routed around failures.
- Traffic resumes once servers recover, ensuring fault tolerance without affecting users.

## Load Balancing Algorithms
- Load balancers use algorithms to distribute traffic.
- Round robin or random → simple, good for stateless services.
- Least connections → better for stateful or long-lived connections (WebSockets, SSE) to prevent overload and improve fairness.
