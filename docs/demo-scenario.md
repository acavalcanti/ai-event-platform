# Demo Scenario

## Overview

This demo showcases how the AI Event Platform can orchestrate a corporate AI event with minimal human intervention.

The system itself becomes part of the event demonstration.

---

## Event Context

- 200 participants
- customer presentations
- executive round table
- networking sessions
- real-time audience engagement

---

## Demo Flow

### 1. Event Creation

An operator creates the event using an Event Blueprint.

Enabled stages:
- registration
- welcome coffee
- customer cases
- round table
- networking

The LangGraph Orchestrator initializes the workflow.

---

### 2. Stakeholder Coordination

The system coordinates:
- vendors
- internal teams
- customer presenters
- executive stakeholders

Tasks and approvals are tracked through orchestration workflows.

---

### 3. Audience Interaction

Participants submit photos and questions through WhatsApp.

The Interaction Service:
- receives webhook events
- validates payloads
- starts moderation workflows

---

### 4. Human Approval

A human operator reviews submitted interactions.

Approved interactions are:
- published to Instagram
- displayed on event dashboards
- stored in the audit log

---

### 5. AI Copilot Usage

Operators query the AI Copilot for:
- workflow status
- pending approvals
- interaction summaries
- event operational insights

---

## Demo Goals

Demonstrate:
- deterministic AI orchestration
- modular event workflows
- real-time interaction handling
- governance and auditability
- scalable multi-event architecture