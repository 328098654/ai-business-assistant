# AI Business Assistant — System Architecture

# AI Business Assistant — 系统架构设计

## 1. Architecture Goals / 架构目标

### English

The architecture must support four core capabilities: Business Agent, Knowledge Assistant, AI Report Studio, and AI Log Intelligence.

The system should be suitable for a portfolio-quality implementation and a realistic enterprise application. It must remain understandable, testable, deployable, and extensible without introducing unnecessary infrastructure at the beginning.

The initial architecture will use a modular monolith with explicit module boundaries. Independent services may be introduced later when justified by deployment, scalability, security, or operational requirements.

### 简体中文

系统架构必须支持四项核心能力：Business Agent、Knowledge Assistant、AI Report Studio 和 AI Log Intelligence。

系统既要适合作为高质量技术作品集，也要具备真实企业应用的工程特征，同时保持易理解、易测试、易部署和可扩展，避免初期引入不必要的基础设施。

初期采用具有明确模块边界的模块化单体架构。未来只有在部署、扩展性、安全或运维需求能够证明其必要性时，才考虑拆分为独立服务。

## 2. High-Level Architecture / 总体架构

### English

The system is organized into five logical layers:

1. Presentation Layer: Web application and external API clients.
2. Application Layer: Use cases, orchestration, and application services.
3. Domain Layer: Business rules, domain models, and module contracts.
4. Infrastructure Layer: Database access, LLM providers, vector search, messaging, and external integrations.
5. Cross-Cutting Capabilities: Authentication, authorization, auditing, observability, configuration, and error handling.

Cross-cutting capabilities must be applied consistently without allowing shared infrastructure code to absorb business-specific logic.

### 简体中文

系统分为五个逻辑层：

1. 展示层：Web 应用和外部 API 客户端。
2. 应用层：业务用例、流程编排和应用服务。
3. 领域层：业务规则、领域模型和模块契约。
4. 基础设施层：数据库访问、大模型供应商、向量检索、消息中间件和外部系统集成。
5. 横切能力层：身份认证、权限控制、审计、可观测性、配置管理和异常处理。

横切能力需要统一应用，但不能让公共基础设施代码逐渐吸收各业务模块的专属逻辑。

### Architecture Diagram / 架构示意图

```text
┌───────────────────────────────────────────────────────────────────┐
│                     Web Frontend / Web 前端                        │
│            Vue 3 · TypeScript · ECharts (candidate / 候选)          │
└───────────────────────────────┬───────────────────────────────────┘
                                │
┌───────────────────────────────▼───────────────────────────────────┐
│                Application API / 应用接口层                         │
│        Validation · Authentication · Authorization                │
│            参数校验 · 身份认证 · 权限控制                            │
└───────────────────────────────┬───────────────────────────────────┘
                                │
┌───────────────┬───────────────┼───────────────┬───────────────────┐
│ Business Agent│   Knowledge   │   AI Report   │  AI Log           │
│   业务查询     │   Assistant   │    Studio     │  Intelligence     │
│               │   知识检索     │   报表分析     │   日志排障         │
└───────┬───────┴───────┬───────┴───────┬───────┴─────────┬─────────┘
        └───────────────┴───────┬───────┴─────────────────┘
                                │
┌───────────────────────────────▼───────────────────────────────────┐
│          Shared Agent Runtime / 共享智能体运行时                    │
│      Model Adapter · Tool Registry · Execution Control            │
│            模型适配 · 工具注册 · 执行控制                            │
└───────────────┬───────────────────────────────┬───────────────────┘
                │                               │
┌───────────────▼─────────────────┐  ┌──────────▼───────────────────┐
│      Data Layer / 数据层         │  │  External Providers          │
│      关系型数据库 · 向量检索       │  │  / 外部供应商                │
│                                 │  │  LLM API · 外部系统           │
└─────────────────────────────────┘  └──────────────────────────────┘

Cross-cutting: Identity · Audit · Logs · Metrics · Tracing
横切能力：身份权限 · 审计 · 日志 · 指标 · 链路追踪
```

