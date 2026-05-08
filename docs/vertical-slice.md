# Vertical Slice

## Overview

The initial implementation focuses on a single end-to-end orchestration flow demonstrating the core architecture principles of the AI Event Platform.

The objective is to validate:
- orchestration semantics
- deterministic governance
- asynchronous execution
- HITL workflows
- replay safety
- workflow resumability

This slice acts as the architectural reference implementation for future platform expansion.

---

# Vertical Slice Scenario

## Scenario

An attendee submits an interaction through an external channel.

The platform:
1. ingests the interaction asynchronously
2. generates an AI proposal
3. validates the proposal deterministically
4. pauses for human approval
5. executes the external side effect asynchronously
6. resumes orchestration after completion

---

# End-to-End Flow

```text
WhatsApp Interaction
    ↓
Asynchronous Interaction Ingestion
    ↓
Proposal Generation
    ↓
Deterministic Policy Validation
    ↓
Human Approval (HITL)
    ↓
Transactional Outbox Persistence
    ↓
Asynchronous Instagram Publish
    ↓
External Callback
    ↓
Workflow Resume
    ↓
Workflow Completion
```

---

# Architecture Components

## Included Components

The vertical slice includes:

- Interaction API
- PostgreSQL domain state
- Transactional Outbox
- Redis Streams
- LangGraph orchestration
- Policy Engine
- HITL approval flow
- deterministic workers
- external integration simulation

---

# Demonstrated Capabilities

The slice demonstrates:

## Orchestration

- workflow execution
- workflow suspension
- workflow resume
- checkpoint persistence

---

## Governance

- deterministic validation
- approval enforcement
- proposal validation
- orchestration safety

---

## Distributed Systems

- asynchronous execution
- reliable queueing
- replay safety
- idempotency
- failure isolation

---

## AI Integration

- proposal generation
- bounded autonomy
- reasoning vs execution separation

---

# Success Criteria

The vertical slice is considered successful if it demonstrates:

- end-to-end orchestration
- successful suspend/resume execution
- deterministic approval enforcement
- replay-safe workflow execution
- asynchronous external execution
- orchestration recovery after interruption

---

# Out of Scope

The following capabilities are intentionally deferred:

- full ABAC implementation
- Open Policy Agent integration
- Kafka-based event mesh
- CQRS/event sourcing
- multi-region orchestration
- advanced analytics
- recommendation systems
- advanced personalization
- production-scale multi-tenancy

---

# Design Principles

The vertical slice prioritizes:
- architectural correctness
- orchestration safety
- deterministic execution
- implementation focus
- incremental validation