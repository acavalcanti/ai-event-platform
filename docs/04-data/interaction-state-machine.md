# Interaction State Machine

## Overview

The AI Event Platform uses a deterministic interaction state machine to coordinate:
- orchestration routing
- governance validation
- replay safety
- approval semantics
- asynchronous execution

The state machine acts as the authoritative lifecycle model for attendee interactions.

---

# Architectural Principles

The interaction lifecycle must be:
- deterministic
- replay-safe
- observable
- versioned
- governance-aware

State transitions are validated by deterministic services.

LLMs never mutate interaction state directly.

---

# State Lifecycle

## RECEIVED

The interaction has been ingested successfully.

Examples:
- WhatsApp message received
- attendee image uploaded
- external webhook accepted

At this stage:
- payload persistence completed
- idempotency validation completed
- orchestration not yet started

---

## PROPOSAL_GENERATED

An orchestration node generated a proposal for the interaction.

Examples:
- publish attendee image
- escalate interaction
- send automated response

The proposal is persisted before workflow continuation.

---

## PENDING_APPROVAL

The proposal requires human approval before execution.

Examples:
- public social media publishing
- executive-facing communications
- sensitive attendee interactions

The orchestration enters a suspended state.

---

## APPROVED

The proposal has been approved by an authorized approver.

At this stage:
- approval persistence completed
- policy validation completed
- execution authorization granted

---

## REJECTED

The proposal has been rejected.

The workflow may:
- terminate
- route to correction flows
- request new proposal generation

Rejections remain persisted for auditability.

---

## PUBLISH_PENDING

The proposal was accepted for asynchronous external execution.

Examples:
- Instagram publishing
- WhatsApp notification dispatch
- CRM synchronization

Execution responsibility transfers to deterministic workers.

---

## PUBLISHED

The external execution completed successfully.

Examples:
- content published
- notification delivered
- webhook acknowledged

The workflow may proceed to completion.

---

## FAILED

The interaction execution failed permanently.

Examples:
- unrecoverable validation error
- exhausted retry attempts
- policy violations
- invalid orchestration transitions

The workflow terminates safely.

---

## PENDING_VERIFICATION

The orchestration entered an ambiguous execution state.

Examples:
- LLM timeout
- external provider timeout
- uncertain completion state
- interrupted reasoning execution

This state prevents:
- duplicated reasoning
- duplicated side effects
- replay corruption

The workflow requires deterministic verification before continuation.

---

# Valid Transitions

```text
RECEIVED
    ↓
PROPOSAL_GENERATED
    ↓
PENDING_APPROVAL
    ↓
APPROVED
    ↓
PUBLISH_PENDING
    ↓
PUBLISHED
```

---

# Rejection Paths

```text
PENDING_APPROVAL
    ↓
REJECTED
```

```text
PROPOSAL_GENERATED
    ↓
PENDING_VERIFICATION
```

```text
PENDING_VERIFICATION
    ↓
PROPOSAL_GENERATED
```

```text
PENDING_VERIFICATION
    ↓
FAILED
```

---

# Invalid Transitions

The following transitions are invalid:

```text
REJECTED → PUBLISHED
FAILED → APPROVED
PUBLISHED → PENDING_APPROVAL
```

Invalid transitions are rejected by:
- Policy Engine
- orchestration validation
- OCC version checks

---

# Optimistic Concurrency Control

Each interaction maintains:
- version number
- orchestration revision
- transition metadata

Transitions are rejected if:
- stale versions are detected
- orchestration diverges
- replay safety is violated

---

# Replay Safety

The interaction state machine ensures:
- deterministic replay
- orchestration consistency
- resumability safety
- side-effect isolation

Proposal generation nodes checkpoint immediately after proposal persistence.

---

# Governance Enforcement

State transitions requiring approval must pass:
- policy validation
- authorization validation
- orchestration validation

Examples:
- APPROVED transitions
- PUBLISH_PENDING transitions

---

# Design Principles

The interaction state machine prioritizes:
- deterministic execution
- replay safety
- orchestration clarity
- governance visibility
- distributed systems correctness