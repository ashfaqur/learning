# Trace Tool Design

- A trace analysis tool that ingests trace packets from the chipset, decodes them, and correlates timestamps.
- Visualizes hardware, firmware, and software events in a desktop client, conceptually similar to Wireshark but for chipset trace data.
- Supports both live streaming capture and offline decoding of binary trace captures.

## Legacy Trace Tool Stack
- Desktop client built on Eclipse RCP (Java).
- Integrated with native C++ components via JNI:
  - Client starts → System.loadLibrary("traceNative").
  - Native library loaded into memory.
  - Java calls native methods (e.g., startTrace(), getPacket()) via JNI.
  - Native code handles low-level chipset communication.
  - Trace packets and events returned to Java client for visualization.

## Service Based Architecture

### Migration to gRPC-Based Services

Reasons for Moving from JNI/Desktop to gRPC Services:
- Logical separation of concerns with clearly defined service boundaries.
- Clear team ownership and maintainability of individual services.
- High-performance inter-service communication via binary protocol.
- Multi-language support through generated gRPC stubs.
- Proto files as API-first, self-documenting contracts.
- Better cross-team collaboration via proto code reviews.
- Decouples backend from desktop UI → enables web & other clients.
- Improved testability and CI compared to JNI integration.
- Incremental migration possible: legacy and new stacks run in parallel.
- Enhanced observability: logging, metrics, tracing.
- Flexible deployment and scaling.

### Protobuf + gRPC — TL;DR

- Protocol Buffers (protobuf): language-neutral, binary serialization format.
- Schema-first .proto files define data models and service APIs.
- Generates strongly-typed client & server code in multiple languages.
- gRPC uses protobuf to:
  - Define RPC service contracts.
  - Serialize request/response messages efficiently.
- Binary encoding → smaller payloads & faster performance than JSON/XML.
- Field numbering ensures backward & forward compatibility.
- Proto files act as self-documenting API contracts and collaboration points.
- Ideal for high-performance, internal, multi-language service communication.

### Prototype — Modernizing Trace Analysis

Goal: Explore evolving the trace analysis workflow from a desktop-centric tool to a modular, service-oriented architecture.

Frontend:
- Built with Svelte + Tailwind for fast development and small compiled output.
- Targeted both web app and lightweight desktop via Tauri.
- Packaged desktop build ~3–4 MB → easy to distribute alongside existing client.

Backend:
- Spring Boot REST layer acts as orchestrator between web UI and existing gRPC-based native services.
- REST layer serves as a browser-friendly proxy (browsers don’t natively support full gRPC over HTTP/2).
- Handles request mapping and provides a clean API boundary for the frontend.
- Enables incremental UI integration without modifying core services.

Key Outcomes:
- Same core trace decoding and processing logic reused across:
  - Legacy desktop client
  - Lightweight Tauri-based desktop
  - Browser-based client
- Validated that service-oriented architecture enables flexible UI options and a scalable, long-term system design.
