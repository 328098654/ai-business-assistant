# AI Business Assistant — Product Charter

# AI Business Assistant — 产品定义

## 1. Product Overview / 产品概述

### English

AI Business Assistant is an enterprise-oriented AI application platform that helps users interact with business data, retrieve organizational knowledge, build analytical reports, and investigate operational issues through natural language and controlled AI-powered workflows.

The platform combines conventional backend engineering with AI application capabilities. Its primary focus is reliability, security, explainability, maintainability, and practical business value.

### 简体中文

AI Business Assistant 是一个面向企业场景的 AI 应用平台，帮助用户通过自然语言和受控的 AI 工作流查询业务数据、检索组织知识、创建分析报表以及排查系统运行问题。

平台将传统后端工程能力与 AI 应用能力结合起来，重点关注可靠性、安全性、可解释性、可维护性和实际业务价值。

## 2. Target Audience / 目标用户

### English

The initial target audience includes:

- Small and medium-sized businesses that need AI-assisted business operations.
- Development teams that want to integrate AI capabilities into existing applications.
- Technical teams that need knowledge retrieval, reporting, and log investigation tools.
- Developers evaluating practical enterprise AI architecture and implementation patterns.

### 简体中文

初期目标用户包括：

- 希望利用 AI 辅助日常业务运营的中小型企业。
- 希望将 AI 能力集成到现有应用中的开发团队。
- 需要知识检索、数据报表和日志排查工具的技术团队。
- 希望研究企业级 AI 架构与实际落地方式的开发人员。

## 3. Core Capabilities / 核心能力

### 3.1 Business Agent / 业务智能体

**English**

Allow users to query business information using natural language. The AI agent may invoke registered business tools, subject to authorization, validation, execution limits, and audit requirements.

**简体中文**

允许用户通过自然语言查询业务信息。AI Agent 可以调用已注册的业务工具，但必须受到权限校验、参数验证、执行限制和审计机制的约束。

### 3.2 Knowledge Assistant / 知识助手

**English**

Allow users to upload and retrieve organizational documents. Answers should be grounded in retrieved evidence and provide source references whenever applicable.

**简体中文**

允许用户导入和检索组织文档。回答应以检索到的证据为依据，并在适用时提供来源引用。

### 3.3 AI Report Studio / AI 报表工作室

**English**

Allow users to create, inspect, and modify analytical reports through natural language and visual editing. Reports must use validated specifications and controlled data queries.

**简体中文**

允许用户通过自然语言和可视化编辑创建、查看和修改分析报表。报表必须使用经过校验的规格定义和受控的数据查询。

### 3.4 AI Log Intelligence / AI 日志智能分析

**English**

Help technical teams search structured logs, identify abnormal patterns, correlate distributed traces, and generate evidence-based troubleshooting suggestions.

AI-generated root-cause hypotheses must be distinguished from verified facts.

**简体中文**

帮助技术团队检索结构化日志、发现异常模式、关联分布式调用链，并生成基于证据的排障建议。

AI 生成的根因假设必须与已经验证的事实明确区分。

## 4. Shared Platform Capabilities / 共享平台能力

### English

The platform may progressively introduce the following shared capabilities:

- Model provider integration and configuration.
- Agent runtime and controlled tool execution.
- Conversation and task state management.
- Authentication, authorization, and tenant isolation.
- Audit records, execution traces, and cost tracking.
- Error handling, timeouts, retries, and recovery.
- Automated testing, evaluation, and deployment.

These capabilities should be introduced when justified by actual use cases. The initial implementation must avoid unnecessary service fragmentation and premature abstraction.

### 简体中文

平台将根据实际需求逐步引入以下共享能力：

- 模型供应商接入与配置。
- Agent Runtime（智能体运行时）和受控工具执行。
- 对话与任务状态管理。
- 身份认证、权限控制和租户隔离。
- 审计记录、执行追踪和成本统计。
- 异常处理、超时、重试和任务恢复。
- 自动化测试、效果评估和部署能力。

这些能力应由真实业务场景驱动，而不是为了架构完整而一次性全部实现。初期必须避免不必要的服务拆分和过早抽象。

## 5. Engineering Principles / 工程原则

### English

