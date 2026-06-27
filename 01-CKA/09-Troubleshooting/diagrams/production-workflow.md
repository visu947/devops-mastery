# Production Troubleshooting Workflow

```mermaid
flowchart TD
    Alert[Alert or User Report]
    Impact[Assess Impact]
    Scope[Identify Scope]
    Resource[Identify Resource]
    Evidence[Collect Evidence]
    Events[Review Events]
    Logs[Review Logs]
    Dependencies[Check Dependencies]
    RootCause[Root Cause]
    Fix[Apply Minimal Fix]
    Validate[Validate Service]
    Monitor[Monitor After Fix]
    Document[Document Incident]

    Alert --> Impact
    Impact --> Scope
    Scope --> Resource
    Resource --> Evidence
    Evidence --> Events
    Events --> Logs
    Logs --> Dependencies
    Dependencies --> RootCause
    RootCause --> Fix
    Fix --> Validate
    Validate --> Monitor
    Monitor --> Document
```

## Key Point

Production troubleshooting should include impact assessment, evidence collection, minimal safe fixes, validation, monitoring, and documentation.
