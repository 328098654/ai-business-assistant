# Technology Stack Decisions

# 技术栈选型决策

## 1. Purpose

This document records the technology choices, rationale, compatibility constraints, and validation requirements for AI Business Assistant. Its goal is to make engineering decisions explicit and reviewable rather than selecting technologies solely because they are popular or new.

本文记录 AI Business Assistant 的技术选型、选型理由、兼容性约束和验证要求。目标是让工程决策明确、可复核，而不是仅仅因为某项技术流行或版本较新就采用它。

## 2. Decision Principles

- Prefer a small, coherent, maintainable stack.
- Verify official compatibility information before pinning framework versions.
- Separate confirmed decisions from provisional candidates.
- Introduce infrastructure only when a concrete use case justifies its operational cost.
- Prefer replaceable adapters for external AI providers and infrastructure.
- Optimize for reproducibility, security, testability, observability, and maintainability.
- Record major changes and their rationale.

- 优先选择精简、一致、易维护的技术栈。
- 在锁定框架版本之前，先核对官方兼容性信息。
- 明确区分已确认的决策和暂定候选方案。
- 只有在具体业务场景能够证明其价值时，才引入额外基础设施。
- 对外部 AI 模型供应商和基础设施采用可替换的适配层。
- 优先保证环境可复现、安全性、可测试性、可观测性和可维护性。
- 记录重大变更及其原因。

## 3. Proposed Technology Stack

| Area | Proposed Choice | Status |
|---|---|---|
| Backend language | Java 21 LTS | Proposed baseline |
| Backend framework | Spring Boot 3.5.x | Provisional compatibility baseline; not a verified dependency lock |
| AI application framework | Spring AI 1.1.x | Provisional compatibility baseline; not a verified dependency lock |
| Build system | Maven multi-module | Confirmed direction |
| Primary database | PostgreSQL | Proposed baseline |
| Vector search | pgvector when the RAG milestone begins | Conditional |
| Frontend | Vue 3 + TypeScript + Vite | Proposed baseline |
| Backend tests | JUnit 5, Spring Boot Test | Proposed baseline |
| Integration tests | Testcontainers when useful | Conditional |
| Local environment | Docker Compose | Proposed baseline |
| Continuous integration | GitHub Actions | Proposed baseline |
| Observability | Structured logs and correlation IDs first; OpenTelemetry incrementally | Phased |
| Cache | Redis only when justified | Deferred |
| Message broker | Kafka or RabbitMQ only when justified | Deferred |
| Log search engine | Elasticsearch or OpenSearch when log-intelligence requirements justify it | Deferred |
| Container orchestration | Kubernetes only when deployment requirements justify it | Deferred |

| 领域 | 建议选择 | 状态 |
|---|---|---|
| 后端语言 | Java 21 LTS | 建议基线 |
| 后端框架 | Spring Boot 3.5.x | 暂定兼容基线；不是已验证的依赖锁定方案 |
| AI 应用框架 | Spring AI 1.1.x | 暂定兼容基线；不是已验证的依赖锁定方案 |
| 构建工具 | Maven 多模块 | 已确认方向 |
| 主数据库 | PostgreSQL | 建议基线 |
| 向量检索 | RAG 阶段启用 pgvector | 条件引入 |
| 前端 | Vue 3 + TypeScript + Vite | 建议基线 |
| 后端测试 | JUnit 5、Spring Boot Test | 建议基线 |
| 集成测试 | 在适用场景使用 Testcontainers | 条件引入 |
| 本地环境 | Docker Compose | 建议基线 |
| 持续集成 | GitHub Actions | 建议基线 |
| 可观测性 | 先使用结构化日志和请求关联 ID，再逐步接入 OpenTelemetry | 分阶段实施 |
| 缓存 | 有明确需求时再引入 Redis | 暂缓 |
| 消息中间件 | 有明确需求时再选择 Kafka 或 RabbitMQ | 暂缓 |
| 日志检索引擎 | 日志智能分析需求明确后，再评估 Elasticsearch 或 OpenSearch | 暂缓 |
| 容器编排 | 部署需求明确后再引入 Kubernetes | 暂缓 |

## 4. Java and Spring Compatibility

### English

**Provisional baseline.** The conservative baseline is Java 21 with Spring Boot 3.5.x and Spring AI 1.1.x. The Spring AI project documents the 1.1.x line for Spring Boot 3.5.x and the 2.x line for Spring Boot 4.x. This pairing is a provisional compatibility baseline, not a verified final dependency lock.

