# Watsapp System Design

- [Watsapp System Design](#watsapp-system-design)
  - [Functional Requirements (Notes)](#functional-requirements-notes)
  - [Non-Functional Requirements](#non-functional-requirements)
  - [Core Entities](#core-entities)
  - [API Design](#api-design)
  - [High-Level Design](#high-level-design)
    - [Group chats with multiple participants (limit 100)](#group-chats-with-multiple-participants-limit-100)
    - [Users should be able to send/receive messages](#users-should-be-able-to-sendreceive-messages)
    - [Offline Message Delivery (Up to 30 Days)](#offline-message-delivery-up-to-30-days)
    - [Handling Media Attachments](#handling-media-attachments)

## Functional Requirements (Notes)
- Create group chats (up to 100 users)
- Send and receive messages
- Store and deliver messages to offline users (up to 30 days)
- Send and receive media in messages

Out of scope:
- Audio/video calling
- Business interactions
- User registration and profile management

## Non-Functional Requirements

- Low-latency message delivery (< 500 ms) for online users
- Guaranteed message delivery
- Scales to billions of users with high throughput
- Minimal message retention on centralized servers
- Fault tolerance against individual component failures

Out of scope:
- Detailed security considerations
- Spam and scraping prevention systems


## Core Entities
- Users
- Chats (2-100 users)
- Messages
- Clients (a user might have multiple devices)


## API Design
- Chat apps need high-frequency, low-latency, bidirectional communication.
- WebSockets are a better fit than REST for real-time messaging.
- Uses the real-time updates pattern: 
  - persistent connections
  - pub/sub for scale
  - stateful connections.
- Clients open a socket on app startup and use it to send and receive commands.
- The system API is defined by the messages exchanged over the WebSocket connection.

createChat
{
    "participants": [],
    "name": ""
} -> {
    "chatId": ""
}

sendMessage
{
    "chatId": "",
    "message": "",
    "attachments": []
} -> "SUCCESS" | "FAILURE"


createAttachment
{
    "body": ...,
    "hash": 
} -> {
    "attachmentId": ""
}

modifyChatParticipants
{
    "chatId": "",
    "userId": "",
    "operation": "ADD" | "REMOVE"
} -> "SUCCESS" | "FAILURE"


chatUpdate
{
    "chatId": "",
    "participants": [],
} -> "RECEIVED"


newMessage
{
    "chatId": "",
    "userId": ""
    "message": "",
    "attachments": []
} -> "RECEIVED"


## High-Level Design

### Group chats with multiple participants (limit 100)


  ┌──────────┐      WS Connection      ┌───────┐      ┌─────────────┐      ┌────────────┐
  │          │    ─────────────────▶   │       │      │             │      │            │
  │  Client  │                         │ L4 LB │ ───▶ │ Chat Server │ ───▶ │  Database  │
  │          │    ◀─────────────────   │       │      │             │      │ (DynamoDB) │
  └──────────┘                         └───────┘      └─────────────┘      └────────────┘

Steps:

1. User connects to the service and sends a `createChat` message.
2. The service, creates a `Chat` record in the database
Chat
- id
- name
- metadata
3. Inside a transaction, creates a `ChatParticipant` record for each user in the chat.
ChatParticipant
- chatId
- participantId
4. The service returns the `chatId` to the user.


### Users should be able to send/receive messages
- Uses the existing WebSocket connection to deliver messages in real time.
- Single Chat Server handles all WebSocket connections (simplified assumption).
- Chat Server keeps an in-memory map of userId → WebSocket.

Message Flow
- Client sends sendMessage over WebSocket.
- Server queries ChatParticipant table to get all participants of the chat.
- Server looks up each participant’s active WebSocket connection in an in-memory hash map (userId → WebSocket).
- Server pushes the message to each connected participant.

Assumptions:
- All users are online.
- All users are connected to the same Chat Server.
- One WebSocket per user (stored in-memory on that server).

             ┌──────────┐
             │  Client  │
             │   (U1)   │
             └─────┬────┘
                   │ WebSocket: sendMessage
                   ▼
             ┌──────────┐
             │ Chat     │
             │ Server   │
             │ (in-memory
             │  userId → WS map)
             └─────┬────┘
                   │ Lookup participants in ChatParticipant table
                   ▼
       ┌────────────────────────────────────┐
       │ Participants of Chat (U1,U2,U3...) │
       └─────┬─────────────┬────────────────┘
             │             │
             ▼             ▼
       ┌──────────┐   ┌──────────┐
       │  Client  │   │  Client  │
       │   (U2)   │   │   (U3)   │
       └──────────┘   └──────────┘


### Offline Message Delivery (Up to 30 Days)
- Users may be offline when a message is sent.
- System must store messages and deliver them when the user reconnects.

Architecture & Tables
- Message Table: stores all messages.
- Inbox Table: per-user table of undelivered messages.
- Partition key: userId → fast lookup for undelivered messages.
- TTL on both tables to automatically delete old messages (30-day retention).

Send a Message (Flow)
1. Client sends sendMessage to Chat Server.
2. Chat Server:
- Looks up participants via ChatParticipant table.
- Writes the message to Message table.
- In a transaction, writes an entry to each recipient’s Inbox table.
3. Returns SUCCESS/FAILURE to sender with message ID.
4. Attempts real-time delivery via WebSocket (newMessage) for connected clients.
5. Client sends ack → Chat Server deletes message from Inbox.

Offline Users
1. Messages stay in Inbox table until user reconnects.
2. When user connects:
- Lookup Inbox for undelivered messages.
- Fetch messages from Message table.
- Send over WebSocket (newMessage).
- Client ack → delete from Inbox.

Throughput & Scalability (Example)
- Average user: 20 messages/day
- 200M active users → ~4B messages/day (~40K/sec)
- Each 1:1 message → 2 writes (Message + Inbox)
- Group chats add some overhead → ~100K writes/sec total
- Within DynamoDB capabilities (with userId as partition key).

           ┌──────────┐
           │  Sender  │
           │  Client  │
           └─────┬────┘
                 │ sendMessage
                 ▼
           ┌──────────┐
           │ Chat     │
           │ Server   │
           └─────┬────┘
                 │
       1. Write message to Message table
                 │
                 ▼
           ┌──────────┐
           │ Message  │
           │  Table   │
           └─────┬────┘
                 │
       2. Create Inbox entries for recipients
                 │
                 ▼
           ┌──────────┐
           │ Inbox    │
           │  Table   │
           └─────┬────┘
                 │
       3. Attempt delivery to online recipients
                 │
      ┌──────────┴───────────┐
      │                      │
      ▼                      ▼
 ┌──────────┐           ┌──────────┐
 │ Client   │           │ Client   │
 │ Recipient│           │ Recipient│
 │   U2     │           │   U3     │
 └─────┬────┘           └─────┬────┘
       │ ack received         │ offline → no ack
       ▼                      │
  4. Delete from Inbox for    │
     U2 only                  │
                              │
                       5. U3 comes back online
                              │
                              ▼
                     ┌─────────────┐
                     │ Chat Server │
                     │  fetches    │
                     │  U3's Inbox │
                     └─────┬───────┘
                           │
                   6. Lookup messages in Message table
                           │
                           ▼
                     ┌──────────┐
                     │ Client   │
                     │   U3     │
                     └─────┬────┘
                           │ ack received
                           ▼
                     7. Delete U3's messages from Inbox
                           │
                           ▼
Message fully delivered to all recipients → Inbox cleared for everyone

### Handling Media Attachments

- Users should be able to send and receive media (images, videos, documents) in chat messages.
- Media can be large and bandwidth-heavy, so handling it through the same Chat Server as text messages is inefficient.


1. Bad solution of storing blob in database
Problems:
- Databases aren’t optimized for large files.
- Chat Servers’ bandwidth is wasted.
- Retrieval is complex and slow.

2. Blob storage via server
- Chat Server receives the attachment from the client.
- Chat Server pushes it to blob storage (e.g., S3) with a TTL of 30 days.
- Messages include a reference / URL to the attachment.
- Clients download attachments directly from blob storage (e.g., pre-signed URL).
- Optional: could expire media once all recipients have downloaded it.

Challenges / Limitations
- Chat Server still handles the upload
  - Chat Server’s bandwidth is used just to forward the file → wasted resources.
- No automatic expiry per recipient
  - Blob is deleted only via TTL; can’t easily expire after all recipients have retrieved it.
- Security & encryption
  - Chat Server must ensure media is encrypted, signed, and authorized → extra complexity.
- CDN not very helpful
  - With a max 100 participants per chat, caching benefits are small.

3. Manage attachments seperately
- Direct upload to blob storage:
  - Client requests a pre-signed URL from Chat Server (getAttachmentTarget).
  - Client uploads media directly to blob storage, bypassing Chat Server bandwidth.
- Reference in chat message:
  - Client receives the attachment URL after upload.
  - Sends the message to Chat Server with opaque attachment URL.
  - Chat Server stores the URL in the Message table.
- Download / retrieval:
  - Recipients fetch attachments directly from blob storage using pre-signed URLs.
  - Optional: could try to expire/delete attachments once all recipients have downloaded.
- Optional CDN:
  - Could cache attachments, but limited benefit for ≤100 participants per chat.

Benefits
- Chat Servers are not bottlenecks for uploads/downloads → scalable.
- Database remains lightweight (only stores URL, not the file).
- Durable storage handled by specialized blob storage.
- Works well for large files / high bandwidth media.

Challenges / Limitations
- Expiring attachments after all recipients have retrieved them isn’t automatic.
- Encryption and security need careful management (client-side or server-side).
- CDN benefits limited for small group sizes.