## 3. Logical Module Boundaries / 逻辑模块边界

### English

The initial codebase should distinguish the following logical modules:

- `platform-api`: External API contracts and shared transport models where appropriate.
- `platform-core`: Shared technical abstractions, common error handling, and execution context.
- `identity`: Authentication, authorization, and tenant context.
- `agent-runtime`: Agent execution, tool registration, model interaction, execution limits, and task lifecycle.
- `business-agent`: Business data queries and controlled business tools.
- `knowledge-assistant`: Document ingestion, chunking, retrieval, and source-grounded answers.
- `report-studio`: Report specifications, query validation, report execution, and report metadata.
- `log-intelligence`: Structured log search, anomaly analysis, trace correlation, and troubleshooting evidence.
- `observability`: Shared logging, metrics, tracing, and audit integration where needed.

These names describe logical boundaries, not a requirement to create a separate deployable service for every module.

The exact Maven module structure will be finalized after the technology and dependency review.

### 简体中文

初期代码库应区分以下逻辑模块：

- `platform-api`：外部 API 契约，以及适用范围内的共享传输模型。
- `platform-core`：共享技术抽象、通用异常处理和执行上下文。
- `identity`：身份认证、权限控制和租户上下文。
- `agent-runtime`：Agent 执行、工具注册、模型交互、执行限制和任务生命周期管理。
- `business-agent`：业务数据查询和受控业务工具。
- `knowledge-assistant`：文档导入、文本分块、检索和基于来源的回答。
- `report-studio`：报表规格、查询校验、报表执行和报表元数据。
- `log-intelligence`：结构化日志检索、异常分析、调用链关联和排障证据。
- `observability`：根据实际需要整合公共日志、指标、追踪和审计能力。

这些名称表示逻辑边界，不意味着每个模块都必须部署成独立服务。

具体 Maven 模块结构将在技术版本与依赖评估后最终确定。

## 4. Initial Deployment Model / 初期部署模型

### English

The first release should minimize deployment complexity:

- One backend application containing clearly separated modules.
- One web frontend.
- A relational database for application data and metadata.
- A vector search capability for knowledge retrieval, introduced when the RAG milestone begins.
- An LLM provider accessed through a controlled integration layer.
- Optional Redis, message broker, and log search infrastructure only when a concrete use case requires them.

Docker Compose will be used to make local development and demonstrations reproducible.

### 简体中文

首期版本应尽量降低部署复杂度：

- 一个包含清晰模块边界的后端应用。
- 一个 Web 前端应用。
- 一个用于存储应用数据和元数据的关系型数据库。
- 在 RAG 阶段开始时引入知识检索所需的向量检索能力。
- 通过受控的集成层访问大模型供应商。
- Redis、消息中间件和日志搜索基础设施仅在具体场景需要时引入。

使用 Docker Compose 让本地开发和项目演示能够重复执行。

## 5. Agent Runtime / 智能体运行时

### English

Agent Runtime is a shared capability rather than a separate business product.

Its responsibilities may include:

- Registering tools with explicit input and output schemas.
- Selecting and executing permitted tools.
- Validating model-generated arguments before execution.
- Enforcing authorization, timeouts, and execution budgets.
- Recording task state, tool results, and execution traces.
- Handling bounded retries and recoverable failures.
- Supporting human approval for sensitive operations when needed.

The runtime must not treat an LLM response as authorization to execute an operation. All permissions and business constraints must be enforced by application code.

Persistent workflows, resumable execution, and human approval should be implemented incrementally, based on the first real use case.

### 简体中文

Agent Runtime（智能体运行时）是一项共享平台能力，而不是独立的业务产品。

它的职责可以包括：

- 使用明确的输入和输出结构注册工具。
- 选择并执行获得授权的工具。
- 在执行前校验大模型生成的参数。
- 执行权限、超时和执行预算限制。
- 记录任务状态、工具结果和执行追踪信息。
- 处理有限次数的重试和可恢复故障。
- 在必要时支持敏感操作的人工审批。

