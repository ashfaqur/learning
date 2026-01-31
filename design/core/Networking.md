# Networking Essentials

- [Networking Essentials](#networking-essentials)
- [Networking Layers OSI Model](#networking-layers-osi-model)
- [DNS (Domain Name System)](#dns-domain-name-system)
  - [DNS lookup flow (simplified)](#dns-lookup-flow-simplified)
- [TCP Handshake](#tcp-handshake)
  - [TCP Four-Way Teardown (Connection Close)](#tcp-four-way-teardown-connection-close)
- [Transport Layer Security (TLS) HTTPS Upgrade](#transport-layer-security-tls-https-upgrade)
  - [One-sentence HTTPS summary (interview gold)](#one-sentence-https-summary-interview-gold)
- [HTTP Request and Response](#http-request-and-response)
- [Network Layer Protocols](#network-layer-protocols)
- [User Datagram Protocol (UDP)](#user-datagram-protocol-udp)
- [HTTP](#http)
  - [Common Headers](#common-headers)
  - [Common Request Methods](#common-request-methods)
  - [Common Status Codes](#common-status-codes)
  - [Authentication](#authentication)
  - [Regionalization and Latency](#regionalization-and-latency)
    - [Content Delivery Networks (CDNs)](#content-delivery-networks-cdns)
    - [Regional Partitioning](#regional-partitioning)
  - [Handling Failures and Fault Modes](#handling-failures-and-fault-modes)
    - [Timeouts and Retries](#timeouts-and-retries)
    - [Idempotency](#idempotency)
    - [Circuit Breakers](#circuit-breakers)


# Networking Layers OSI Model

1. Physical
   - Transmits raw bits over a physical medium
   - Cables, voltages, radio signals, connectors (Ethernet cable, Wi-Fi radio).
2. Data Link
   - Moves frames between nodes on the same network
   - MAC addresses, switches, ARP, Ethernet framing, error detection.
3. Network
   - Routes packets between different networks
   - IP addressing, routing decisions, routers (IP, ICMP).
4. Transport
   - Provides end-to-end communication and reliability
   - Ports, flow control, retransmission (TCP) or fast best-effort delivery (UDP).
5. Session
   - Manages sessions between applications
   - Session setup, maintenance, teardown (rarely explicit today).
6. Presentation
   - Formats, encrypts, and compresses data
   - Encoding, TLS/SSL, serialization (JSON, UTF-8).
7. Application
   - User-facing network services
   - HTTP, HTTPS, FTP, SMTP, DNS — what apps actually use.

# DNS (Domain Name System)
- DNS translates human-readable domain names into IP addresses.
Example: www.example.com → 93.184.216.34
- Without DNS, you’d have to remember IP addresses for every site.
- DNS is the phonebook of the internet
- Distributed, hierarchical, and highly cached

## DNS lookup flow (simplified)
- Browser checks cache
- OS checks local DNS cache
- Query sent to recursive resolver
- Resolver asks: Root server → who handles .com?
- Top-Level Domain (TLD) server → who handles example.com?
- Authoritative server → here’s the IP
- IP returned + cached (based on TTL)

# TCP Handshake

Transmission Control Protocol (TCP) establishes a reliable, ordered, bidirectional connection using three messages.

The TCP three-way handshake establishes a reliable connection in three steps: the client sends a SYN with its initial sequence number to request a connection, the server replies with SYN-ACK acknowledging the client and providing its own sequence number, and the client responds with an ACK to confirm. After this exchange, both sides know each other’s sequence numbers, can send and receive data, and the connection is in the ESTABLISHED state. The three-step process ensures both endpoints are reachable, can communicate bidirectionally, and prevents half-open connections.

    Client                    Server
    | ------ SYN (x) ------> |
    | <--- SYN-ACK (y,x+1) - |
    | ------ ACK (y+1) ----> |
     Connection established

## TCP Four-Way Teardown (Connection Close)

CP uses a four-way handshake to close a connection gracefully: the client sends FIN to signal it’s done, the server replies with ACK, then the server sends its own FIN after finishing data, and the client responds with ACK. After this exchange, the connection is fully CLOSED, allowing each side to terminate independently.

Visual summary

    Client                    Server
    | ------ FIN ----------> |
    | <------ ACK ---------- |
    | <------ FIN ---------- |
    | ------ ACK ----------> |
            Connection closed

# Transport Layer Security (TLS) HTTPS Upgrade

HTTPS is HTTP secured with TLS, encrypting data to prevent eavesdropping and man-in-the-middle attacks, typically over port 443. It uses asymmetric encryption (public/private keys) to authenticate the server and securely exchange a symmetric session key, which is then used for fast bulk data encryption. During the TLS handshake, the client requests HTTPS, the server sends its CA-signed certificate containing its public key, the client verifies the certificate using the CA’s public key, and both sides establish a shared symmetric key for secure communication. This protects against attackers who cannot fake the server’s identity or decrypt traffic, and certificates are issued by trusted Certificate Authorities via a Certificate Signing Request (CSR), ensuring the server’s public key truly belongs to it.

    Client                                           Server
    |                                               |
    | 1. "I want to view YouTube.com"             ->|
    |                                               |
    |<- 2. "Here is my certificate (CERT) that 
    |      contains my public key. The CERT was 
    |      signed by Google Certificate Authority (CA)"
    |                                               |
    | 3. "I trust Google CA and I have Google CA's 
    |      public key. It seems your identity is valid.
    |      I have created a new symmetric private key 
    |      and encrypted it with your public key"  ->|
    |                                               |
    |<- 4. "Nobody else knows our new symmetric 
            private key. Let's encrypt everything 
            using the new key from now on" 
    |                                               |


## One-sentence HTTPS summary (interview gold)

HTTPS uses asymmetric encryption to authenticate and exchange a symmetric key, then switches to fast symmetric encryption for secure data transfer.


# HTTP Request and Response

HTTP is a protocol for transferring data between a client and a server. The client sends an HTTP request—commonly a GET to fetch data or a POST to submit data—specifying the URL, headers, and optionally a body. The server processes the request using request handlers, checks headers and body, and responds with an HTTP response containing headers, a body, and a status code (e.g., 200 OK). HTTP headers control caching, compression, authorization, and provide metadata such as user-agent. URLs include the domain (server), path (resource), and query parameters (filters or state). This request/response cycle forms the basic workflow of web communication.

# Network Layer Protocols

The network layer is mainly handled by the IP protocol, which assigns addresses to devices and routes traffic. Devices usually get their IPs from a DHCP server when they boot up. On private networks, you can pick any IPs, but for internet visibility, you need public IPs allocated by a Regional Internet Registry (RIR). Public IPs are globally unique and routers know how to reach them. For example, Apple owns addresses starting with 17.0.0.0 — the internet backbone routes packets to Apple’s routers when you send traffic to these addresses.


# User Datagram Protocol (UDP) 

Fast but Unreliable

User Datagram Protocol (UDP) is the machinegun of protocols. It offers few features on top of IP but is very fast. Spray and pray is the right way to think about this. It provides a simpler, connectionless service with no guarantees of delivery, ordering, or duplicate protection.

If you write an application that receives UDP datagrams, you'll be able to see where they came from (i.e. the source IP address and port) and where they're going (i.e. the destination IP address and port). But that's it! The rest is a binary blob.

Key characteristics of UDP include:

- Connectionless: No handshake or connection setup
- No guarantee of delivery: Packets may be lost without notification
- No ordering: Packets may arrive in a different order than sent
- Lower latency: Less overhead means faster transmission


# HTTP

Hypertext Transfer Protocol (HTTP) is the de-facto standard for data communication on the web. It's a request-response protocol where clients send requests to servers, and servers respond with the requested data.

HTTP is a stateless protocol, meaning that each request is independent and the server doesn't need to maintain any information about previous requests. This is generally a good thing. In system design you'll want to minimize the surface area of your system that needs to be stateful where possible. Most simple HTTP servers can be described as a function of the request parameters — they're stateless!

## Common Headers

    ------------------------------
    Request Headers (Client → Server)
    ------------------------------
    Header             | Purpose
    ------------------ | -------------------------------------------------------------
    Host               | Specifies the domain being requested (required in HTTP/1.1)
    User-Agent         | Identifies the client software (browser, OS, version)
    Accept             | Types of content client can handle (HTML, JSON, images)
    Accept-Encoding    | Compression formats supported (gzip, deflate)
    Authorization      | Credentials for authentication (Basic, Bearer token)
    Cookie             | Stores session or user-specific data
    Content-Type       | Type of the request body (JSON, form-data)
    Content-Length     | Size of request body in bytes

    ------------------------------
    Response Headers (Server → Client)
    ------------------------------
    Header             | Purpose
    ------------------ | -------------------------------------------------------------
    Content-Type       | Type of the response body (HTML, JSON, image)
    Content-Length     | Size of the response body
    Set-Cookie         | Instructs the client to store a cookie
    Cache-Control      | Controls caching behavior of the response
    Expires            | When cached content becomes stale
    Server             | Identifies the server software (Nginx, Apache)
    Content-Encoding   | Compression applied to the response (gzip, br)
    Location           | Redirect URL (for 3xx responses)
    WWW-Authenticate   | Challenges client for authentication (401 responses)


## Common Request Methods

    GET: Request data from the server. GET requests should be idempotent and don't have a body.
    POST: Send data to the server.
    PUT: Update data on the server.
    PATCH: Update a resource partially.
    DELETE: Delete data from the server. DELETE requests should be idempotent.


## Common Status Codes

    Success (2xx)
        200 OK: The request was successful
        201 Created: The request was successful and a new resource was created
    Moved (3xx)
        302 Found: The requested resource has been moved temporarily
        301 Moved Permanently: The requested resource has been moved permanently
    Client Error (4xx)
        404 Not Found: The requested resource was not found
        401 Unauthorized: The request requires authentication
        403 Forbidden: The server understood the request but refuses to authorize it
        429 Too Many Requests: The client has sent too many requests in a given amount of time
    Server Error (5xx)
        500 Server Error: The server encountered an error
        502 Bad Gateway: The server received an invalid response from the upstream server

## Authentication
- Even though HTTPS encrypts requests and responses, it doesn’t guarantee that the data is legitimate
- APIs should never trust client-supplied identifiers like user IDs in the request body.
- Instead, the server should derive user identity from trusted sources such as a signed JWT token or a session
- Always validate and authorize access before performing sensitive operations.
- This prevents attackers from tampering with requests to access or modify other users’ data.

## Regionalization and Latency

Global services reduce outages by deploying across multiple availability zones and regions, but physical distance introduces unavoidable network latency due to speed-of-light limits. Requests to distant servers can add tens of milliseconds before any processing occurs, making geography a key performance factor. To minimize latency, systems should prioritize data locality—keeping data and computation close to each other and to users—by regionalizing infrastructure and placing user data near the services that access it, while accepting that some latency is fundamentally unavoidable.

### Content Delivery Networks (CDNs)

A Content Delivery Network (CDN) is a globally distributed network of edge servers that cache and serve content close to users, dramatically reducing latency and backend load. By returning cacheable data (like static assets or infrequently changing responses) from nearby locations, CDNs improve performance, scalability, and reliability, especially for global applications.

### Regional Partitioning

Regional partitioning improves performance and scalability by splitting data and services by geographic region, so users are served by nearby servers with locally relevant data. By co-locating regional services and databases (e.g., city- or region-based shards), systems minimize cross-region traffic, reduce latency, and better reflect real-world locality constraints, as seen in location-dependent apps like Uber.


## Handling Failures and Fault Modes

### Timeouts and Retries

To handle transient failures, systems use timeouts to stop waiting for slow requests and retries to attempt the request again. Retrying must be idempotent to avoid unintended effects. To prevent overwhelming the system, retries often use exponential backoff with jitter, meaning each retry waits progressively longer with some randomness, reducing synchronized retry storms and giving the system time to recover.

### Idempotency

Idempotent APIs ensure that multiple identical requests produce the same result without causing unintended side effects. This is crucial for operations like payments, where retrying a request could otherwise duplicate charges. For writes, idempotency is often implemented using a unique idempotency key per request, allowing the server to recognize and process each action only once, preventing duplicate effects while safely handling retries.

### Circuit Breakers

Circuit breakers are a design pattern to prevent cascading failures in distributed systems. They monitor calls to external services and, when failures exceed a threshold, “trip” to an open state where requests immediately fail without hitting the service. After a timeout, the circuit moves to a half-open state to test recovery. This prevents overwhelmed or failing services from bringing down the entire system, reduces load, enables self-healing, and improves user experience by failing fast instead of hanging. They are especially useful for external API calls, database connections, service-to-service communication, and any network or resource-intensive operations prone to failure.
