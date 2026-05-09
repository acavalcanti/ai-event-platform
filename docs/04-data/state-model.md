# State Model

## Overview

The AI Event Platform separates orchestration state from domain state to ensure deterministic execution, replay safety, and clear ownership boundaries.

The platform uses:
- orchestration state for workflow coordination
- domain state for business lifecycle persistence
- deterministic validation for workflow transitions

This separation prevents stale checkpoint execution and reduces distributed state inconsistencies.

---

# Orchestration State

The orchestration state is owned by the LangGraph Orchestrator.

The orchestration layer stores only workflow coordination metadata required for deterministic execution and replay safety.

Business lifecycle state is not persisted in LangGraph checkpoints.

---

## EventWorkflowState

```python
from typing import TypedDict, Optional


class EventWorkflowState(TypedDict):
    workflow_execution_id: str
    event_id: str
    orchestration_version: int
    current_node: str
    proposal_id: Optional[str]
    interruption_id: Optional[str]
    suspended: bool
```

---

## Orchestration Responsibilities

The orchestration layer is responsible for:
- workflow coordination
- checkpoint persistence
- replay recovery
- interruption management
- workflow routing
- orchestration version validation

The orchestration layer does not own:
- interaction lifecycle state
- approval records
- event business state
- external execution state

---

# Domain State Ownership

Application services remain the authoritative source of truth for business state.

Examples:
- Event Service owns event lifecycle state
- Interaction Service owns interaction lifecycle state
- Governance Service owns approval and audit records

LangGraph checkpoints do not act as the source of truth for business state transitions.

Workflow nodes must rehydrate domain state before execution.

---

# Replay Safety

The platform separates:
- orchestration checkpoints
- domain persistence

This design prevents stale workflow execution after orchestration resume or replay.

Before execution resumes:
1. orchestration state is restored
2. domain state is rehydrated
3. orchestration version is validated
4. policy validation is executed
5. workflow execution continues

---

# Optimistic Concurrency Control (OCC)

The platform uses optimistic concurrency control to prevent stale workflow execution.

Workflow transitions are rejected when:
- orchestration state is outdated
- approval state has changed
- interaction lifecycle advanced
- concurrent modifications exist

This validation prevents replay inconsistencies and stale HITL approvals.

---

# Human-in-the-Loop (HITL)

Certain workflow transitions require explicit human approval.

Examples:
- public interaction publishing
- executive workflow transitions
- external communications
- escalation approvals

When HITL interruption occurs:
1. orchestration state is checkpointed
2. workflow execution pauses
3. domain state remains persisted
4. approval is requested
5. orchestration resumes only after deterministic validation

---

# Interaction Lifecycle State Machine

Interactions follow a deterministic lifecycle to ensure orchestration consistency and replay safety.

## Lifecycle States

| State | Description |
|---|---|
| RECEIVED | Interaction accepted by ingestion layer |
| VALIDATING | Schema and policy validation in progress |
| PROPOSAL_GENERATED | AI reasoning proposal generated |
| PENDING_APPROVAL | Awaiting HITL approval |
| APPROVED | Proposal approved |
| REJECTED | Proposal rejected |
| EXECUTING | External execution in progress |
| COMPLETED | Execution completed successfully |
| FAILED | Execution failed |
| PENDING_VERIFICATION | External execution result uncertain |

---

## Lifecycle Rules

The interaction lifecycle is owned by the Interaction Service.

Workflow orchestration may coordinate transitions, but domain services remain authoritative for lifecycle persistence.

Invalid transitions are rejected through:
- orchestration version validation
- policy enforcement
- concurrency validation
- replay safety checks

---

# Shared Decision Log

The platform maintains a shared Decision Log for:
- governance traceability
- replay support
- workflow auditability
- orchestration visibility
- approval tracking

The Decision Log schema is defined in:

`docs/07-governance/audit.md`