运行时不能把大模型的回答视为执行操作的授权。所有权限和业务约束必须由应用代码强制执行。

持久化工作流、可恢复执行和人工审批应围绕首个真实业务场景逐步实现。

## 6. Dependency Rules / 依赖规则

### English

1. Business modules must not directly depend on one another's internal implementation.
2. Cross-module interaction must use explicit application interfaces or documented contracts.
3. Domain logic should not depend directly on vendor-specific infrastructure implementations.
4. LLM provider integration must be isolated behind an appropriate application boundary.
5. Shared modules must remain small and must not become dumping grounds for unrelated functionality.
6. Circular module dependencies are not permitted.
7. Infrastructure choices must be justified by concrete requirements and documented trade-offs.

### 简体中文

1. 业务模块不能直接依赖其他模块的内部实现。
2. 跨模块交互必须通过明确的应用接口或有文档说明的契约完成。
3. 领域逻辑不应直接依赖特定供应商的基础设施实现。
4. 大模型供应商接入必须通过合适的应用边界进行隔离。
5. 共享模块必须保持精简，不能成为无关功能的堆积区。
6. 不允许模块之间出现循环依赖。
7. 基础设施选型必须有具体需求依据，并记录相关取舍。

## 7. Security and Observability / 安全与可观测性

### English

The architecture must progressively provide:

- Server-side authorization for APIs, data access, and tool execution.
- Tenant isolation when multi-tenancy is introduced.
- Secret management through environment configuration or an appropriate secret store.
- Audit records for important actions and sensitive tool execution.
- Correlation identifiers for requests and agent tasks.
- Structured logs, meaningful error responses, and health checks.
- Limits on model calls, tool execution, query size, and execution time.

Secrets must never be committed to the repository or included in logs and demonstrations.

### 简体中文

架构应逐步提供：

- 对 API、数据访问和工具执行进行服务端权限控制。
- 引入多租户时保证租户隔离。
- 通过环境配置或合适的密钥管理系统管理敏感信息。
- 对重要操作和敏感工具执行保留审计记录。
- 为请求和 Agent 任务提供关联标识。
- 提供结构化日志、明确的错误响应和健康检查。
- 对模型调用、工具执行、查询规模和执行时间进行限制。

密钥不得提交到代码仓库，也不得写入日志或演示内容。

## 8. Future Evolution / 后续演进

### English

The platform may evolve toward independently deployable services if measurable requirements emerge.

Potential triggers include independent scaling, deployment isolation, stricter security boundaries, sustained workload growth, or operational ownership by separate teams.

Any extraction must define its API contract, failure behavior, observability, deployment strategy, and data ownership before implementation.

### 简体中文

当出现可衡量的实际需求时，平台可以逐步演进为独立部署的服务。

可能的触发条件包括独立扩缩容、部署隔离、更严格的安全边界、持续增长的工作负载，或由不同团队分别负责运维。

任何服务拆分都必须在实施前明确 API 契约、故障处理方式、可观测性、部署策略和数据所有权。

## 9. Architecture Decisions Pending Validation / 待验证的架构决策

### English

The following decisions remain provisional until the technology review:

- Java and Spring Boot versions.
- Spring AI compatibility and model provider integration.
- Maven module boundaries and dependency direction.
- Relational database and vector storage configuration.
- Frontend framework and API integration approach.
- Whether Redis, a message broker, or a dedicated log search engine is necessary.
- Authentication implementation and initial tenant model.

No production dependency should be added solely because it is commonly used in enterprise systems.

### 简体中文

以下决策在技术评估完成前仍属于候选方案：

- Java 和 Spring Boot 版本。
- Spring AI 兼容性及大模型供应商接入方式。
- Maven 模块边界和依赖方向。
- 关系型数据库与向量存储配置。
- 前端框架和 API 集成方式。
- 是否需要 Redis、消息中间件或独立日志搜索引擎。
- 身份认证实现方案和初期租户模型。

不能仅仅因为某个组件在企业系统中很常见，就将它加入生产依赖。
