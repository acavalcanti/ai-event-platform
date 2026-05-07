# ADR 001: Use LangGraph for Orchestration

## Status

[Insert current status here, e.g., "In Development", "Accepted", etc.]

## Context

The decision to use LangGraph for orchestration is based on the following considerations:

- **Deterministic Orchestration**: Ensuring that every event runs smoothly without unexpected deviations.
- **Human-in-the-Loop**: Maintaining control and intervention where necessary for optimal outcomes.
- **LLM Abstraction (Cloud + Local)**: Leveraging both cloud-based and local AI models to handle complex tasks efficiently.
- **Event-driven Interactions**: Facilitating seamless, real-time interactions with strong auditability for compliance.
- **Strong Auditability (Decision Log)**: Providing a robust log of decisions for compliance and traceability.

## Decision

We have decided to use LangGraph for orchestration due to its ability to meet the above requirements effectively.

## Consequences

### Pros
1. **Deterministic Orchestration**: Ensures consistent event execution.
2. **Human-in-the-Loop**: Allows for intervention when necessary.
3. **LLM Abstraction (Cloud + Local)**: Flexibility in using both cloud and local AI models.
4. **Event-driven Interactions**: Real-time and approval-based interactions with strong auditability.
5. **Strong Auditability (Decision Log)**: Comprehensive logging for compliance.

### Cons
1. **Complexity**: LangGraph may introduce additional complexity in system design and maintenance.
2. **Learning Curve**: Developers may need time to learn and adapt to using LangGraph.
3. **Cost**: There might be associated costs with maintaining a robust LangGraph infrastructure.

## Trade-offs

The decision to use LangGraph comes with the trade-off of increased complexity and potential learning curve, but it offers significant benefits in terms of deterministic orchestration, human intervention, LLM abstraction, event-driven interactions, and strong auditability. The pros outweigh the cons given the platform's requirements and goals.
