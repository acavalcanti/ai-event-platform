# Workflow State

## Overview

The AI Event Platform separates orchestration state from business domain state to ensure deterministic execution, operational resilience, and auditability.

The orchestration layer is responsible for workflow coordination and resumability.

The domain layer remains the source of truth for business operations.

---

# State Ownership Model

## Domain State (Source of Truth)

The primary source of truth is maintained by application services and persisted in the domain database.

The domain database stores:
- event lifecycle state
- participant registrations
- approvals
- interactions
- audit records
- vendor coordination state
- operational metadata

Recommended persistence:
- PostgreSQL

---

## Orchestration State

The LangGraph Orchestrator maintains orchestration runtime state.

This state exists to support:
- workflow execution
- resumability
- checkpoints
- HITL interruptions
- orchestration coordination

The orchestration state is NOT the source of truth for business operations.

Examples:
- current workflow node
- graph execution path
- pending interruption state
- retry metadata
- orchestration context

Recommended persistence:
- LangGraph Postgres Checkpointer

---

# State Synchronization Strategy

## Principle

Business state changes must be persisted before orchestration checkpoints are considered committed.

The system avoids dual-write inconsistency by ensuring:

1. Domain state persistence
2. Audit log persistence
3. Orchestration checkpoint update

Only after successful persistence should workflow progression continue.

---

# Shared Workflow State

The workflow state object contains orchestration-specific execution metadata.

Example conceptual schema:

```python
class EventWorkflowState(TypedDict):
    event_id: str
    workflow_id: str
    current_stage: str
    current_node: str
    pending_approval: bool
    approval_owner: str | None
    interaction_queue: list
    execution_history: list
    retry_count: int
    orchestration_status: str
```

---

# Human-in-the-Loop Interruptions

Approval workflows use LangGraph interruption checkpoints.

Examples:
- interaction publishing approval
- executive approval
- stage transition approval

When interrupted:
- orchestration state is checkpointed
- workflow execution pauses
- domain state remains persisted

When resumed:
- orchestration state is hydrated
- workflow execution continues from checkpoint

---

# Failure Recovery

## Crash Recovery

If orchestration crashes:
- workflow state is restored from checkpoint
- domain state remains authoritative
- incomplete external actions are retried through deterministic workers

---

## Retry Strategy

Retries must be:
- idempotent
- deterministic
- externally observable

External integrations must never be retried directly from LLM reasoning nodes.

---

# Consistency Model

The platform uses eventual consistency between:
- orchestration runtime state
- asynchronous external integrations

The platform uses strong consistency for:
- approvals
- audit records
- event lifecycle transitions
- governance operations

---

# Design Principles

The workflow state model prioritizes:
- deterministic execution
- resumability
- operational traceability
- governance visibility
- replayability
- fault isolation