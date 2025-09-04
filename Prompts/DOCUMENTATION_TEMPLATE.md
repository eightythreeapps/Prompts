You just created a new feature. Now document it for developers and stakeholders.  
Your output must be in **Markdown** and include the following sections:

# Feature Documentation

## Overview
- Provide a concise summary of what this feature does.  
- Explain the user value and business context in plain language.  

## Technical Details
- Describe the main components (APIs, services, modules, database schema, etc.).  
- List any key dependencies or external services.  
- Include configuration or environment variables if relevant.  

## Architecture Diagram
Generate a **Mermaid diagram** to illustrate the architecture.  
Use the appropriate diagram type (e.g., `flowchart`, `sequenceDiagram`, or `erDiagram`) depending on what best explains the feature.  
Make sure it shows:
- User interactions  
- Key system components  
- Data flow / request flow  

Example:
```mermaid
flowchart LR
    User -->|Request| Frontend
    Frontend --> API
    API --> Database
    API --> ExternalService
