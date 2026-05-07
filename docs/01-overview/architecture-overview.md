# Architecture Overview

## Architecture Layers

The AI Event Platform is organized into layered domains that separate orchestration, interaction, governance, and reusable event management capabilities.

### Client Layer
Interfaces used by operators, stakeholders, and participants.

Components:
- Dashboard UI
- Approval UI
- AI Copilot Interface

---

### Application Services Layer

Core business services responsible for event lifecycle management.

#### Event Service
Manages event blueprints, stages, and execution lifecycle.

#### Template Service
Handles reusable event templates, cloning, and multi-event support.

#### Interaction Service
Processes real-time interactions, approval workflows, and external communication pipelines.

#### Governance Service
Provides auditability, RBAC, compliance controls, and policy enforcement.

---

### AI Orchestration Layer

Coordinates deterministic multi-agent workflows using LangGraph orchestration.

Components:
- LangGraph Orchestrator
- Agent Coordination
- Shared Workflow State

---

### Shared State & Decision Layer

Stores orchestration state, interaction state, and decision logs used by agents and workflows.

Components:
- Event State
- Interaction State
- Decision Log
- Audit Records

---

### Integration Layer

Handles communication with external platforms and systems.

Components:
- WhatsApp Integration
- Instagram Integration
- Internal Portal Integration
- Webhook Adapters

## Components

The platform is built around several key components that work together to achieve its goals.

- **Event Service**: Manages event blueprints and templates.
- **Template Service**: Handles reusable templates and multi-event support.
- **Interaction Service**: Facilitates real-time and approval-based interactions.
- **Governance Service** ensures compliance, auditability, RBAC enforcement, and policy validation across orchestration workflows.
- **AI Orchestration Layer**: Orchestrates the multi-agent system using LangGraph.

## System Flow

The AI Event Platform follows a structured flow to ensure seamless event orchestration:

1. **Event Creation**:
   - The **Event Service** creates an event blueprint, which includes modular stages.
   - The **Template Service** manages reusable templates and ensures multi-event support.

2. **Real-time Interactions**:
   - The **Interaction Service** handles real-time interactions using the **Interaction Engine**, which supports both real-time and approval-based processes.
   - The **Governance Service** ensures compliance, auditability, RBAC enforcement, and policy validation across orchestration workflows.

3. **Orchestration**:
   - The **AI Orchestration Layer** uses LangGraph for deterministic orchestration, coordinating the multi-agent system efficiently.

## Shared State & Decision Log

Stores event state, interactions, and audit records used by agents and orchestration flows.

---

## State Ownership Model

The AI Event Platform separates orchestration state from domain state to ensure deterministic execution and clear ownership boundaries.

### Orchestration State

Owned by the LangGraph Orchestrator.

Stores:
- workflow execution state
- stage transitions
- agent coordination state
- orchestration checkpoints

### Domain State

Owned by application services.

Examples:
- Event Service owns event lifecycle data
- Interaction Service owns interaction processing data
- Governance Service owns audit and approval records

### Shared Decision Log

Used across orchestration and services to provide:
- traceability
- auditability
- replay support
- governance visibility

---

## Mermaid Diagram

```mermaid
flowchart TD

    UI[Clients / Dashboard]
    API[API Gateway]

    subgraph APP[Application Services]
        ES[Event Service]
        TS[Template Service]
        IS[Interaction Service]
        GS[Governance Service]
    end

    subgraph AI[AI Orchestration Layer]
        ORCH[LangGraph Orchestrator]
        AGENTS[AI Agents]
    end

    subgraph STATE[Shared State & Decision Layer]
        STATESTORE[Event State]
        DECISIONLOG[Decision Log]
    end

    subgraph EXT[Integration Layer]
        WA[WhatsApp]
        IG[Instagram]
        PORTAL[Internal Portal]
    end

    UI --> API

    API --> ES
    API --> TS
    API --> IS
    API --> GS

    ES --> ORCH
    TS --> ORCH
    IS --> ORCH

    ORCH <--> AGENTS

    AGENTS <--> STATESTORE
    AGENTS --> DECISIONLOG

    IS --> WA
    IS --> IG
    IS --> PORTAL
```

## Status

Architecture and design phase.
Implementation starting soon.