**Alternative route.** Spring Boot 4.x together with Spring AI 2.x is the alternative route. The final choice between the two routes must be based on official compatibility information and the model-provider integrations the project actually requires.

**Compatibility review findings (2026-10-26).** A review was performed on 2026-10-26. The official Spring pages could not be reached directly from the development environment, so the following findings come from multiple independent secondary sources and must be confirmed against official sources before any dependency is locked:

- Spring Boot 3.5.x open-source support is reported to have ended on 2026-06-25, with 3.5.16 reported as the final open-source release of the 3.5.x line.
- Spring AI 1.1.x is reported to be in terminal maintenance, with 1.1.8 (2026-06-12) reported as its final release, positioned for existing projects that remain on Spring Boot 3.x.
- Spring AI 2.0.0 GA is reported to have been released on 2026-06-12, requiring Spring Boot 4.0.x/4.1.x as its minimum baseline. Spring Boot 4.1.0 is reported to have been released on 2026-06-10.

If these findings are confirmed, starting a new codebase on the 3.5.x line would mean beginning on a version line that no longer receives open-source security patches, followed by a migration with known breaking changes (Jackson 3, modular starters, JSpecify null-safety, and documented Spring AI 1.x-to-2.0 incompatibilities). Choosing the 4.x route instead accepts a newer line with potential third-party ecosystem lag, and Spring AI 1.x learning material does not transfer unchanged.

**Preliminary direction.** On 2026-10-26 the project owner indicated a preference for the 4.x route (Java 21 + Spring Boot 4.1.x + Spring AI 2.0.x) based on the review above. This direction is recorded as preliminary, not final: it must be confirmed against official compatibility information and the required model-provider integrations before the application build configuration is created. No exact patch versions are pinned by this document.

**Known documentation inconsistency.** `docs/technology-stack.md`, created in the same working session on 2026-10-26, records the 4.x route as the confirmed baseline as a result of the review. This document intentionally keeps the conservative provisional framing required for the Day 4 baseline. The two documents must be reconciled after official confirmation: either the 4.x route is officially verified and both documents updated, or this document is revised. No build configuration exists in the repository yet, so the inconsistency has no code impact at this time.

Before the first implementation, verify the latest compatible patch releases, Java requirements, dependency support, required model-provider integrations, testing libraries, and migration implications against official sources. If the Spring Boot 4.x and Spring AI 2.x line better satisfies the project requirements, document the trade-off and revise this decision before creating the application build configuration.

Official references:

- Spring Boot system requirements: https://docs.spring.io/spring-boot/system-requirements.html
- Spring AI project and compatibility guidance: https://github.com/spring-projects/spring-ai
- Spring AI reference documentation: https://docs.spring.io/spring-ai/reference/

### 简体中文

**暂定基线。** 保守基线是 Java 21、Spring Boot 3.5.x 和 Spring AI 1.1.x。Spring AI 项目文档说明，1.1.x 系列对应 Spring Boot 3.5.x，2.x 系列对应 Spring Boot 4.x。该组合是暂定的兼容性基线，不是已经验证完成的最终依赖锁定方案。

**替代路线。** Spring Boot 4.x 与 Spring AI 2.x 组合是替代路线。两条路线之间的最终选择，必须依据官方兼容性资料和项目实际需要的模型供应商集成能力来决定。

**兼容性核对发现（2026-10-26）。** 2026-10-26 进行了一次兼容性核对。由于开发环境当时无法直接访问 Spring 官方页面，以下发现来自多个独立二手来源，在锁定任何依赖之前必须通过官方来源确认：

- 据报道，Spring Boot 3.5.x 的开源支持已于 2026-06-25 结束，3.5.16 是 3.5.x 开源线的最后一个版本。
- 据报道，Spring AI 1.1.x 已进入收尾维护阶段，1.1.8（2026-06-12）是其最后一个版本，定位是服务于留在 Spring Boot 3.x 上的存量项目。
- 据报道，Spring AI 2.0.0 GA 于 2026-06-12 发布，最低要求 Spring Boot 4.0.x/4.1.x。Spring Boot 4.1.0 于 2026-06-10 发布。