1. Build a working end-to-end vertical slice before expanding the entire platform.
2. Keep business logic deterministic and enforceable wherever possible.
3. Treat LLM output as untrusted input and validate it before execution.
4. Enforce authorization on the server side, including tool execution and data access.
5. Prefer explicit contracts, automated tests, and observable execution.
6. Introduce infrastructure only when a concrete requirement justifies it.
7. Document important architectural decisions and their trade-offs.
8. Never claim a test or build succeeded unless it was actually executed and verified.

### 简体中文

1. 先构建一个可运行的端到端业务闭环，再扩展整个平台。
2. 尽可能让业务逻辑保持确定性，并确保关键规则可以被强制执行。
3. 将大模型输出视为不可信输入，在执行之前进行校验。
4. 在服务端执行权限控制，包括工具调用和数据访问。
5. 优先采用明确的接口契约、自动化测试和可观测的执行流程。
6. 只有在具体需求能够证明其必要性时，才引入新的基础设施。
7. 记录重要架构决策及其取舍。
8. 未实际执行和验证的测试或构建，不得宣称已经通过。

## 6. Initial Delivery Strategy / 初期交付策略

### English

The initial release will focus on one demonstrable end-to-end workflow that validates the platform foundation, AI integration, controlled tool execution, error handling, and automated testing.

The four core capabilities remain part of the long-term product scope. They do not need to be fully implemented in the first release.

The project is milestone-driven rather than bound to a fixed 100-day completion deadline.

### 简体中文

首期交付将聚焦于一个可演示的端到端工作流，用于验证平台基础、AI 接入、受控工具执行、异常处理和自动化测试。

四大核心能力仍然属于长期产品范围，但不要求在首个版本中全部完成。

项目以里程碑和验收结果推进，不再以固定的 100 天完成期限作为约束。

## 7. Success Criteria / 成功标准

### English

The project should progressively demonstrate that it can:

- Be cloned and started using documented instructions.
- Provide a working application interface and documented APIs.
- Execute AI-assisted workflows with validated tool inputs and controlled permissions.
- Handle expected failures without silently corrupting application state.
- Pass reproducible automated tests.
- Provide a clear architecture explanation and a realistic demonstration.
- Be understood and evaluated by an international technical audience.

### 简体中文

项目应逐步证明自己能够：

- 根据文档完成代码克隆和环境启动。
- 提供可用的应用界面和清晰的 API 文档。
- 在参数校验和权限控制下执行 AI 辅助工作流。
- 正确处理预期内的故障，避免应用状态被静默破坏。
- 通过可重复执行的自动化测试。
- 提供清晰的架构说明和真实可运行的演示。
- 让海外技术人员能够理解、运行并评估项目。

## 8. Explicit Non-Goals / 明确不在当前目标内

### English

- Training or fine-tuning foundation models.
- Building a foundation model from scratch.
- Implementing all distributed infrastructure before validating the use cases.
- Completing every planned capability within a fixed 100-day period.
- Treating AI-generated code volume as the primary measure of engineering quality.
- Implementing stock market intelligence as a core product module.

### 简体中文

- 不进行基础大模型训练或微调。
- 不从零构建基础大模型。
- 不在验证业务场景之前搭建全部分布式基础设施。
- 不要求在固定的 100 天内完成所有规划能力。
- 不以 AI 生成代码的数量作为工程质量的主要衡量标准。
- 不将股票行情智能分析作为核心产品模块。

## 9. Documentation and Collaboration / 文档与协作约定

### English

Project documentation must present English first, followed immediately by the corresponding Simplified Chinese translation in the same Markdown file.

Code changes should follow the agreed scope and be accompanied by relevant tests. Major architecture changes, new infrastructure dependencies, database migrations, and breaking API changes require discussion before implementation.

Changes must be reviewed before being committed or pushed.

### 简体中文

项目文档必须在同一个 Markdown 文件中先展示英文，再紧接着展示对应的简体中文翻译。

代码修改应遵守已确认的任务范围，并配套相关测试。重大架构变更、新增基础设施依赖、数据库迁移和不兼容的 API 变更必须先讨论，再实施。

所有修改都需要经过审查后，才能决定是否提交或推送。
