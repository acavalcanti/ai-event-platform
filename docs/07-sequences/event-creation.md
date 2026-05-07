# Event Creation Sequence Diagram

```mermaid
sequenceDiagram
    participant User
    participant EventService
    participant TemplateService
    participant GovernanceService

    User->>EventService: Create Event Blueprint
    EventService->>TemplateService: Fetch Templates
    TemplateService-->>EventService: Return Templates
    EventService->>GovernanceService: Validate Event Blueprint
    GovernanceService-->>EventService: Approval
    EventService->>User: Confirm Event Created
```