# Event API

## Overview

The Event API manages the lifecycle of corporate events using modular Event Blueprints and deterministic orchestration workflows.

Each event is composed of configurable stages that can be enabled or disabled depending on the event type and operational requirements.

The API is designed to support:
- modular event orchestration
- human approval workflows
- deterministic execution
- lifecycle management
- auditability

---

## Core Concepts

### Event Blueprint

Defines the structure and enabled stages of an event.

Example stages:
- registration
- welcome coffee
- keynote
- customer presentations
- networking
- round table

---

### Event Lifecycle

Events progress through orchestrated lifecycle states managed by the LangGraph Orchestrator.

Supported lifecycle states:
- draft
- scheduled
- active
- paused
- completed
- cancelled

---

## Endpoints

### Create Event

Creates a new event using an Event Blueprint.

#### Request

```http
POST /events
```

#### Request Example

```json
{

  "event_name": "AI Innovation Summit 2026",

  "event_date": "2026-10-15",

  "enabled_stages": [

    "registration",

    "welcome_coffee",

    "customer_cases",

    "round_table",

    "networking"

  ]

}
```

#### Response Example

```json
{

  "event_id": "evt-123",

  "status": "draft"

}
```

---

### Retrieve Event

Retrieves event metadata, enabled stages, orchestration status, and lifecycle information.

#### Request

```http
GET /events/{event_id}
```

---

### Update Event Configuration

Updates event metadata and enabled stages.

#### Request

```http
PUT /events/{event_id}
```

---

### Start Event Workflow

Starts orchestration execution for the event.

This operation initializes:
- workflow state
- orchestration checkpoints
- shared orchestration state initialization
- agent coordination

#### Request

```http
POST /events/{event_id}/start
```

---

### Pause Event Workflow

Temporarily pauses orchestration execution.

#### Request

```http
POST /events/{event_id}/pause
```

---

### Resume Event Workflow

Resumes a paused orchestration workflow.

#### Request

```http
POST /events/{event_id}/resume
```

---

### Approve Event Stage

Approves an event stage requiring human validation.

Examples:
- publishing external content
- starting executive presentations
- enabling audience interactions

#### Request

```http
POST /events/{event_id}/stages/{stage_id}/approve
```

---

### Complete Event Stage

Marks an event stage as completed and advances orchestration state.

#### Request

```http
POST /events/{event_id}/stages/{stage_id}/complete
```

---

### Cancel Event

Cancels the event workflow.

#### Request

```http
POST /events/{event_id}/cancel
```

---

## State Ownership

The AI Event Platform separates orchestration state from domain state ownership.

### Orchestration State

Owned by the LangGraph Orchestrator.

Examples:
- workflow execution state
- stage transitions
- orchestration checkpoints
- agent coordination state

---

### Domain State

Owned by application services.

Examples:
- Event Service owns lifecycle state
- Interaction Service owns interaction state
- Governance Service owns audit and approval state

---

## Auditability

All event lifecycle operations are recorded in the Decision Log to support:
- auditability
- governance tracking
- operational traceability
- replay support
