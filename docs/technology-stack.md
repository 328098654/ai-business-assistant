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

## 3. Technology Stack

| Area | Choice | Status |
|---|---|---|
| Backend language | Java 21 LTS | Confirmed baseline |
| Backend framework | Spring Boot 4.1.x | Confirmed baseline (revised 2026-10-26); verify exact patch before build config |
| AI application framework | Spring AI 2.0.x | Confirmed baseline (revised 2026-10-26); verify required model-provider integrations |
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

| 领域 | 选择 | 状态 |
|---|---|---|
| 后端语言 | Java 21 LTS | 已确认基线 |
| 后端框架 | Spring Boot 4.1.x | 已确认基线（2026-10-26 修订）；创建构建配置前需确认具体补丁版本 |
| AI 应用框架 | Spring AI 2.0.x | 已确认基线（2026-10-26 修订）；需验证所需模型供应商集成 |
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

**Original proposal.** The initial conservative baseline proposed in this document was Java 21 with Spring Boot 3.5.x and Spring AI 1.1.x, based on the Spring AI compatibility guidance that maps the 1.1.x line to Spring Boot 3.5.x and the 2.x line to Spring Boot 4.x.

**Revision (2026-10-26).** A compatibility review performed on 2026-10-26 found that the 3.5.x line is no longer viable as a starting point for a new project:

- Spring Boot 3.5.x open-source support ended on 2026-06-25; 3.5.16 was the final open-source release of that line.
- Spring AI 1.1.x received its final release (1.1.8) on 2026-06-12 and is positioned as the line for existing projects that remain on Spring Boot 3.x.
- Spring AI 2.0.0 GA was released on 2026-06-12 and requires Spring Boot 4.0.x/4.1.x as its minimum baseline. Spring Boot 4.1.0 was released on 2026-06-10.

Starting a new codebase on the Boot 3.5 + Spring AI 1.1 line would mean beginning on a version line that no longer receives open-source security patches, followed by a forced migration to the 4.x line. That migration involves known breaking changes (Jackson 3, modular starters, JSpecify null-safety, and roughly nine documented Spring AI 1.x-to-2.0 incompatibilities), so deferring it only increases cost.

**Decision.** Adopt Java 21 with Spring Boot 4.1.x and Spring AI 2.0.x as the confirmed baseline. Java 21 remains valid because Spring Boot 4.x supports Java 17+ and officially recommends the Java 21/25 LTS lines.

**Trade-offs accepted.**

- Spring AI 2.0 is an architectural rewrite relative to 1.x: ChatClient is the sole recommended entry point, the tool-calling loop moved onto the Advisor chain, and provider modules such as Anthropic now build on official vendor SDKs. Learning material and community examples for 1.x will not transfer unchanged.
- The 4.x line is newer, so third-party starters and ecosystem libraries may lag in compatibility. Any dependency must be checked against Spring Boot 4.1 before being added.
- The facts above were cross-checked across multiple independent secondary sources because the official Spring pages were not directly accessible from the development environment at review time. Before creating the application build configuration, confirm the exact current patch releases of Spring Boot 4.1.x and Spring AI 2.0.x against the official sources below, and record the pinned versions.

Official references:

- Spring Boot system requirements: https://docs.spring.io/spring-boot/system-requirements.html
- Spring AI project and compatibility guidance: https://github.com/spring-projects/spring-ai
- Spring AI reference documentation: https://docs.spring.io/spring-ai/reference/

### 简体中文

**原方案。** 本文档最初的保守基线是 Java 21、Spring Boot 3.5.x 和 Spring AI 1.1.x，依据是 Spring AI 官方的兼容性对应关系：1.1.x 系列对应 Spring Boot 3.5.x，2.x 系列对应 Spring Boot 4.x。

**修订（2026-10-26）。** 2026-10-26 进行的兼容性核对发现，3.5.x 路线已不适合作为新项目的起点：

