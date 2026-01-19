- [JWT (JSON Web Token) Overview](#jwt-json-web-token-overview)
  - [JWT Stateless Authentication](#jwt-stateless-authentication)
  - [JWT Tampering Protection](#jwt-tampering-protection)
  - [JWT Signature Integrity](#jwt-signature-integrity)
  - [JWT Logout Handling](#jwt-logout-handling)
  - [Short-Lived JWT \& Refresh Token](#short-lived-jwt--refresh-token)
  - [Client-Side Refresh Handling](#client-side-refresh-handling)
- [Cookie](#cookie)
  - [Cookies + JWT](#cookies--jwt)
  - [Why cookies matter](#why-cookies-matter)
- [Cookie Storage Responsibility](#cookie-storage-responsibility)
  - [How Cookies Are Stored](#how-cookies-are-stored)
- [Client-Side Storage Overview](#client-side-storage-overview)
  - [Cookies](#cookies)
  - [LocalStorage](#localstorage)
  - [SessionStorage](#sessionstorage)
  - [Memory](#memory)
- [Session-Based Authentication (Step-by-Step)](#session-based-authentication-step-by-step)
  - [Key Points](#key-points)


# JWT (JSON Web Token) Overview

- Purpose: Stateless authentication & authorization token.
- Structure: Header + Payload + Signature
  - Header: algorithm & type (`{"alg":"HS256","typ":"JWT"}`)
  - Payload: claims (`{"userId":"123","role":"admin","exp":<timestamp>}`)
  - Signature: HMAC/RSA signature to prevent tampering
- Lifecycle:
  1. User logs in → server verifies credentials
  2. Server issues JWT → sent to client
  3. Client stores JWT → localStorage/cookie
  4. Client sends JWT in `Authorization: Bearer <token>` header
  5. Server verifies signature & expiration → grants access
  6. Optional: refresh token flow for new JWTs
- Algorithms: HS256 (symmetric), RS256 (asymmetric)
- Security Tips: HTTPS, short expiration, secure storage, avoid sensitive payload data

## JWT Stateless Authentication

- JWT contains all user identity & claims in payload.
- Signature proves token integrity & authenticity.
- Server can **verify token and authorize request** without DB lookup.
- DB may still be needed if:
  - Roles/permissions changed dynamically
  - Token revocation / logout support
  - Additional user info required for request
- Advantage: stateless, scalable, fewer DB queries.

## JWT Tampering Protection
- JWT payload can be decoded and modified by the client
- JWT is **signed**, not encrypted
- Any change to header or payload invalidates the signature
- Server recomputes signature using secret/private key
- Mismatch → token rejected
- Client cannot forge a valid JWT without server key
- JWT does NOT hide data → never store sensitive info in payload

## JWT Signature Integrity
- JWT signature is created from **header + payload** by the server
- Signature uses server secret/private key
- Any change to header or payload:
  - Changes signature input
  - Causes signature verification to fail
- Client cannot recreate a valid signature
- Server recomputes signature and compares
- Mismatch → token rejected

## JWT Logout Handling

- JWT is stateless → server does not track sessions.
- Manual logout = client deletes JWT from storage.
- Challenges:
  - JWT is still valid until expiration.
  - Stolen tokens remain usable until expiry.
- Common solutions:
  1. **Client-side logout:** delete JWT from storage.
  2. **Short-lived JWT + refresh tokens:** minimize window of exposure.
  3. **Token blacklist (server-side revocation):** mark JWT as invalid before expiration.
- Best practice: combine short-lived JWTs with refresh tokens; blacklist only if immediate revocation needed.

## Short-Lived JWT & Refresh Token

Short-lived JWT:
- Valid for a short time (e.g., 15 min)
- Contains user info and expiration (`exp` claim)
- Limits exposure if token is stolen

Refresh Token:
- Long-lived token, securely stored
- Used only to obtain new JWTs after expiry
- Usually verified in DB/cache
- Can be revoked for logout or security

Flow:
1. User logs in → server returns short-lived JWT + refresh token
2. Client uses JWT for API requests
3. JWT expires → client sends refresh token to `/refresh` endpoint
4. Server verifies refresh token → issues new JWT
5. Repeat until refresh token expires or is revoked

## Client-Side Refresh Handling
- Browser does NOT wait or retry automatically
- Client must:
  - Detect `401 Unauthorized`
  - Call `/refresh`
  - Wait for response
  - Update access token
  - Retry failed requests
- Only ONE refresh request should run at a time
- Other requests must wait (queue)
- If refresh fails → logout user

# Cookie

- Cookie = small data stored by browser for a domain
- Browser automatically sends cookies with requests
- Cookies exist because HTTP is stateless
- Cookies are transport, not auth logic

## Cookies + JWT
- JWT = identity + claims + signature
- Cookies = secure storage & transport
- Best practice:
  - Access JWT → memory / Authorization header
  - Refresh token → HttpOnly Secure cookie

## Why cookies matter
- Automatic sending by browser
- Protected from JS (HttpOnly)
- Enable secure refresh token flow

# Cookie Storage Responsibility
- Cookies are stored by the **browser**, not JavaScript
- Server sets cookies via `Set-Cookie` header
- Browser automatically:
  - Stores cookies
  - Sends cookies with matching requests
- JavaScript cannot read or modify `HttpOnly` cookies
- This makes cookies ideal for storing refresh tokens

## How Cookies Are Stored
- Server sends cookie **instructions** via `Set-Cookie`
- Browser stores cookies based on:
  - Name
  - Domain
  - Path
- If cookie exists → browser replaces it entirely
- No partial updates or merging
- Server deletes cookie by setting `Max-Age=0`
- Browser manages storage; JS is not involved


# Client-Side Storage Overview

## Cookies
- Stored by browser per domain
- HttpOnly = JS cannot access (secure)
- Automatically sent with matching requests
- Good for refresh tokens

## LocalStorage
- JS-accessible key-value store
- Persistent across tabs and reloads
- Not automatically sent with requests
- Good for short-lived access tokens (but XSS risk)

## SessionStorage
- Like localStorage, but only lasts until tab/window closes
- JS-accessible

## Memory
- Stored in JS variable
- Exists only while page is loaded
- Safest for sensitive short-lived tokens

# Session-Based Authentication (Step-by-Step)

1. User submits credentials via POST /login
2. Server verifies credentials in DB
3. Server creates session and stores server-side
4. Server sends session ID to client in `Set-Cookie`
5. Browser automatically sends cookie on future requests
6. Server looks up session by session ID, authenticates & authorizes
7. Logout: server deletes session, browser deletes cookie
8. Sessions can expire based on inactivity or max lifetime

## Key Points
- Stateful (server keeps session)
- Cookies handle automatic transport
- HttpOnly + Secure protect cookie
- Authorization comes from server session data

