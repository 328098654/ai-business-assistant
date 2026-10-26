# AI Business Agent

A Java-based AI business assistant built with Spring Boot
and Spring AI.

## Features

- Natural language business queries
- LLM-powered tool calling
- Business data retrieval
- RAG-powered knowledge search
- REST API integration
- Docker-based deployment

## Tech Stack (provisional / 候选)

- Java (version to be confirmed)
- Spring Boot (version to be confirmed)
- Spring AI
- PostgreSQL with pgvector (relational data + vector search)
- Redis (optional, only when a concrete use case requires it)
- Vue 3 · TypeScript · ECharts (frontend, candidate)
- Docker Compose

## Architecture

The application separates the API layer, agent orchestration,
business tools, and data access layer.

The LLM interprets user requests and selects available tools.
Java services execute authorized business operations and
return results to the model.

Detailed architecture: see [`docs/system-architecture.md`](docs/system-architecture.md).
Product definition: see [`docs/product-charter.md`](docs/product-charter.md).

## Project Status

Early development — features are being implemented incrementally.

## Getting Started

Setup instructions will be added as the first runnable
version becomes available.

## Roadmap

Milestone-driven; see the docs for scope and principles.

- [ ] Finalize technology selection
- [ ] Choose and build the first end-to-end vertical slice
- [ ] Integrate an LLM provider
- [ ] Implement the controlled Agent Runtime (tool registry, validation, authorization)
- [ ] Implement RAG
- [ ] Add tests and Docker Compose deployment

## License

To be determined.
