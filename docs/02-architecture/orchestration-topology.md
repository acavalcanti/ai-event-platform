# Orchestration Topology

## Overview

The AI Event Platform uses LangGraph to orchestrate deterministic workflow execution with bounded AI reasoning.

The orchestration topology defines:
- workflow nodes
- execution edges
- interruption boundaries
- replay semantics
- suspend/resume behavior

The orchestration layer coordinates workflow progression but does not own business state.

Business state remains authoritative in PostgreSQL domain services.

---

# Architectural Principles

The orchestration model prioritizes:
- deterministic execution
- replay safety
- resumability
- orchestration isolation
- bounded autonomy

LLMs generate proposals but never execute external side effects directly.

---

# Workflow Execution Model

The orchestration runtime follows a proposal-driven execution flow:

```text
Interaction Ingestion
    ↓
Proposal Generation
    ↓
Policy Validation
    ↓
Human Approval
    ↓
Async Execution
    ↓
Workflow Resume
    ↓
Workflow Completion
```

---

# Workflow Nodes

## IngestInteractionNode

Initial workflow entrypoint.

Responsibilities:
- validate orchestration start request
- initialize workflow execution
- load interaction reference
- initialize orchestration metadata

This node does not mutate business state.

---

## LoadInteractionStateNode

Loads the latest interaction state from PostgreSQL.

Responsibilities:
- hydrate latest business state
- validate orchestration version
- validate replay safety
- verify OCC constraints

This prevents stale checkpoint execution.

---

## GenerateProposalNode

Generates operational proposals using LLM reasoning.

Examples:
- publish attendee image
- escalate interaction
- suggest attendee response

Responsibilities:
- invoke reasoning layer
- generate proposal payload
- produce reasoning summary

This node never executes external actions directly.

---

## PersistProposalNode

Persists proposal state before orchestration continuation.

Responsibilities:
- persist proposal metadata
- persist reasoning summary
- persist orchestration references
- update interaction state

The orchestration checkpoints immediately after proposal persistence.

This guarantees replay-safe proposal recovery.

---

## PolicyValidationNode

Routes proposals through deterministic governance validation.

Responsibilities:
- validate policy rules
- validate authorization
- validate orchestration state
- validate approval requirements

Possible outcomes:
- APPROVED
- REJECTED
- HITL_REQUIRED

---

## AwaitApprovalNode

Suspends orchestration awaiting human approval.

Responsibilities:
- persist interruption metadata
- suspend orchestration execution
- expose approval context

This node defines the primary HITL interruption boundary.

---

## CorrectionNode

Handles rejected proposals safely.

Responsibilities:
- persist rejection reason
- route to correction flow
- request revised proposal generation
- terminate invalid execution safely

This prevents orchestration corruption after governance rejection.

---

## PublishIntentNode

Creates asynchronous execution intent.

Responsibilities:
- generate execution intent
- route execution request
- prepare outbox payload

This node does not execute side effects directly.

---

## SuspendForExternalExecutionNode

Suspends orchestration while external execution occurs asynchronously.

Responsibilities:
- persist orchestration checkpoint
- persist suspend metadata
- wait for external completion callback

This prevents orchestration thread blocking.

---

## ResumeWorkflowNode

Resumes workflow execution after asynchronous completion.

Responsibilities:
- reload latest business state
- validate orchestration version
- validate replay consistency
- continue orchestration safely

---

## CompleteWorkflowNode

Finalizes orchestration execution.

Responsibilities:
- persist workflow completion metadata
- close orchestration execution
- finalize audit trail

---

# Workflow Topology

## Main Flow

```text
IngestInteractionNode
    ↓
LoadInteractionStateNode
    ↓
GenerateProposalNode
    ↓
PersistProposalNode
    ↓
PolicyValidationNode
```

---

## HITL Flow

```text
PolicyValidationNode
    ↓
AwaitApprovalNode
    ↓
PublishIntentNode
```

---

## Rejection Flow

```text
PolicyValidationNode
    ↓
CorrectionNode
```

---

## Async Execution Flow

```text
PublishIntentNode
    ↓
SuspendForExternalExecutionNode
    ↓
ResumeWorkflowNode
    ↓
CompleteWorkflowNode
```

---

# Interruption Boundaries

## HITL Interruption

The primary interruption point occurs before:

```text
AwaitApprovalNode
```

The workflow remains suspended until:
- approval granted
- approval rejected
- approval expired

---

## Async Execution Interruption

The orchestration suspends before external execution.

This prevents:
- blocking orchestration threads
- synchronous side-effect coupling
- orchestration instability

---

# Resume Semantics

Workflow resume uses:

```text
thread_id = workflow_execution_id
```

Resume execution always:
1. reloads latest domain state
2. validates orchestration version
3. validates replay consistency
4. resumes deterministic execution

---

# Replay Safety

Replay safety is enforced through:
- proposal persistence
- immediate checkpointing
- OCC validation
- deterministic policy enforcement

LLM proposal nodes checkpoint immediately after proposal persistence.

This prevents:
- duplicated reasoning
- proposal divergence
- replay corruption

---

# Failure Handling

Failures route through deterministic recovery paths.

Examples:
- proposal rejection
- orchestration divergence
- stale approvals
- replay conflicts
- external execution failures

Ambiguous execution states transition to:

```text
PENDING_VERIFICATION
```

before workflow continuation.

---

# Design Principles

The orchestration topology prioritizes:
- deterministic orchestration
- replay-safe execution
- governance visibility
- bounded autonomy
- orchestration resilience
- distributed systems correctness