如果上述发现得到确认，让新代码库从 3.5.x 路线起步，意味着起步就在一条不再接收开源安全补丁的版本线上，随后还必须进行一次存在已知破坏性变更的迁移（Jackson 3、Starter 模块化、JSpecify 空安全，以及 Spring AI 1.x 到 2.0 的已记录不兼容变更）。选择 4.x 路线则要接受较新的版本线和第三方生态适配可能滞后的风险，且针对 Spring AI 1.x 的学习资料不能原样照搬。

**初步倾向。** 2026-10-26，项目所有者基于上述核对结果表示倾向选择 4.x 路线（Java 21 + Spring Boot 4.1.x + Spring AI 2.0.x）。该倾向仅作为初步记录，不是最终决定：在创建应用构建配置之前，必须结合官方兼容性资料和实际需要的模型供应商集成能力进行确认。本文档不锁定任何具体补丁版本。

**已知文档不一致。** 同一工作日（2026-10-26）创建的 `docs/technology-stack.md` 因上述核对结果，将 4.x 路线记录为已确认基线。本文档按 Day 4 基线要求，有意保留了保守的暂定表述。两份文档需要在官方确认之后进行统一：要么在官方验证通过后同时更新两份文档确认 4.x 路线，要么修订本文档。仓库中尚不存在任何构建配置，因此该不一致目前不会造成代码层面的影响。

首次实现之前，需要对照官方来源核对兼容的最新补丁版本、Java 要求、依赖支持情况、所需模型供应商集成、测试库以及迁移影响。如果 Spring Boot 4.x 与 Spring AI 2.x 路线更符合项目要求，应先记录取舍并更新本决策，再创建应用构建配置。

官方参考资料：

- Spring Boot 系统要求：https://docs.spring.io/spring-boot/system-requirements.html
- Spring AI 项目及兼容性说明：https://github.com/spring-projects/spring-ai
- Spring AI 参考文档：https://docs.spring.io/spring-ai/reference/

## 5. Database and Vector Search

PostgreSQL is the proposed primary database for transactional business data, configuration, and persistent application state. The design should keep database access behind clear repository or persistence boundaries.

When the Knowledge Assistant milestone begins, evaluate pgvector for storing embeddings and performing similarity search within PostgreSQL. Validate extension availability in the selected local and deployment environments before relying on it.

Do not introduce a separate vector database until measured scale, isolation, performance, or operational requirements justify it.

Official reference: https://github.com/pgvector/pgvector

PostgreSQL 暂定作为主数据库，用于存储业务事务数据、配置和持久化应用状态。数据库访问应通过清晰的 Repository 或持久化边界进行封装。

知识助手阶段开始时，评估 pgvector 是否能够满足向量存储和相似度检索需求。在依赖它之前，需要确认选定的本地环境和部署环境都支持该扩展。

在数据规模、隔离性、性能或运维要求尚未证明其必要性之前，不引入独立向量数据库。

官方参考资料：https://github.com/pgvector/pgvector

## 6. AI Provider Integration

Use Spring AI abstractions where they meet the required functionality, and isolate provider-specific configuration and behavior behind an application-level integration boundary.

Keep model identifiers, credentials, endpoint configuration, timeouts, token or cost limits, and provider-specific options outside business-domain logic. Secrets must come from environment configuration or an appropriate secret-management mechanism and must never be committed.

Do not assume every model provider supports the same tools, structured output, streaming, embedding dimensions, or multimodal capabilities. Verify the features required by each use case.

在满足功能要求的前提下，优先使用 Spring AI 提供的抽象，并将供应商特定的配置和行为隔离在应用集成边界之后。

模型标识、凭据、端点配置、超时、Token 或成本限制，以及供应商特定选项，不应散落在业务领域逻辑中。密钥必须通过环境配置或适当的密钥管理机制提供，严禁提交到仓库。

不能假设所有模型供应商都支持相同的工具调用、结构化输出、流式响应、Embedding 维度或多模态能力。每个业务场景所需的功能都必须单独验证。

## 7. Infrastructure Introduction Policy

- Redis: introduce for a demonstrated need such as shared caching, distributed coordination, or rate limiting.
- Kafka or RabbitMQ: introduce when asynchronous messaging, decoupling, or event processing is required.
- Elasticsearch or OpenSearch: evaluate when the log-intelligence module needs scalable log indexing and search.
- Kubernetes: evaluate when deployment scale or operational requirements justify orchestration.
- A separate vector database: evaluate only when PostgreSQL plus pgvector is insufficient.

