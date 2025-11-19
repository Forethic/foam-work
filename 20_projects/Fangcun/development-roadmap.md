# FangCun 项目开发路线图 (MVP 阶段 - 以“复式记账”为起点)

本文档旨在为 FangCun 项目的 MVP (Minimum Viable Product) 阶段提供一份详细的开发路线图。该路线图将任务拆解为具体的 SMART 小目标，并融入学习内容和 UI/UX 设计考量，以确保项目高效推进，并支持开发者在实践中学习成长。

## 1. 项目基础信息回顾

* **项目愿景**: 打造一个属于自己的全能软件生态，成为一个更好的人。
* **核心功能 (MVP)**: 复式记账模块。
* **核心技术栈**:
  * **后端**: ASP.NET Core Web API (C#)
  * **前端**: Avalonia UI (C# / XAML)
  * **数据库**: PostgreSQL
  * **ORM**: Entity Framework Core
* **开发工具**: JetBrains Rider (IDE), DataGrip (DB 管理), Postman/Insomnia (API 测试)。

## 2. 开发管理策略

* **文档即管理**: 所有重要的规划、决策、学习笔记都以 Markdown 文件的形式存在。
* **SMART 目标驱动**: 所有任务都拆解为具体、可衡量、可实现、相关性强的小目标。
* **学习与实践结合**: 针对即将开发的任务，有目的地学习相关技术知识，并立即付诸实践。
* **UI/UX 设计前置**: 在编码之前，完成相应的 UI/UX 设计，确保用户体验。
* **可视化进度**: 通过这份文档和 TODO List 实时追踪进度。

## 3. 开发路线图 (MVP 阶段)

### 阶段 0: UI/UX 设计与准备

* **目标**: 建立 FangCun 的基础设计语言和核心用户流程。
* **学习内容**: UI/UX 设计基础，设计系统概念，线框图与流程图绘制工具。
* **任务列表**:
  * **0.1 定义核心设计原则与设计系统**:
    * **目标**: 明确 FangCun 的品牌调性、核心设计原则。初步确定品牌颜色、字体、图标风格、通用组件样式等视觉规范。
    * **产出**: `20_projects/Fangcun/design-system.md` (或类似文档)。
  * **0.2 绘制用户流程图与核心功能线框图**:
    * **目标**: 为用户注册登录、账户管理、交易录入、交易列表等核心流程绘制用户流程图和线框图。
    * **产出**: `20_projects/Fangcun/user-flows-wireframes.md` (或 Figma/Axure 文件)。

### 阶段 1: 项目基础搭建与核心服务

* **目标**: 搭建 FangCun 项目的基础框架，并确保开发环境就绪。
* **学习内容**: .NET 基础，Rider/PostgreSQL/Avalonia UI 基础，Git。
* **任务列表**:
  * **1.1 开发环境准备**:
    * **目标**: 成功安装 JetBrains Rider 和最新 .NET SDK，并完成 Rider 的初始配置，确保 `dotnet --version` 命令正常输出。成功安装 PostgreSQL 数据库服务器，并安装 DataGrip。通过 DataGrip 连接到 PostgreSQL，创建 `fangcun_db` 数据库和 `fangcun_user` 用户。
    * **产出**: 就绪的开发环境。
    * **学习**: 熟悉 Rider 基础操作、快捷键，.NET SDK 安装与验证，PostgreSQL 基础概念，DataGrip 连接和基本查询操作。
  * **1.2 创建 .NET Solution 与后端/前端/共享库项目**:
    * **目标**: 在 Rider 中创建一个空的 .NET Solution，并添加 ASP.NET Core Web API 项目、Avalonia UI 项目和共享领域模型库项目，确保所有项目可以成功编译。
    * **产出**: 包含三个项目的可编译 Solution。
    * **学习**: .NET Solution 结构，ASP.NET Core/Avalonia UI 项目模板，项目引用。
  * **1.3 配置 EF Core 与初始数据库迁移**:
    * **目标**: 配置后端 API 项目集成 Entity Framework Core，配置 PostgreSQL 数据库连接字符串，并完成第一次数据库迁移，生成 `Users` 表。
    * **产出**: 成功的数据库迁移记录，PostgreSQL 中出现 `Users` 表。
    * **学习**: EF Core 基础（DBContext、Migration、Code-First）。
  * **1.4 配置日志与 DI**:
    * **目标**: 为后端 API 项目配置基本的日志系统（如 Serilog），并初始化依赖注入 (DI) 容器。
    * **产出**: 后端 API 项目具备基础日志输出和 DI 能力。
    * **学习**: ASP.NET Core 日志系统，依赖注入原理。

### 阶段 2: 用户身份认证与授权

* **目标**: 实现 FangCun 的用户注册、登录、权限管理和多端登录控制。
* **学习内容**: JWT 认证，RBAC 模型，Redis 基础。
* **任务列表**:
  * **2.1 实现用户注册、登录 API**:
    * **目标**: 完成用户注册和登录的后端 API，并通过 Postman 测试。
    * **产出**: 可用的注册登录 API。
    * **学习**: ASP.NET Core Identity/Authentication，密码哈希。
  * **2.2 集成 JWT 认证**:
    * **目标**: 在后端 API 中集成 JWT 认证，确保受保护的 API 只能被认证用户访问。
    * **产出**: 受 JWT 保护的 API。
    * **学习**: JWT 原理与实现。
  * **2.3 实现 RBAC 权限校验机制**:
    * **目标**: 在后端 API 中实现基于角色的权限校验，例如管理员和普通用户。
    * **产出**: 具备 RBAC 权限校验的 API。
    * **学习**: RBAC 模型，ASP.NET Core 授权策略。
  * **2.4 实现 UserDevices 管理 API (多端登录管理)**:
    * **目标**: 完成记录用户登录设备信息、管理会话的 API。
    * **产出**: 可管理多端登录的 API。
    * **学习**: Redis 基础，会话管理策略。

### 阶段 3: 核心记账模块 API (后端)

* **目标**: 实现复式记账的核心后端 API，包括账户、交易和 MDD 模型的 CRUD 操作。
* **学习内容**: MDD 模型在 EF Core 中的实现，PostgreSQL JSONB 操作，Beancount 语法解析。
* **任务列表**:
  * **3.1 实现 Accounts 的 CRUD API**:
    * **目标**: 完成账户的增删改查 API。
    * **产出**: 可用的账户管理 API。
    * **学习**: EF Core 实体映射，控制器开发。
  * **3.2 实现 Transactions 和 Postings 的 CRUD API**:
    * **目标**: 完成交易和分录的增删改查 API。
    * **产出**: 可用的交易管理 API。
    * **学习**: EF Core 复杂关系映射，事务处理。
  * **3.3 实现基于 Beancount 语法的导入 API**:
    * **目标**: 开发一个 API，能够接收 Beancount 文本，解析并将其转换为 `Transactions` 和 `Postings` 存储。
    * **产出**: Beancount 导入 API。
    * **学习**: C# 语法解析器开发基础，Beancount 语法规则。
  * **3.4 实现核心 MDD 模块的 CRUD API (辅助记账元数据)**:
    * **目标**: 完成 `ItemTypes`, `Properties`, `Items`, `Relations` 的 CRUD API，用于管理记账交易的元数据。
    * **产出**: 可用的 MDD 元数据管理 API。
    * **学习**: MDD 模型在 API 设计中的体现。

### 阶段 4: 前端基础与数据同步服务

* **目标**: 搭建 Avalonia UI 应用的基础结构，并实现与后端的数据同步机制。
* **学习内容**: Avalonia UI 基础，SQLite 客户端，CQRS 客户端实现，网络编程。
* **任务列表**:
  * **4.1 前端基础 API Client 与认证集成**:
    * **目标**: 实现 Avalonia UI 应用与后端 API 的基础通信服务，并集成用户登录认证。
    * **产出**: 登录界面，可调用后端 API。
    * **学习**: Avalonia UI 数据绑定，HttpClient 使用，JWT 客户端集成。
  * **4.2 本地 SQLite 数据库集成与初始化**:
    * **目标**: 在 Avalonia UI 应用中集成 SQLite，并初始化本地数据库，包括 `LocalItems` 和 `PendingCommands` 表。
    * **产出**: 可用的本地 SQLite 数据库。
    * **学习**: SQLite 基础，ORM Lite/EF Core for SQLite。
  * **4.3 离线同步服务实现**:
    * **目标**: 实现本地 `PendingCommands` 队列管理，网络状态监视器，同步触发器，以及与服务器的 Commands 上传和 Read Model 拉取机制。
    * **产出**: 具备离线操作和同步能力的客户端。
    * **学习**: 异步编程，网络编程，CQRS 客户端实践。

### 阶段 5: 复式记账 MVP 功能 (前端 UI)

* **目标**: 实现复式记账的核心 UI 界面，包括账户管理和交易录入。
* **学习内容**: Avalonia UI 高级特性，UI/UX 设计实践。
* **任务列表**:
  * **5.1 实现账户列表与管理 UI**:
    * **目标**: 完成账户的列表展示、创建、编辑、删除等 UI 功能。
    * **产出**: 可用的账户管理界面。
    * **学习**: Avalonia UI 控件库，MVVM 模式在 Avalonia 中的实践。
  * **5.2 实现交易列表与详情 UI**:
    * **目标**: 完成交易的列表展示、详情查看、基于 MDD 元数据的筛选功能。
    * **产出**: 可用的交易列表和详情界面。
    * **学习**: Avalonia UI 数据网格，数据过滤。
  * **5.3 实现 Beancount 记账文本的输入与预览 UI**:
    * **目标**: 完成 Beancount 记账文本的输入界面，并集成实时预览功能。
    * **产出**: 具备文本输入和预览的记账界面。
    * **学习**: Avalonia UI 文本编辑控件，与后端解析 API 交互。

### 阶段 6: 桌面端智能编辑器 (MVP)

* **目标**: 在桌面端实现 Beancount 记账的智能编辑器，提升录入效率。
* **学习内容**: AvalonEdit 集成与定制，C# 语法解析器开发。
* **任务列表**:
  * **6.1 集成 AvalonEdit 组件到 Avalonia UI**:
    * **目标**: 成功在 Avalonia UI 窗口中嵌入 AvalonEdit 文本编辑器。
    * **产出**: 可用 AvalonEdit 的 Avalonia UI 应用。
    * **学习**: AvalonEdit 文档，Avalonia UI 宿主控件。
  * **6.2 实现 Beancount 语法的语法高亮和解析逻辑**:
    * **目标**: 为 AvalonEdit 实现 Beancount 语法的自定义高亮，并在后端或共享库中完成 Beancount 语法的解析逻辑。
    * **产出**: 具备语法高亮和解析能力的编辑器。
    * **学习**: 语法解析器原理，Beancount 语法树结构。
  * **6.3 实现记账文本的实时预览功能**:
    * **目标**: 将解析后的 Beancount 交易数据，实时渲染为结构化的预览视图。
    * **产出**: 记账文本实时预览功能。
    * **学习**: Avalonia UI 数据模板，数据转换。

### 阶段 7: 核心功能端到端 (E2E) 测试

* **目标**: 确保 MVP 阶段的核心功能能够稳定、正确地运行。
* **学习内容**: E2E 测试框架 (如 Playwright for .NET)。
* **任务列表**:
  * **7.1 为用户注册登录流程编写 E2E 测试**:
    * **目标**: 编写并运行 E2E 测试，验证用户注册、登录、登出流程的正确性。
    * **产出**: 通过的 E2E 测试报告。
    * **学习**: E2E 测试框架使用。
  * **7.2 为记账核心流程编写 E2E 测试**:
    * **目标**: 编写并运行 E2E 测试，验证记账数据的创建、编辑、删除、离线同步和 Beancount 导入的正确性。
    * **产出**: 通过的 E2E 测试报告。
    * **学习**: 如何测试复杂 UI 和异步同步逻辑。

## 4. 后续阶段 (MVP 之后)

* **其他核心功能**: 闪念笔记、日程安排、习惯养成、学习/考试管理、健康管理等。
* **报表与数据分析**: 强大的财务报表和可视化工具。
* **部署与运维**: 准备生产环境部署脚本，配置 CI/CD 流程，监控与告警系统。
* **性能优化**: 针对读写分离、数据库分区等进行深入优化。
* **用户自定义**: 实现用户自定义 `ItemType` 和 `Property` 的功能。
