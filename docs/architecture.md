
# Architecture

## Components

1. API Layer
    - Accepts HTTP requests
    - Validates requests
    - Handles authentication

2. Agent Layer
    - Builds model prompts
    - Coordinates model interactions
    - Handles tool calls and results

3. Tool Layer
    - OrderQueryTool
    - SalesAnalyticsTool
    - KnowledgeSearchTool

4. Business Layer
    - OrderService
    - SalesAnalyticsService
    - KnowledgeService

5. Data Layer
    - MySQL for business data
    - Vector store for document retrieval
    - Redis for selected caching use cases

## Request Flow

User Request
-> REST API
-> Agent Service
-> LLM
-> Authorized Java Tool
-> Business Service
-> Data Source
-> Tool Result
-> LLM Final Response
-> User

## Design Principles

- Keep model reasoning separate from business execution.
- Use explicit and validated tool interfaces.
- Keep API, business, and persistence layers independent.
- Make external dependencies configurable.
- Test critical business logic independently.