For each new infrastructure component, document the use case, operational burden, failure modes, security implications, local development experience, and testing strategy.

- Redis：只有共享缓存、分布式协调或限流等具体需求得到确认后再引入。
- Kafka 或 RabbitMQ：在确实需要异步消息、系统解耦或事件处理时引入。
- Elasticsearch 或 OpenSearch：日志智能分析模块需要可扩展的日志索引与检索时再评估。
- Kubernetes：部署规模或运维要求足以证明其必要性时再评估。
- 独立向量数据库：只有 PostgreSQL 加 pgvector 无法满足需求时才考虑。

每增加一个基础设施组件，都应记录其业务场景、运维负担、故障模式、安全影响、本地开发体验和测试策略。

## 8. Testing and Quality

Use unit tests for deterministic business rules and boundary conditions. Use integration tests for persistence, model-provider adapters, and infrastructure interactions where practical.

AI behavior must be evaluated separately from deterministic business logic. Tests should cover invalid model output, missing or malformed tool arguments, authorization failures, timeouts, provider errors, and budget limits where applicable.

Document the exact commands used to validate the project. Never report a test or build as successful unless it was actually executed and passed.

使用单元测试覆盖确定性的业务规则和边界条件。在可行的情况下，使用集成测试验证持久化、模型供应商适配器和基础设施交互。

AI 行为应与确定性的业务逻辑分开评估。根据具体功能，测试应覆盖模型输出无效、工具参数缺失或格式错误、权限校验失败、超时、供应商异常和预算限制等情况。

文档必须记录实际使用的验证命令。没有真实执行并通过的构建或测试，不能报告为成功。

## 9. Decisions Deferred

The following decisions remain open until the corresponding implementation milestone:

- Final choice between the Spring Boot 3.5.x + Spring AI 1.1.x line and the Spring Boot 4.x + Spring AI 2.x line, confirmed against official documentation and required provider integrations.
- Exact framework patch versions and dependency versions to pin.
- Initial model provider and fallback strategy.
- Authentication and authorization implementation.
- Whether tenant isolation is required in the first release.
- Exact database migration tool and schema-management conventions.
- Whether Redis or a message broker is needed for the first end-to-end workflow.
- Vector indexing strategy and evaluation dataset.
- Log storage and search architecture for Log Intelligence.

以下决策推迟到对应的实现阶段：

- 在 Spring Boot 3.5.x + Spring AI 1.1.x 路线与 Spring Boot 4.x + Spring AI 2.x 路线之间的最终选择，需以官方文档和实际需要的供应商集成为依据确认。
- 需要锁定的框架补丁版本和依赖的精确版本。
- 首个模型供应商及其故障回退方案。
- 认证与授权的具体实现方式。
- 首个版本是否需要租户隔离。
- 数据库迁移工具和 Schema 管理规范。
- 首个端到端流程是否需要 Redis 或消息中间件。
- 向量索引策略和评估数据集。
- 日志智能分析模块的日志存储与检索架构。

## 10. Review Checklist

- [ ] The proposed stack is consistent with the product charter and system architecture.
- [ ] Framework compatibility has been checked against official documentation.
- [ ] Confirmed decisions and provisional choices are clearly distinguished.
- [ ] Deferred infrastructure has a concrete introduction criterion.
- [ ] No unnecessary dependencies or infrastructure are introduced by this document.
- [ ] English content appears first, followed immediately by its Simplified Chinese counterpart.
- [ ] Any unresolved compatibility or design issue is reported explicitly.

- [ ] 技术栈与产品章程和系统架构保持一致。
- [ ] 已根据官方文档核对框架兼容性。
- [ ] 已明确区分确定的决策与暂定选择。
- [ ] 已为暂缓引入的基础设施制定明确的引入条件。
- [ ] 本文档没有要求引入不必要的依赖或基础设施。
- [ ] 英文内容在前，随后紧接对应的简体中文内容。
- [ ] 所有尚未解决的兼容性或设计问题都已明确报告。

Note: the compatibility findings recorded in section 4 come from secondary sources; the checklist item on official documentation verification therefore remains unchecked until the official pages are re-checked directly and the Spring Boot / Spring AI route is finalized.

说明：第 4 节记录的兼容性发现来自二手来源；在直接复核官方页面并最终确定 Spring Boot / Spring AI 路线之前，关于官方文档核对的检查项保持未勾选。
