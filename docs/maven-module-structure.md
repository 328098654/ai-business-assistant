# AI Business Assistant — Maven Module Structure

# AI Business Assistant — Maven 模块结构

## 1. Objectives / 设计目标

### English

The project will use a single Git repository and Maven multi-module build.

The module structure must support clear business boundaries, controlled dependencies, automated testing, and future evolution. It must not create unnecessary modules before their responsibilities are understood.

The initial structure is provisional. Final dependencies and framework-specific modules will be confirmed after the technology compatibility review.

### 简体中文

项目采用单个 Git 仓库和 Maven 多模块构建。

模块结构必须支持清晰的业务边界、受控的依赖关系、自动化测试和后续演进，同时避免在职责尚未明确时创建不必要的模块。

初始结构属于候选方案。最终依赖关系及框架相关模块将在技术兼容性评估后确定。

## 2. Repository Layout / 仓库目录结构

### English

The proposed repository layout is:

- `pom.xml`: Root Maven project and module aggregation.
- `apps/`: Executable applications and application entry points.
- `modules/`: Reusable platform and business modules.
- `frontend/`: Web frontend application.
- `docs/`: Product, architecture, API, development, and deployment documentation.
- `scripts/`: Development and demonstration scripts.
- `infra/`: Local infrastructure and deployment configuration.
- `.github/workflows/`: Continuous integration workflows.
- `CLAUDE.md`: Repository-specific coding and collaboration instructions.
- `.gitignore`: Files and directories excluded from version control.

The directory layout may be adjusted when the executable module and framework versions are confirmed.

### 简体中文

建议的仓库目录结构如下：

- `pom.xml`：根 Maven 工程及模块聚合配置。
- `apps/`：可执行应用和应用启动入口。
- `modules/`：可复用的平台模块和业务模块。
- `frontend/`：Web 前端应用。
- `docs/`：产品、架构、API、开发和部署文档。
- `scripts/`：开发与演示脚本。
- `infra/`：本地基础设施和部署配置。
- `.github/workflows/`：持续集成工作流。
- `CLAUDE.md`：仓库专属的编码和协作规范。
- `.gitignore`：需要排除在版本控制之外的文件和目录。

具体目录可以在可执行模块和框架版本确定后进行调整。

## 3. Initial Module Responsibilities / 初期模块职责

### English

The initial backend should be organized around the following responsibilities.

**Application entry point**

- Starts the backend application.
- Configures dependency injection and runtime settings.
- Exposes HTTP APIs.
- Coordinates application-level components.

**Platform core**

- Contains genuinely reusable technical abstractions.
- Provides shared error types and execution context where appropriate.
- Must not contain business-specific rules.

**Identity**

- Owns authentication and authorization concepts.
- Provides identity and tenant context when implemented.
- Must not allow business modules to bypass server-side authorization.

**Agent runtime**

- Owns model interaction abstractions, tool registration, execution control, and task lifecycle.
- Validates tool arguments before execution.
- Provides execution records and failure handling as the implementation evolves.

**Business agent**

- Owns business query use cases and business-specific tools.
- Must not contain generic model-provider implementation details.

**Knowledge assistant**

- Owns document ingestion, chunking, retrieval, and source-grounded answering.

**Report studio**

- Owns report specifications, validation, controlled query execution, and report metadata.

**Log intelligence**

- Owns log search, anomaly analysis, trace correlation, and troubleshooting evidence.

These responsibilities define logical ownership. They do not require every responsibility to become an independent Maven artifact immediately.

### 简体中文

初期后端围绕以下职责组织。

**应用启动入口**

- 启动后端应用。
- 配置依赖注入和运行环境。
- 暴露 HTTP API。
- 协调应用层组件。

**平台核心模块**

- 存放真正可复用的技术抽象。
- 在适用范围内提供公共异常类型和执行上下文。
- 不包含具体业务规则。

**身份模块**

- 负责身份认证和权限控制相关概念。
- 在功能实施时提供身份与租户上下文。
- 不允许业务模块绕过服务端权限校验。

