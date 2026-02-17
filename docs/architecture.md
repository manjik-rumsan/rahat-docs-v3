# Architecture 

Rahat V2 follows a **hub-based, service-oriented architecture** where all components communicate through a central Hub. This design ensures modularity, scalability, and clean separation between business logic, service execution, and system coordination.


## System Components

**Hub / Registry** — The central communication router that manages service registration, message routing, and queue-based request processing. All inter-service communication flows through the Hub.

**Apps** — High-level components that orchestrate business workflows:
- **Core App**: Handles billing, accounting, and system-wide reporting
- **CVA App**: Manages project configurations, beneficiaries, vendors, and assistance workflows

**Services** — Independent, stateless components that execute specific tasks (SMS, IVR, WhatsApp, OTP generation, token disbursement, etc.). Services register with the Hub, declare their pricing, and verify payment before execution.

**Payment Layer** — Integrates pay-per-use pricing where apps attach payment proofs to service requests, and services validate payment before processing.

**Communication Layer** — Queue-based architecture where the Hub and each service maintain their own message queues for asynchronous, controlled processing.

## Flow

1. Services register themselves with the Hub, declaring capabilities and pricing
2. Apps initiate workflows by sending requests to the Hub with payment proofs
3. Hub routes messages to appropriate services via queues
4. Services validate payment and execute tasks
5. Results flow back through the Hub to the requesting app

For detailed information on each component, see the [System Architecture Concept Note](intro.md).

![Architecture diagram](/img/architecture.png)
