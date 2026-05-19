# SecureX Core Architecture Blueprint 📐

This document serves as the top-level architectural map for SecureX. Detailed module designs are broken down in the `docs/` directory.

---

## 🏛️ System Topology (High-Level)

The SecureX architecture consists of high-throughput telemetry ingestion, decoupled event processing, state correlation, real-time alerting, and an interactive analyst workspace.

```
       [ Telemetry Sources (Apps/Microservices using Node.js SDK) ]
                                    │
                                    ▼ (HTTPS/TLS Ingestion API)
                       ┌─────────────────────────┐
                       │      Ingestion API      │
                       └────────────┬────────────┘
                                    │
                                    ▼ (Stream Buffer / Queue)
                       ┌─────────────────────────┐
                       │     Ingestion Queue     │
                       └────────────┬────────────┘
                                    │
                                    ▼ (Worker Nodes)
                       ┌─────────────────────────┐
                       │   Processing Engine     │
                       └─────┬─────────────┬─────┘
                             │             │
                             ▼             ▼
          ┌─────────────────────┐       ┌──────────────────────┐
          │  Detection Engine   │       │  Correlation Engine  │
          └──────────┬──────────┘       └──────────┬───────────┘
                     │                             │
                     └──────────────┬──────────────┘
                                    ▼ (Real-time Stream)
                       ┌─────────────────────────┐
                       │     WebSocket Hub       │
                       └────────────┬────────────┘
                                    │
                                    ▼ (Push updates)
                       ┌─────────────────────────┐
                       │  Analyst UI Workspace   │
                       └─────────────────────────┘
```

---

## ⚡ Key Architectural Decisions (ADRs)

To understand specific decisions, refer to their comprehensive designs in the following modules:

1. **Node.js-Only Telemetry Integration**:
   - *Decision*: Restrict the initial telemetry agent interface to a Node.js-based SDK.
   - *Rationale*: Solves immediate scope sprawl and allows optimizing ingestion schemas for clean application-level security telemetry before dealing with OS-level agents.
   - *Reference*: [docs/05-integrations/integration-overview.md](docs/05-integrations/integration-overview.md)

2. **Decoupled Queue Ingestion**:
   - *Decision*: Run an asynchronous processing queue directly behind the Ingestion API endpoint.
   - *Rationale*: Absorbs sudden traffic/telemetry spikes (e.g., during a credential stuffing attack) without degrading application performance or dropping logs.
   - *Reference*: [docs/08-infrastructure/queue-system.md](docs/08-infrastructure/queue-system.md)

3. **SIEM-Inspired Structured Logging**:
   - *Decision*: Require structured, JSON-validated events at ingestion.
   - *Rationale*: Simplifies query processing and makes rules within the detection engine deterministic and low-latency.
   - *Reference*: [docs/03-core-modules/logging-system.md](docs/03-core-modules/logging-system.md)

---
*For a complete walkthrough of the modules, start with the [Platform Overview](docs/02-platform-overview/platform-overview.md).*
