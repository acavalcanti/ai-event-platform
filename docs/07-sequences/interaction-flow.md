# Interaction Flow

## Overview

This sequence describes the end-to-end asynchronous interaction workflow for the initial vertical slice.

The workflow demonstrates:
- asynchronous ingestion
- deterministic governance
- HITL approvals
- orchestration suspend/resume
- outbox execution
- external integration isolation

---

```mermaid
sequenceDiagram

participant ExternalSystem
participant InteractionAPI
participant DomainDB
participant Outbox
participant Worker
participant LangGraph
participant PolicyEngine
participant HumanApprover
participant InstagramAPI

ExternalSystem->>InteractionAPI: POST /interactions
InteractionAPI->>DomainDB: Persist interaction
InteractionAPI->>Outbox: Persist ingestion event
InteractionAPI-->>ExternalSystem: HTTP 202 Accepted

Worker->>Outbox: Consume ingestion event
Worker->>LangGraph: Start workflow execution

LangGraph->>DomainDB: Load current interaction state
LangGraph->>LangGraph: Generate proposal

LangGraph->>DomainDB: Persist proposal
LangGraph->>LangGraph: Checkpoint proposal state

LangGraph->>PolicyEngine: Validate proposal

PolicyEngine-->>LangGraph: HITL approval required

LangGraph->>DomainDB: Persist interruption state
LangGraph->>LangGraph: Suspend workflow

HumanApprover->>PolicyEngine: Approve proposal

PolicyEngine->>DomainDB: Persist approval
PolicyEngine->>Outbox: Persist publish intent

Worker->>Outbox: Consume publish intent
Worker->>InstagramAPI: Publish content

InstagramAPI-->>Worker: Publish confirmation

Worker->>Outbox: Persist completion event

Worker->>LangGraph: Resume workflow

LangGraph->>DomainDB: Reload current state
LangGraph->>LangGraph: Validate orchestration version
LangGraph->>LangGraph: Complete workflow
```