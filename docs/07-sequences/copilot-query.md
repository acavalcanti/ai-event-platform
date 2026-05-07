# Copilot Query Sequence Diagram

```mermaid
sequenceDiagram
    participant User
    participant AIOrchestrationLayer
    participant GovernanceService
    participant LLMAbstractionLayer

    User->>AIOrchestrationLayer: Submit Query
    AIOrchestrationLayer->>GovernanceService: Validate Query
    GovernanceService-->>AIOrchestrationLayer: Approval
    AIOrchestrationLayer->>LLMAbstractionLayer: Process Query
    LLMAbstractionLayer->>AIOrchestrationLayer: Generate Response
    AIOrchestrationLayer->>User: Provide Insights
```