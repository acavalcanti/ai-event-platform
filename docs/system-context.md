# System Context

## Overview

The AI Event Platform is an enterprise orchestration platform designed to coordinate corporate event workflows using deterministic orchestration and bounded AI reasoning.

The platform combines:
- workflow orchestration
- deterministic governance
- asynchronous integrations
- human-in-the-loop approvals
- AI-assisted operational reasoning

---

# Architectural Philosophy

The platform follows a system-first architecture.

AI components assist operational workflows through:
- recommendations
- proposal generation
- summarization
- interaction classification

Business-critical execution remains deterministic.

---

# Core Principles

## Deterministic Governance

Governance, authorization, and workflow execution are enforced by deterministic services.

LLMs never enforce governance or authorization rules.

---

## Bounded Autonomy

Agents generate proposals but never execute external side effects directly.

All proposals are validated before execution.

---

## Distributed Workflow Orchestration

The platform uses LangGraph for:
- orchestration execution
- checkpoint persistence
- workflow suspend/resume
- HITL interruptions

Business state remains owned by PostgreSQL domain services.

---

## Asynchronous Execution

External integrations execute asynchronously through:
- Transactional Outbox
- Redis Streams
- deterministic workers

This isolates orchestration from external system failures.

---

# Primary Use Cases

The platform is designed to support:

- enterprise event coordination
- attendee interaction management
- operational approvals
- external communication orchestration
- AI-assisted event operations

---

# Current Architecture Scope

The current architecture phase focuses on:
- orchestration correctness
- governance boundaries
- distributed systems safety
- replay-safe execution
- vertical slice validation

The current objective is not full production hardening.

---

# Implementation Strategy

The platform will evolve incrementally.

The first implementation milestone is the Vertical Slice documented in:

```text
/docs/vertical-slice.md
```

This slice validates the core orchestration model before broader platform expansion.