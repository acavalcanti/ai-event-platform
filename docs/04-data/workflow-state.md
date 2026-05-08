# Workflow State

## Overview

The AI Event Platform separates orchestration runtime state from business domain state to ensure deterministic execution, operational resilience, and governance visibility.

The architecture follows a system-first orchestration model where:
- business state remains authoritative
- orchestration state remains resumable
- external side effects are isolated
- workflow execution is deterministic

---

# State Ownership Model

## Domain State (Source of Truth)

Business domain state is the authoritative source of truth.

Domain state is owned by application services and persisted in the primary transactional database.

Recommended persistence:
- PostgreSQL

The domain state includes:
- event lifecycle state
- stage state
- participant registrations
- approvals
- interaction metadata
- vendor coordination state
- orchestration decisions
- audit records

---

## Orchestration State

The LangGraph Orchestrator maintains orchestration runtime state.

This state exists exclusively to support:
- workflow execution
- resumability
- orchestration coordination
- checkpoint persistence
- HITL interruptions

The orchestration state is NOT the source of truth for business operations.

Examples:
- current workflow node
- execution path
- retry metadata
- interruption metadata
- orchestration context
- graph traversal state

Recommended persistence:
- LangGraph PostgreSQL Checkpointer

---

# Shared Workflow State

## Overview

The LangGraph orchestration state remains intentionally minimal.

The orchestration layer stores only execution metadata required for:
- resumability
- orchestration continuity
- suspend/resume semantics
- replay coordination

Business state is never duplicated inside orchestration checkpoints.

Business state remains authoritative in PostgreSQL domain services.

---

# Example Workflow State

```python
class EventWorkflowState(TypedDict):
    workflow_execution_id: str
    event_id: str
    orchestration_version: int
    current_node: str
    suspended: bool
    interruption_id: str | None
    proposal_id: str | None
```

---

# Minimal Orchestration Philosophy

The orchestration runtime does not cache mutable business state.

The orchestration state contains only:
- execution references
- orchestration metadata
- replay coordination metadata

Examples of excluded business state:
- approval status
- interaction payloads
- participant state
- workflow decisions
- external execution status

All business state is reloaded from PostgreSQL before node execution.

---

# State Hydration

Each orchestration node reloads the latest business state directly from the Domain Database before execution.

Examples:
- interaction state
- approval state
- workflow transitions
- orchestration decisions
- external execution state

This prevents:
- stale orchestration state
- split-brain execution
- replay inconsistencies
- checkpoint corruption

---

# Proposal Persistence Boundary

Proposal generation introduces a deterministic replay boundary.

The workflow sequence is:

```text
Generate Proposal
    ↓
Persist Proposal
    ↓
Checkpoint Workflow State
    ↓
Continue Orchestration
```

This guarantees:
- proposal durability
- replay-safe execution
- deterministic recovery
- proposal consistency

LLM reasoning nodes never re-generate proposals after successful persistence.

---

# Ambiguous Completion Handling

Certain failures may produce ambiguous execution states.

Examples:
- LLM timeout
- interrupted provider response
- uncertain external execution state
- network interruption during reasoning

These cases transition the interaction into:

```text
PENDING_VERIFICATION
```

---

# Pending Verification Semantics

`PENDING_VERIFICATION` prevents unsafe replay behavior.

The workflow pauses until deterministic verification confirms:
- proposal persistence state
- execution completion state
- provider completion state
- replay safety guarantees

This prevents:
- duplicated reasoning
- duplicated execution
- inconsistent orchestration recovery

---

# Replay Coordination

Replay coordination uses:
- orchestration version validation
- proposal persistence checks
- deterministic transition validation
- OCC version checks

Replay execution is rejected if:
- orchestration diverges
- stale workflow state detected
- transition consistency violated

---

# Suspend/Resume Coordination

Workflow suspension stores:
- workflow_execution_id
- orchestration_version
- interruption metadata
- proposal references

Workflow resume always:
1. reloads latest business state
2. validates orchestration consistency
3. validates OCC version
4. validates replay safety
5. resumes deterministic execution

---

# Design Principles

The workflow state architecture prioritizes:
- deterministic orchestration
- replay-safe execution
- resumability
- orchestration isolation
- governance visibility
- distributed systems correctness

---

# Transactional Outbox Pattern

## Overview

The platform uses the Transactional Outbox Pattern to prevent dual-write inconsistencies between:
- domain state
- orchestration state
- external integrations

---

# Atomic Transaction Boundary

Business state updates and orchestration events must be persisted atomically.

Example transaction:

```text
BEGIN TRANSACTION

- update event state
- persist audit log
- persist orchestration event
- persist outbox event

COMMIT
```

This guarantees:
- consistent workflow progression
- replay safety
- failure recovery
- deterministic orchestration

---

# Orchestration Event Relay

The LangGraph Orchestrator is updated asynchronously through orchestration events.

The orchestration relay:
1. consumes orchestration events
2. hydrates orchestration state
3. resumes workflow execution
4. updates orchestration checkpoints

This prevents:
- synchronous orchestration coupling
- dual-write failures
- orchestration blocking

---

# Human-in-the-Loop Interruptions

## Interruption Model

Approval workflows use LangGraph interruption checkpoints.

Examples:
- interaction publishing approval
- executive workflow approval
- social media publishing approval
- workflow escalation approval

When interrupted:
- orchestration state is checkpointed
- workflow execution pauses
- domain state remains persisted
- interruption metadata is stored

---

# Workflow Resumption

When approval is received:
1. the Policy Engine validates approval freshness
2. orchestration version checks are performed
3. workflow state is hydrated
4. orchestration execution resumes

---

# Approval Freshness Validation

Approvals may become stale if:
- workflow state changes
- event stages advance
- orchestration versions diverge

The Policy Engine validates:
- orchestration version
- workflow consistency
- approval expiration
- replay safety

before resuming execution.

---

# Optimistic Concurrency Control

The platform uses Optimistic Concurrency Control (OCC) to prevent orchestration conflicts.

Domain objects include:
- version identifiers
- orchestration revision metadata

Updates are rejected if:
- workflow state changed during execution
- orchestration versions diverged
- stale orchestration resumed

This prevents:
- race conditions
- stale approvals
- concurrent workflow corruption

---

# Consistency Model

## Strong Consistency

Strong consistency is enforced inside transactional domain operations.

Examples:
- approval persistence
- audit logging
- lifecycle transitions
- orchestration event creation

---

## Eventual Consistency

Eventual consistency is used between:
- orchestration runtime state
- asynchronous workers
- external integrations
- notification systems

This architecture prioritizes:
- resilience
- replayability
- operational isolation

---

# Failure Recovery

## Crash Recovery

If orchestration execution crashes:
- orchestration state is restored from checkpoints
- domain state remains authoritative
- pending outbox events are replayed
- deterministic workers resume execution

---

# Replay Safety

All orchestration operations must be replay-safe.

Replay safety is enforced through:
- idempotency keys
- orchestration version checks
- transactional outbox events
- deterministic execution workers

---

# Retry Strategy

Retries must be:
- bounded
- observable
- idempotent
- deterministic

The platform avoids direct retry execution from:
- LLM nodes
- orchestration reasoning flows

Retries are delegated to deterministic workers.

---

# Design Principles

The workflow state architecture prioritizes:
- deterministic orchestration
- resumability
- fault isolation
- governance visibility
- replay safety
- operational resilience
- distributed systems correctness