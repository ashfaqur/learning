# Dropbox
- Dropbox is a cloud-based file storage service that allows users to store and share files.
- It provides a secure and reliable way to store and access files from anywhere, on any device.


# Requirements
- Upload file
- Download file
- Share file
- Automatically sync files across devices.

Out of scope:
- Editing files.
- Viewing files without downloading.

# Non functional requirements
- Prioritize availability over consistency.
- Support large files – up to 50 GB.
- Secure and reliable, able to recover lost or corrupted files.
- Low latency – uploads, downloads, and syncs should be fast.

Out-of-Scope:
- Storage limit per user.
- File versioning.
- Virus/malware scanning.

# Core entities
- File: The actual file data that users upload, download, and share.
- FileMetadata: Metadata about the file like name, size, type, owner, timestamps, etc.
- User: Represents a system user.

# API

Uploading a file

    POST /files
    Request:
    {
        File, 
        FileMetadata
    }

 Dwnload a file

 GET /files/{fileId} -> File & FileMetadata


Share a file

    POST /files/{fileId}/share
    Request:
    {
        User[] // The users to share the file with
    }

Query for changes to files

    GET /files/{fileId}/changes -> FileMetadata[]


# High Level Design

## Upload
- Store the file contents (the raw bytes)
- Store the file metadata

Document Schema:

  {
    "id": "123",
    "name": "file.txt",
    "size": 1000,
    "mimeType": "text/plain",
    "uploadedBy": "user1"
  }

### Upload File to Single Server :
- Client uploads file to backend File Service.
- Backend stores file on its local filesystem.
- File metadata is saved in a database.

Why it doesn’t scale:
- Storage doesn’t scale easily as files grow.
- Hard to scale horizontally (files tied to specific servers).
- Single point of failure — server crash = files unavailable/lost.

Conclusion:
✅ Works for small apps
❌ Not reliable or scalable

### Store file in Blob Storage
- Store file contents in Blob/Object Storage (e.g., S3, GCS).
- Store file metadata in a database.
- Backend coordinates the upload process.

Why it’s better:
- Infinitely scalable storage.
- Highly reliable (files survive server failures).
- Built-in features like durability and lifecycle management.

Challenges:
- More complex integration.
- Must keep file upload and metadata in sync (transactional logic).
- Initial design uploads file twice (client → backend → blob).

### Upload Directly to Blob Storage:
- Client uploads files directly to Blob Storage using pre-signed URLs.
- Backend only handles metadata and permissions, not file bytes.

Upload Flow:
- Client requests a pre-signed upload URL from backend.
- Backend saves file metadata with status uploading.
- Client uploads file directly to Blob Storage using the URL.
- Blob Storage notifies backend → metadata status updated to uploaded.

Why this is best:
- Fastest uploads
- Cheapest (backend bandwidth avoided)
- Highly scalable
- Backend stays stateless

Backend manages metadata, Blob Storage handles file bytes.

┌──────────┐      1. Request Pre-signed URL      ┌─────────────┐
│          │────────────────────────────────────▶│             │
│          │                                     │     API     │
│ Uploader │      2. Return Pre-signed URL       │  Gateway &  │
│          │◀────────────────────────────────────│    Load     │
│          │                                     │  Balancer   │
└──────────┘                                     └─────────────┘
      │                                                 │
      │ 4. Upload file directly to S3                   │ (Routing, Auth,
      │    via pre-signed URL                           │  Rate Limiting)
      ▼                                                 ▼
┌──────────┐                                     ┌─────────────┐
│          │                                     │             │
│    S3    │                                     │    File     │
│          │                                     │   Service   │
│          │                                     │             │
└──────────┘                                     └─────────────┘
      │                                                 │
      │ 5. S3 Notification                              │ 3. Initial
      │    on completed upload                          │    Metadata
      ▼                                                 |
┌──────────────────┐                                    |
│                  │                                    |
│  File Metadata   │◀───────────────────────────────────|
│        DB        │                              
└──────────────────┘                              
  - fileId
  - name
  - size
  - mimeType
  - uploadedBy
  - status
  - s3Url (link to file in S3)

## Download

### Through Server
- Client requests file download from backend.
- Backend downloads the file from Blob Storage.
- Backend streams the file to the client.

Why this is bad
- File downloaded twice (Blob → backend → client)
- Higher latency
- Higher bandwidth cost
- Backend becomes a bottleneck
- Harder to scale for large files (50GB)

Conclusion
- Works for small systems
- Not suitable for Dropbox-scale systems

### From Blob Storage
- Client requests a pre-signed download URL from backend:
    GET /files/{fileId}/presigned-url
- Backend verifies access and returns a time-limited pre-signed URL.
- Client downloads the file directly from Blob Storage using the URL.

Why this is better
- No double download
- Lower latency
- Cheaper (backend bandwidth avoided)
- Scales well for large files

Remaining challenge
- Blob Storage lives in one region
- Users far away may experience slow downloads

### CDN
- Serve file downloads through a CDN in front of Blob Storage.
- Backend generates a time-limited, signed CDN URL for secure access.
- CDN serves the file from the closest edge location to the user.

Why this is best:
- Lowest latency for global users
- Reduced load on Blob Storage and backend
- Scales effortlessly for large downloads

Trade-offs / Challenges:
- CDNs are expensive
- Must control costs using:
  - Cache-Control headers (TTL)
  - Selective caching
  - Cache invalidation on updates/deletes


## Share File
- Users can share files with other users.
- Users can see files shared with them.

### Share list
- Add a sharelist array in file metadata:
{
  "id": "123",
  "name": "file.txt",
  "uploadedBy": "user1",
  "sharelist": ["user2","user3"]
}
- Check sharelist to authorize downloads.

Problem:
- Querying all files shared with a given user is slow — must scan all files.

### SharedFiles Table

| userId (PK) | fileId (SK) |
|-------------|-------------|
| user1       | fileId1     |
| user1       | fileId2     |
| user2       | fileId3     |

- No need of sharelist from file metadata.
- To get files shared with a user → query `SharedFiles` by `userId`.

### Trade-offs
- Query slightly slower (index lookup vs key-value)
- No syncing issues between file metadata and shared mapping
- Scales better for large numbers of users and shares

## Sync files across devices

Files are kept on each client device and in remote storage.

Sync works in two directions:

Local -> Remote
- Client monitors local folder for changes using OS file system events
- Changes are queued for upload
- Upload API sends file changes and updated metadata to the server
- Conflicts are resolved using a last write wins strategy

Remote -> Local
- Clients need to know when remote files change
- Two approaches:
  - Polling: client periodically checks server for updates
  - WebSocket/SSE: server pushes updates to clients in real-time

Hybrid approach
- Fresh files (recently edited) use WebSocket for near real-time sync
- Stale files (not recently edited) use periodic polling
- Provides real-time updates for active files while conserving resources for inactive ones
