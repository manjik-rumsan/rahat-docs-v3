---
sidebar_position: 2
---


# Rahat V2 — Terminologies & Standards

This section defines the core terms, components, and standards used across the Rahat V2 architecture. It ensures consistent understanding for developers, integrators, and service providers building on the Rahat ecosystem.

---

## 1. Core Terminologies

---

## 1.1 Service

A **Service** is an independent, stateless component that **consumes requests and performs a specific function**.

Services do not manage projects directly. They execute tasks when invoked through the Hub and return results.

### Characteristics of a Service

* Consumes standardized requests.
* Implements a defined input/output interface.
* Declares pricing and payment configuration.
* Registers itself with the Hub.
* Verifies payment before execution.
* Returns standardized responses and errors.

Services focus on execution, not orchestration.

---

## 1.2 Service Types

**Service Types** define the contract (input/output schema) for a category of services.

They specify:

* Required input parameters.
* Output format.
* Supported actions.
* Payment model.
* Validation rules.

Service Types allow Rahat to plug multiple implementations under the same interface.

### Standard Service Types in Rahat

| Service Type           | Description                                                                     |
| ---------------------- | ------------------------------------------------------------------------------- |
| SMS                    | Sends text messages to beneficiaries or vendors.                                |
| IVR                    | Sends automated voice calls and collects responses.                             |
| WhatsApp               | Sends messages over WhatsApp channels.                                          |
| Vendor OTP             | Generates and validates redemption codes for vendors.                           |
| Token Disbursement     | Distributes blockchain-based tokens or executes contract calls.                 |
| Beneficiary Management | Handles beneficiary lifecycle operations (create, update, verify, deduplicate). |

> Note: Beneficiary Management is treated as a service interface for project apps, even though ownership stays at project scope.

---

## 1.3 App

An **App** is a higher-level component that **implements business logic by orchestrating services**.

Apps do not execute low-level tasks directly. Instead, they invoke services through the Hub.

### Characteristics of an App

* Implements workflows.
* Owns project data.
* Calls services via Hub.
* Pays for service usage.
* Produces reporting data.

### Standard Apps in Rahat

| App  | Description                                                               |
| ---- | ------------------------------------------------------------------------- |
| Core | Manages billing, accounting, reporting, and system-level logic.           |
| CVA  | Manages project configuration, beneficiaries, vendors, and CVA workflows. |

Apps represent Rahat’s product logic, while services represent execution units.

---

## 1.4 Hub

The **Hub** is the central communication and coordination layer of Rahat.

### Responsibilities

* Registers services and apps.
* Routes messages.
* Attaches payment proofs.
* Manages queues.
* Enforces communication standards.
* Verifies registration before forwarding messages.

The Hub ensures controlled, secure, and paid communication between all components.

---

## 1.5 Registry

The **Registry** is the metadata layer managed by the Hub.

It stores:

* Service identity.
* Service type.
* Pricing and payment info.
* Wallet address.
* Supported actions.
* App metadata.

Only registered entities can communicate within Rahat.

---

## 1.6 Delegation

**Delegation** allows an App to authorize another service or component to act on its behalf.

It defines:

* Scope of action.
* Time validity.
* Allowed service types.
* Payment authority.

Delegation enables secure automation without exposing private credentials.

Example use cases:

* CVA delegates token distribution to Chain Call.
* Core delegates billing execution to Hub.

---

# 2. Standards

---

## 2.1 Messaging Flow Standard

All messages follow a standard lifecycle:

1. App creates request.
2. Hub validates sender registration.
3. Hub attaches payment proof.
4. Hub queues message.
5. Hub routes to destination service.
6. Service verifies payment.
7. Service executes logic.
8. Service returns response.
9. Hub forwards response to sender.

No direct communication bypassing Hub is allowed.

---

## 2.2 Registration Schema Standard

Each Service and App must register with the Hub using a standard schema.

### Registration Fields

* id
* name
* type (service / app)
* serviceType (if service)
* supportedActions
* endpoint
* walletAddress
* pricingModel
* network
* version

This allows dynamic discovery and validation.

---

## 2.3 Error Schema & Codes

All services must return standardized errors.

### Error Object

```json
{
  "errorCode": 1002,
  "errorType": "PAYMENT_ERROR",
  "message": "Insufficient payment",
  "details": {}
}
```

### Example Error Codes

| Code | Type             | Description                   |
| ---- | ---------------- | ----------------------------- |
| 1001 | REGISTRY_ERROR   | Service not registered        |
| 1002 | PAYMENT_ERROR    | Insufficient payment          |
| 1003 | PAYMENT_ERROR    | Invalid payment proof         |
| 2001 | VALIDATION_ERROR | Invalid payload               |
| 2002 | VALIDATION_ERROR | Missing parameters            |
| 3001 | QUEUE_ERROR      | Queue timeout                 |
| 4001 | CHAIN_ERROR      | Blockchain transaction failed |
| 5001 | SERVICE_ERROR    | Internal service failure      |

---

# 3. Messaging Schema

All Rahat messages follow a standard schema.

---

## 3.1 Sample Messaging Schema

```json
{
  "version": "1.0",
  "serviceType": ["SMS", "SEND"],
  "sender": "cva-app",
  "destination": "sms-service",
  "message": "SEND_SMS",
  "payment": {
    "txHash": "0xabc...",
    "token": "USDC",
    "amount": "0.05",
    "network": "Base"
  },
  "payload": {
    "to": ["+97798xxxxxxx"],
    "template": "Your voucher code is {{code}}",
    "data": {
      "code": "829102"
    }
  }
}
```

---

## 3.2 Field Definitions

| Field       | Description                   |
| ----------- | ----------------------------- |
| version     | Messaging schema version.     |
| serviceType | `[serviceName, action]` pair. |
| sender      | Registered sender id.         |
| destination | Target service id.            |
| message     | Logical action name.          |
| payment     | Payment proof object.         |
| payload     | Service-specific data.        |



