# Interaction Flow

## Overview

This sequence describes the end-to-end orchestration flow for the initial vertical slice.

The workflow demonstrates:
- asynchronous ingestion
- deterministic governance
- HITL approvals
- orchestration suspend/resume
- proposal persistence
- replay-safe execution
- asynchronous side-effect execution

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

LangGraph->>DomainDB: Load latest interaction state
LangGraph->>LangGraph: Generate proposal

LangGraph->>DomainDB: Persist proposal
LangGraph->>LangGraph: Checkpoint proposal state

LangGraph->>PolicyEngine: Validate proposal

alt Approval Required

    PolicyEngine-->>LangGraph: HITL approval required

    LangGraph->>DomainDB: Persist interruption metadata
    LangGraph->>LangGraph: Suspend workflow

    HumanApprover->>PolicyEngine: Approve proposal

    PolicyEngine->>DomainDB: Persist approval
    PolicyEngine->>Outbox: Persist publish intent

    Worker->>Outbox: Consume publish intent
    Worker->>InstagramAPI: Publish content

    InstagramAPI-->>Worker: Publish confirmation

    alt Publish Failed
        Worker->>DomainDB: Persist FAILED state
        Worker->>LangGraph: Resume failure flow
        LangGraph->>LangGraph: Route to CorrectionNode

    else Publish Succeeded
        Worker->>Outbox: Persist completion event
    end

    Worker->>LangGraph: Resume workflow

    LangGraph->>DomainDB: Reload latest interaction state
    LangGraph->>LangGraph: Validate orchestration version
    LangGraph->>LangGraph: Complete workflow

else Proposal Rejected

    PolicyEngine-->>LangGraph: Proposal rejected

    LangGraph->>DomainDB: Persist rejection reason
    LangGraph->>LangGraph: Route to correction flow

    LangGraph->>LangGraph: Generate revised proposal

    LangGraph->>DomainDB: Persist revised proposal
    LangGraph->>LangGraph: Checkpoint revised proposal

end
```