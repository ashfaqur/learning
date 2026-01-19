- [API Design](#api-design)
  - [API Protocols:](#api-protocols)
  - [Resource Modeling:](#resource-modeling)
  - [HTTP Methods:](#http-methods)
  - [Design Principle:](#design-principle)
- [Common API Patterns](#common-api-patterns)
  - [Pagination](#pagination)
  - [Versioning](#versioning)
- [API Security:](#api-security)
  - [Authentication vs Authorization:](#authentication-vs-authorization)
  - [API Keys vs JWT Tokens:](#api-keys-vs-jwt-tokens)
  - [Role-Based Access Control (RBAC)](#role-based-access-control-rbac)
  - [Rate Limiting/Throttling](#rate-limitingthrottling)


# API Design

## API Protocols:
- Default to REST for most use cases
- GraphQL is for flexible data fetching
- RPC (like gRPC) is for high-performance internal services.
- Real-time needs (chat, notifications) use WebSockets or SSE.

## Resource Modeling:
- Represent system entities, not actions.
- Use plural nouns for resources (e.g., /events, /bookings).
- Nested paths for required relationships (/events/{id}/tickets)
- Query parameters for optional filters (/tickets?event_id=123).

## HTTP Methods:
- GET retrieves data (idempotent)
- POST creates new resources (non-idempotent)
- PUT replaces a resource (idempotent)
- PATCH updates partially (idempotent)
- DELETE removes resources (idempotent).

## Design Principle:
- Focus on identifying correct resources and interactions
- URL perfection is less important than showing proper resource mapping and HTTP semantics understanding.

# Common API Patterns

## Pagination
- Break large datasets into smaller chunks.
- Offset-based: /events?offset=20&limit=10 – simple but can skip or duplicate records if data changes.
- Cursor-based: /events?cursor=<last_id>&limit=10 – more stable with real-time data, uses a pointer to the last record.

## Versioning
- Handle API evolution without breaking clients.
- URL versioning: /v1/events, /v2/events – explicit, easy to understand, common in interviews.
- Header versioning: Accept-Version: v2 – cleaner URLs but less obvious and harder to test.

# API Security:

## Authentication vs Authorization:
- Authentication: Verifies who is making the request.
- Authorization: Checks if the user can perform the requested action.

## API Keys vs JWT Tokens:
- API Keys: For server-to-server communication or third-party access; tied to an application, not a user.
- JWT Tokens: For user sessions; encode user info and permissions, stateless, easy to verify in distributed systems.

## Role-Based Access Control (RBAC)

Assign roles to users and permissions to roles (e.g., customer, venue_manager, admin) to control access to endpoints.

## Rate Limiting/Throttling

Restrict requests per user, IP, or endpoint to prevent abuse; typically enforced at the API gateway or via middleware.