**Agent Runtime 模块**

- 负责模型交互抽象、工具注册、执行控制和任务生命周期。
- 在执行前校验工具参数。
- 随着功能演进提供执行记录和故障处理能力。

**Business Agent 模块**

- 负责业务查询用例和业务专属工具。
- 不包含通用模型供应商的具体实现细节。

**Knowledge Assistant 模块**

- 负责文档导入、文本分块、检索和基于来源的回答。

**Report Studio 模块**

- 负责报表规格、规格校验、受控查询执行和报表元数据。

**Log Intelligence 模块**

- 负责日志检索、异常分析、调用链关联和排障证据。

这些职责用于明确逻辑归属，并不意味着每项职责都必须立即变成独立的 Maven 模块。

## 4. Dependency Rules / 模块依赖规则

### English

1. Dependencies must point toward clearly defined contracts and responsibilities.
2. Business modules must not depend on one another's internal implementations.
3. Shared code must not become a general-purpose dumping ground.
4. Circular dependencies between modules are prohibited.
5. Framework and infrastructure dependencies should be isolated where practical.
6. Application entry points may assemble modules but should not own their business rules.
7. Tests must verify important module behavior and dependency boundaries where feasible.

Cross-module communication should use explicit interfaces or documented application contracts.

### 简体中文

1. 依赖必须指向职责明确的契约和模块。
2. 业务模块不能依赖其他业务模块的内部实现。
3. 共享代码不能成为通用杂物区。
4. 禁止模块之间出现循环依赖。
5. 在可行的情况下，隔离框架和基础设施依赖。
6. 应用启动入口可以组装模块，但不应拥有这些模块的业务规则。
7. 在可行的情况下，通过测试验证重要模块行为和依赖边界。

跨模块通信应通过明确的接口或有文档说明的应用契约完成。

## 5. Maven Build Conventions / Maven 构建约定

### English

The root Maven project should provide:

- A consistent Java compiler configuration.
- Centralized dependency and plugin version management where appropriate.
- Consistent test execution conventions.
- Reproducible build commands.
- Clear separation between application modules and reusable modules.

The project must not introduce framework or library versions until their compatibility has been checked against the selected Java and Spring versions.

A successful build must be verified by actually running the corresponding Maven command.

### 简体中文

根 Maven 工程应提供：

- 统一的 Java 编译配置。
- 在适用范围内集中管理依赖和插件版本。
- 统一的测试执行约定。
- 可重复执行的构建命令。
- 应用模块与可复用模块之间的清晰区分。

在确认框架和库版本与选定的 Java、Spring 版本兼容之前，不应随意引入相关依赖。

构建是否成功，必须通过实际执行相应的 Maven 命令验证。

## 6. Initial Scope / 初期范围

### English

The first implementation should establish only the minimum structure needed to compile, test, and run a backend application.

The four business capabilities remain part of the long-term design, but their Maven modules should be introduced when their implementation begins or when a concrete dependency boundary justifies their creation.

The project should avoid creating empty modules solely to make the directory tree appear complete.

### 简体中文

第一阶段只建立能够完成编译、测试和启动后端应用所需的最小结构。

四项业务能力仍属于长期设计范围，但只有在开始实现相关功能，或者确实需要建立依赖边界时，才创建对应的 Maven 模块。

不能仅仅为了让目录树看起来完整，就创建没有实际职责的空模块。

## 7. Validation Checklist / 验收清单

### English

- The root Maven project can be built reproducibly.
- Module responsibilities and dependency directions are documented.
- Circular dependencies are avoided.
- Tests can run through documented commands.
- No unnecessary external infrastructure is required to compile the initial backend.
- No secrets, local IDE files, build outputs, or runtime data are accidentally committed.

### 简体中文

- 根 Maven 工程能够按照文档重复构建。
- 模块职责和依赖方向已有文档说明。
- 不存在循环依赖。
- 测试可以通过文档中说明的命令执行。
- 初始后端编译不依赖不必要的外部基础设施。
- 不会意外提交密钥、本地 IDE 文件、构建产物或运行数据。
