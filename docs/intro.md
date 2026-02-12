---
sidebar_position: 1
---

# System Architecture Concept Note

## 1. Architectural Overview

Rahat V2 follows a **hub-based, service-oriented architecture**.
All services communicate through a central Hub, ensuring modularity, scalability, and controlled service access.

The system is divided into:

* Hub / Registry
* Core Service
* Project Layer (CVA)
* Service Providers
* Payment Protocol Layer
* Messaging Layer

---

# 2. Hub / Registry

## Purpose

The **Hub** acts as the central communication router of the Rahat system.

## Responsibilities

### 2.1 Message Routing

* Receives incoming service requests.
* Identifies destination service.
* Forwards request to the correct service provider.

### 2.2 Queue Management

* Maintains message queues.
* Prevents service overload during high traffic.
* Ensures controlled, asynchronous processing.

### 2.3 Service Registry

* Registers all authorized services.
* Stores service metadata (address, capabilities, pricing).
* Ensures only registered services can communicate.

---

# 3. Core Service

The **Core Service** manages global system-level operations.

## Responsibilities

### 3.1 Billing & Accounting

* Records payment made per project.
* Tracks service usage per project.
* Maintains service-level cost records.
* Generates billing reports.

### 3.2 Reporting

* Stores system-wide reporting data.
* Stores project-specific reports.
* Aggregates service usage analytics.
* Provides structured reporting APIs.

The Core acts as the financial and reporting backbone of Rahat.

---

# 4. CVA (Cash Voucher Assistance) – Project Layer

The CVA service manages project-specific configurations.

## Responsibilities

* Stores project-level configurations.
* Maintains project-specific beneficiaries.
* Maintains project-specific vendors.
* Handles project-specific graph queries.
* Manages project-level permissions.

Important:

* Beneficiaries and vendors exist within project scope.
* Graph calls are made via the CVA service.

---

# 5. Service Providers

Rahat uses modular service providers that perform specific tasks.

Each service:

* Registers to the Hub
* Declares pricing
* Provides wallet address for payment
* Communicates via standard message schema

For CVA, the primary services include:

---

## 5.1 SMS Service

* Sends single or bulk SMS.
* Handles delivery tracking.
* Returns delivery status.
* Verifies payment before sending.

---

## 5.2 IVR Service

* Sends automated voice calls.
* Handles beneficiary interaction.
* Stores IVR responses.
* Verifies payment before execution.

---

## 5.3 Vendor OTP Service

* Generates secure OTP for vendor redemption.
* Validates token claims.
* Manages vendor rules.
* Replaces legacy OTP servers.

---

## 5.4 Chain Call Service

* Handles token distribution.
* Executes blockchain transactions.
* Supports:

  * Simple token transfers
  * Signed contract function calls
  * Meta-transactions (MetaTx)
* Forwards signed transactions to blockchain.
* Abstracts blockchain complexity from projects.

---

# 6. Payment Protocol Integration

Rahat V2 uses a **pay-per-service model** integrated at the protocol level.

Each service defines:

* Price
* Accepted token
* Network
* Wallet address

---

## 6.1 Payment Flow

1. Hub receives service request.
2. Hub checks service pricing metadata.
3. Hub generates/signs payment transaction.
4. Payment proof is attached to request.
5. Service verifies payment proof.
6. Service executes task.

---

## 6.2 X402 Protocol Example

When payment is required:

```
HTTP/402 Payment Required
Pay-To: 0x71C7...
Amount: 0.05
Token: USDC
Network: Base
```

After payment:

```
POST /api/v1/send-sms
Content-Type: application/json
X-402-Proof: TxHash:5f89...a2bc; Sender:0xAbc...123
```

The service verifies:

* Transaction hash
* Sender address
* Correct amount
* Correct token
* Correct network

Only then does execution proceed.

---

# 7. Messaging Mechanism

All services communicate via the Hub.

## 7.1 Communication Rules

* Only registered services may communicate.
* All communication passes through Hub.
* No direct service-to-service communication.
* Standardized message schema enforced.

---

## 7.2 Standard Message Schema

```json
{
  "origin": "service_address",
  "destination": "service_address",
  "message_content": "action_type",
  "payload_content": {
    "data": "..."
  }
}
```

### Field Descriptions

* origin: Address of the sending service
* destination: Target service
* message_content: Type of request (e.g., SEND_SMS, ISSUE_TOKEN)
* payload_content: Structured data required by the service

---

# 8. Error Handling

Each service must return standardized error codes.

Examples:

* 1001 – Service Not Registered
* 1002 – Insufficient Payment
* 1003 – Invalid Payment Proof
* 2001 – Invalid Payload
* 3001 – Queue Timeout
* 4001 – Blockchain Transaction Failed

A unified error format ensures:

* Easier debugging
* Standard logging
* Consistent client handling

---

# 9. Design Principles of Rahat V2

* Modular microservice architecture
* Pay-per-use service economy
* Blockchain-agnostic execution layer
* Queue-based load protection
* Project-level isolation
* Service registration and verification
* Secure payment verification before execution

---

# 10. Architectural Advantages

This design enables:

* Scalable service marketplace model
* Third-party service integration
* Automatic billing
* Reduced system overload
* Clear financial transparency per project
* Clean separation between:

  * Project logic
  * Blockchain execution
  * Communication services
  * Billing layer

