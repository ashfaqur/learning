# Live Contract - System Design

Live Contract is a real-time digital contract system that allows an advisor
and a customer to review, complete, and sign a contract together in a
single online session, with all actions tracked and legally recorded.

## Requirements

### Functional Requirements

1. Create a contract
- An advisor can create a new contract for a customer.
- The contract starts in a draft state.
2. Upload and view contract documents
- Users can upload a contract document (e.g. PDF).
- Both advisor and customer can view the same document.
3. Live session between advisor and customer
- Advisor and customer can join a shared session.
- Actions (e.g. page change, status updates) are reflected in real time.
 
Out of scope
1. Audit log
The system records important events:
- contract created
- document uploaded
- contract signed

### Non-Functional Requirements
1. Real-time responsiveness
- Live updates should be delivered with low latency (seconds or less).
2. Reliability
- Contract data and signatures must not be lost.
- Once signed, a contract cannot be modified.
3. Security
- Only authorized users can access a contract.
- Data must be encrypted in transit and at rest.
4. Scalability
- System should support many simultaneous live sessions.
5. Availability
- The system should remain usable even if some components fail.

Out of scope
1. Compliance & auditability
- All important actions must be traceable for legal reasons.



## Core Entities 

1. Contract (long-lived, source of truth)
- contractId
- advisorId
- customerId
- status DRAFT → REVIEW → SIGNED
- createdAt
- signedAt
Notes:
- Immutable once SIGNED
- No live/connection info here

2. Document (multiple per contract)
- documentId
- contractId
- fileUrl
Notes:
- One contract → many documents
- Stored in blob/object storage (S3)

3. Session (short-lived, real-time)
- sessionId
- contractId
- advisorId
- customerId
- status (ACTIVE | ENDED)
- startedAt
- endedAt?

Notes:
- Exists only during live interaction
- Can be recreated if connection drops
- Mostly managed in memory (Redis / WebSocket server)

4. User
- userId
- role (ADVISOR | CUSTOMER)

## API

1. Contract APIs (REST)
- POST /contracts → create a new contract
- GET /contracts/{id} → fetch contract details
- PATCH /contracts/{id} → update contract status (REVIEW → SIGNED)

2. Document APIs (REST)
- POST /contracts/{id}/documents → upload a document
- GET /contracts/{id}/documents → list all documents for a contract
- GET /documents/{id} → fetch a single document (download/view URL)

3. Session APIs (REST + WebSocket)

REST to start/end session:
- POST /contracts/{id}/session → start a live session
  - Response: sessionId, WebSocket URL
- DELETE /sessions/{id} → end session

WebSocket for real-time events:
- Connect to WebSocket
- wss://livecontract.example.com/sessions/{sessionId}
- pageChange (both ways)
- contractSigned (both ways)

## High Level Design

### Create a contract

+----------------+         HTTP POST          +----------------+        SQL Insert       +----------------+
|   Advisor UI   | ----------------------->  |  Backend/API   | -----------------------> |   Database     |
| (Frontend App) |                           |  (REST Server) |                          |  (Contracts)   |
+----------------+                           +----------------+                          +----------------+
        |                                           |
        | <----------------- Response -------------|
        |   { contractId }     |

POST /contracts
->
{
  contractId: "abc123",
  status: "DRAFT",
  createdAt: "2026-02-01T15:00:00Z"
}


Database:

contracts
- contractId
- advisorId
- customerId
- status
- createdAt

users:
- userid
- role

### Upload and view contract documents

POST /contracts/{contractId}/documents
->
{
  "documentId": "doc123",
  "contractId": "abc123",
  "fileUrl": "https://bucket.example.com/abc123.pdf",
}

Server processes the request:
- Validate user has access to the contract
- Generate documentId
- Upload file to Blob Storage (S3 / Azure / GCS) → get fileUrl
- Insert document metadata in Database

Database:

documents:
- documentId
- contractId
- fileUrl


Client view the document directly from the bucket url

GET /documents/{id}
-> 
{
  "documentId": "doc123",
  "fileUrl": "https://bucket.example.com/abc123.pdf",
}

### Live session between advisor and customer

Real-time updates:
- Page changes (scrolling or document navigation)
- Contract signing
- Optional: annotations or chat
- Low latency (seconds or less)

1. Create a session

POST /contracts/{id}/session -> sessionId

Stored in memory (redis):

sessionId -> {
    contractId,
    advisorId,
    customerId,
    status: ACTIVE | ENDED,
    startedAt,
    ephemeralState (optional: current page, annotations),
    WebSocket server info (optional for multi-server setup)
}


2. Connect to session

wss://livecontract.example.com/sessions/{sessionId}

3. Real-time events

loadDocument -> {"documentId": "doc123"}
pageChange	-> { pageNumber: 3, userId: "advisor123" }
contractSigned	->	{ signedBy: "customer456", signedAt: "2026-02-01T15:45:00Z" }
statusUpdate	->	{ status: "SIGNED" }

1. Update database

contracts
- contractId
- status

5. Close session

DELETE /sessions/{id}


