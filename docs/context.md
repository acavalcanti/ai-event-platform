# AI Event Platform - Context

This system is a multi-agent AI platform for orchestrating corporate events.

Core concepts:
- Event Blueprint (modular stages)
- Multi-agent orchestration (LangGraph-based)
- Interaction Engine (real-time + approval)
- Template & Reuse Engine
- Multi-event support (event_id isolation)
- AI Copilot (queries + insights)

Architecture principles:
- Deterministic orchestration
- Human-in-the-loop
- LLM abstraction (cloud + local)
- Event-driven interactions (lightweight)
- Strong auditability (decision log)

Main components:
- Event Service
- Template Service
- Interaction Service
- Governance Service
- AI Orchestration Layer

Decisions:
- Use LangGraph for orchestration
- Do not use emergent multi-agent systems
- Use modular event stages
- Use LLM abstraction layer