- Spring Boot 3.5.x 的开源支持已于 2026-06-25 结束；3.5.16 是该开源线的最后一个版本。
- Spring AI 1.1.x 的最后一个版本 1.1.8 于 2026-06-12 发布，定位是服务于留在 Spring Boot 3.x 上的存量项目。
- Spring AI 2.0.0 GA 于 2026-06-12 发布，最低要求 Spring Boot 4.0.x/4.1.x。Spring Boot 4.1.0 于 2026-06-10 发布。

让一个全新的代码库从 Boot 3.5 + Spring AI 1.1 起步，意味着起步就在一条不再接收开源安全补丁的版本线上，随后还必须迁移到 4.x。该迁移存在已知的破坏性变更（Jackson 3、Starter 模块化、JSpecify 空安全，以及 Spring AI 1.x 到 2.0 约九处有文档记录的不兼容变更），拖延只会增加成本。

**决策。** 采用 Java 21、Spring Boot 4.1.x 和 Spring AI 2.0.x 作为已确认基线。Java 21 依然成立：Spring Boot 4.x 支持 Java 17 及以上，并官方推荐 Java 21/25 LTS。

**已接受的取舍。**

- Spring AI 2.0 相对 1.x 是一次架构重写：ChatClient 成为唯一推荐的入口，工具调用循环移入 Advisor 链，Anthropic 等供应商模块改为基于官方 SDK 构建。针对 1.x 的学习资料和社区示例不能原样照搬。
- 4.x 路线较新，部分第三方 starter 和生态库的适配可能滞后。任何依赖在引入之前都必须核对与 Spring Boot 4.1 的兼容性。
- 上述事实在核对时因开发环境无法直接访问 Spring 官方页面，是通过多个独立二手来源交叉验证的。在创建应用构建配置之前，需通过以下官方来源确认 Spring Boot 4.1.x 和 Spring AI 2.0.x 当前的确切补丁版本，并记录锁定的版本号。

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

- Exact patch releases of Spring Boot 4.1.x and Spring AI 2.0.x to pin in the build configuration, confirmed against official sources.
- Initial model provider and fallback strategy, verified against Spring AI 2.0 provider support.
- Authentication and authorization implementation.
- Whether tenant isolation is required in the first release.
- Exact database migration tool and schema-management conventions.
- Whether Redis or a message broker is needed for the first end-to-end workflow.
- Vector indexing strategy and evaluation dataset.
- Log storage and search architecture for Log Intelligence.

以下决策推迟到对应的实现阶段：

- 需要在构建配置中锁定的 Spring Boot 4.1.x 和 Spring AI 2.0.x 的确切补丁版本，以官方来源确认。
- 首个模型供应商及其故障回退方案，需结合 Spring AI 2.0 的供应商支持情况验证。
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

Note: the framework compatibility check for the 2026-10-26 revision was performed against multiple independent secondary sources; official Spring pages must be re-checked directly before pinning exact patch versions (see section 4). The checklist above therefore remains unchecked until that confirmation is done.

说明：2026-10-26 修订所依据的框架兼容性核对来自多个独立二手来源；在锁定确切补丁版本之前，必须直接复核 Spring 官方页面（见第 4 节）。因此上述清单在该确认完成之前保持未勾选状态。

## 11. Change Record / 变更记录

### English

- 2026-10-26: Baseline revised from Spring Boot 3.5.x + Spring AI 1.1.x to Spring Boot 4.1.x + Spring AI 2.0.x. Reason: Spring Boot 3.5.x open-source support ended 2026-06-25 and Spring AI 1.1.x is in terminal maintenance, while Spring AI 2.0 GA (2026-06-12) requires the Boot 4.x line. Rationale and trade-offs are documented in section 4. Java 21 baseline unchanged.

### 简体中文

- 2026-10-26：基线由 Spring Boot 3.5.x + Spring AI 1.1.x 修订为 Spring Boot 4.1.x + Spring AI 2.0.x。原因：Spring Boot 3.5.x 开源支持已于 2026-06-25 结束，Spring AI 1.1.x 处于收尾维护状态，而 Spring AI 2.0 GA（2026-06-12）要求 Boot 4.x 路线。理由与取舍见第 4 节。Java 21 基线不变。
