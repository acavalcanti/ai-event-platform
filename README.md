# AI Event Platform

A multi-agent AI platform for orchestrating corporate events with deterministic workflows, real-time interactions, and human oversight.

Corporate events involve complex coordination between internal teams, vendors, stakeholders, and participants.

This project explores how multi-agent AI systems can orchestrate these workflows through modular execution, real-time interaction pipelines, and governance mechanisms.

## Key Features

- **Deterministic Orchestration**: Ensure every event runs smoothly without unexpected deviations.
- **Human-in-the-Loop**: Maintain control and intervention where necessary for optimal outcomes.
- **LLM Abstraction (Cloud + Local)**: Leverage the power of both cloud-based and local AI models to handle complex tasks efficiently.
- **Event-Driven Interactions**: Facilitate seamless, real-time interactions with strong auditability for compliance.

## Architecture Highlights

The platform is built on a layered architecture that ensures scalability, reliability, and ease of maintenance:

- **Event Service Layer**: Manages the creation and management of event blueprints.
- **Template Service Layer**: Handles reusable templates for events with multi-event support.
- **Interaction Service Layer**: Facilitates real-time and approval-based interactions during events.
- **Governance Service Layer**: Ensures compliance and auditability through governance mechanisms.
- **AI Orchestration Layer**: Orchestrates the multi-agent system using LangGraph for deterministic orchestration.

## Architectural Goals

The AI Event Platform is designed to explore how multi-agent systems can coordinate complex operational workflows involving:

- multiple stakeholders
- real-time interactions
- external integrations
- governance and approvals
- reusable event orchestration patterns

The platform prioritizes:
- deterministic execution
- modularity
- auditability
- scalability
- human oversight

# Current Implementation Focus

The current implementation milestone is the Vertical Slice architecture described in:

```text
/docs/vertical-slice.md
```

This slice validates:
- deterministic orchestration
- bounded AI reasoning
- asynchronous execution
- HITL workflows
- replay-safe execution
- governance enforcement

The project intentionally prioritizes architectural correctness and orchestration semantics before broader platform expansion.

## Status

Architecture and design phase.

Core architecture, orchestration model, governance, and interaction flows are currently being defined before implementation begins.