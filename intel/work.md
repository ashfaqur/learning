# Intel Work

At Intel I worked on a system-level trace analysis tool that ingested trace packets from the chipset via Intel Trace Hub, decoded and timestamp-correlated them, and visualized hardware, firmware, and software events in a desktop client — similar conceptually to Wireshark, but for chipset trace data. The tool supported both live streaming capture and offline decoding of binary trace captures


I spent around ten years at Intel, and it was a very rewarding journey — I joined as a junior developer and progressively took on more responsibility over the years. My primary focus was the Java-based desktop client used to visualize chipset trace data.

Beyond development, I also played a key role in the release process — helping debug issues, coordinating with stakeholders across teams, and ensuring that our four-week release cadence ran smoothly and reliably

I took parental leave after leaving Intel and used that time both for family and to explore new technologies I’m interested in. Now I’m looking for a role that aligns with my long-term growth direction.

## Why leave Intel

In the last few years at Intel, our team was migrating our trace analysis tool from a monolithic architecture to a more service-based model. The desktop client — which had previously integrated directly with native C++ components via JNI — was being transitioned to communicate with new native services over gRPC.

It was a challenging but very interesting phase, because we had to support both the legacy stack and the new service-based architecture in parallel while continuing to deliver features on the client side.

Once parts of that migration were completed, it opened the door to new possibilities — especially around building web-based applications on top of the new distributed service layer. I worked on some prototypes in that direction, and through that experience I realized that I really enjoy working with modern service architectures and contributing across the full stack of a web-based application.

To continue growing in that direction, it became clear that I would need to look outside Intel for roles more closely aligned with those technologies. The timing worked well, because I was also able to take parental leave and use that period both for family and to deepen my knowledge in those areas. Now I’m looking for a role where I can apply that experience and continue developing in that space.

## Eclipse RCP desktop client to JNI

In legacy stack:
- The Eclipse RCP client (Java) starts
- It calls System.loadLibrary("traceNative")
- The native library is loaded into memory
- Java calls methods via JNI, e.g., startTrace(), getPacket()
- Native code handles low-level chipset communication
- Results (packets, events) are returned to the Java desktop client for visualization

While this works, it couples your client tightly to native code — which is why moving to a service-based architecture with gRPC helped decouple the Java UI from platform-specific native components.

TL;DR
- JNI loads the whole native library at once, not function-by-function.
- Java native methods are just bindings to functions inside the library.
- JVM handles type conversion and bridging calls between Java ↔ C/C++.
- Your RCP client used JNI to call native code for trace capture and decoding.

## Migration from JNI Monolith to gRPC Services

### Legacy Architecture
- Eclipse RCP Java desktop client
- Java ↔ native C++ via JNI
- Native C++ libraries configured with XML
- Tight coupling between UI and backend logic
- Difficult to scale, test, and evolve independently

### Reasons for Moving to gRPC-Based Services
- Separation of concerns via logical service boundaries
- Clear team ownership of individual services
- High-performance inter-service communication (binary protocol)
- Multi-language support via generated gRPC stubs
- Proto files as API-first, self-documenting contracts
- Improved cross-team collaboration through proto code reviews
- Decoupling backend from desktop UI (enables web & other clients)
- Better testability and CI compared to JNI integration
- Incremental migration with legacy and new stacks running in parallel
- Improved observability (logging, metrics, tracing)
- Flexible deployment and scalability

## Protobuf + gRPC — TL;DR

- **Protocol Buffers (protobuf)** is a language-neutral, binary serialization format
- Uses **schema-first `.proto` files** to define data models and service APIs
- Generates strongly-typed client & server code in multiple languages
- **gRPC uses protobuf** to:
  - define RPC service contracts
  - serialize request/response messages efficiently
- Binary encoding → **smaller payloads & faster performance** than JSON/XML
- Field numbering enables **backward & forward compatibility**
- Proto files act as **self-documenting API contracts** and collaboration points
- Ideal for **high-performance, internal, multi-language service communication**


## Prototype

In my prototype, the goal was to explore how our trace analysis workflow could evolve from a desktop-centric tool into something more modern, modular, and service-oriented.

For the frontend, I chose Svelte with Tailwind. Svelte gave a very fast development ramp-up and its compiled output is extremely small, which was important because I wanted the UI to be usable both as a web app and as a lightweight desktop alternative via Tauri. With this approach, the packaged desktop build was only around 3–4 MB, making it practical to distribute alongside the existing client rather than replacing it outright.

On the backend, I introduced a Spring Boot REST layer that acted as an orchestrator between the web UI and our existing gRPC-based native services. The services already exposed gRPC APIs, but browsers don’t support full gRPC over HTTP/2 natively, so the REST layer served as a proxy, handled request mapping, and provided a clean API boundary for the frontend. It also allowed us to gradually integrate new UI functionality without changing the service layer.

This architecture let me demonstrate that the same core decoding and trace processing logic could be reused across multiple delivery models — legacy desktop, lightweight desktop via Tauri, and a browser-based client — while keeping the backend logic centralized in the service layer. The prototype helped validate that a service-oriented approach opens the door to more flexible UI options and a more scalable long-term architecture.

