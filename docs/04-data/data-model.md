# Data Model

## Event Blueprint

An **Event Blueprint** represents the modular structure of a corporate event. It consists of stages that define the sequence and dependencies between different services.

### Attributes
- **event_id**: Unique identifier for the event.
- **name**: Name of the event.
- **stages**: List of stages in the event blueprint, each with its own set of services and interactions.

## Stage

A **Stage** is a modular component within an Event Blueprint. It defines a specific phase or task in the event lifecycle.

### Attributes
- **stage_id**: Unique identifier for the stage.
- **name**: Name of the stage.
- **services**: List of services required to execute this stage.
- **interactions**: List of interactions that occur during this stage.

## Service

A **Service** is a component that performs a specific function within an event. It can be part of an Event Blueprint or a reusable template.

### Attributes
- **service_id**: Unique identifier for the service.
- **name**: Name of the service.
- **description**: Description of the service's functionality.
- **dependencies**: List of services this service depends on.

## Interaction

An **Interaction** describes the communication between participants during an event. It includes real-time messaging and approval processes.

### Attributes
- **interaction_id**: Unique identifier for the interaction.
- **name**: Name of the interaction.
- **type**: Type of interaction (e.g., real-time, approval).
- **participants**: List of participants involved in this interaction.

## Decision Log

The **Decision Log** records all decisions made during the event orchestration process. This includes policy enforcement, AI integration, and human-in-the-loop interactions.

### Attributes
- **log_id**: Unique identifier for the log entry.
- **timestamp**: Timestamp when the decision was made.
- **decision**: Description of the decision.
- **user**: User who made the decision.

## Template

A **Template** is a reusable blueprint for events. It defines the structure and content of an event, including stages, services, and interactions.

### Attributes
- **template_id**: Unique identifier for the template.
- **name**: Name of the template.
- **description**: Description of the template's purpose.
- **stages**: List of stages defined in this template.
