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

## Tech Stack

- Java 21
- Spring Boot
- Spring AI
- MySQL
- PostgreSQL with pgvector
- Redis
- Docker Compose

## Architecture

The application separates the API layer, agent orchestration,
business tools, and data access layer.

The LLM interprets user requests and selects available tools.
Java services execute authorized business operations and
return results to the model.

## Project Status

Early development — features are being implemented incrementally.

## Getting Started

Setup instructions will be added as the first runnable
version becomes available.

## Roadmap

- [ ] Initialize the Spring Boot application
- [ ] Integrate an LLM provider
- [ ] Implement business tool calling
- [ ] Add persistent conversation history
- [ ] Implement RAG
- [ ] Add MCP integration
- [ ] Add tests and Docker deployment

## License

To be determined.
