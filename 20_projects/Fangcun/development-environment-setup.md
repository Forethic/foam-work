# FangCun 项目开发环境安装与配置指南

本文档旨在指导开发者完成 FangCun 项目所需的开发工具安装和初始配置，确保开发环境的顺利搭建。

## 1. 操作系统要求

* **Windows**: 推荐 Windows 10/11
* **macOS**: 推荐最新版本
* **Linux**: 推荐主流发行版 (如 Ubuntu)

## 2. 核心开发工具

### 2.1 JetBrains Rider (IDE)

* **用途**: 主要集成开发环境，用于编写 C# 代码 (后端 ASP.NET Core, 前端 Avalonia UI)。
* **安装步骤**:
    1. 访问 JetBrains 官方网站: [https://www.jetbrains.com/rider/download/](https://www.jetbrains.com/rider/download/)
    2. 下载适用于您操作系统的 Rider 安装包。
    3. 按照安装向导的指示完成安装。
    4. **初始配置**:
        * 首次启动 Rider，根据向导进行基本设置（主题、键盘映射等）。
        * 安装必要的插件：Rider 通常会自动检测并推荐安装 .NET 相关插件。对于 Avalonia UI 开发，可能需要手动搜索并安装 "Avalonia XAML Styler" 或其他 Avalonia UI 辅助插件。

### 2.2 .NET SDK

* **用途**: 开发 .NET 应用程序所需的运行时和开发工具包。
* **安装步骤**:
    1. 访问 .NET 官方网站: [https://dotnet.microsoft.com/download](https://dotnet.microsoft.com/download)
    2. 下载最新稳定版 `.NET SDK` (例如 .NET 8 SDK)。
    3. 按照安装向导的指示完成安装。
    4. **验证安装**: 打开终端 (Command Prompt / PowerShell / Terminal)，运行 `dotnet --version`。如果显示版本号，则表示安装成功。

### 2.3 PostgreSQL (数据库服务器)

* **用途**: 存储 FangCun 项目的所有核心业务数据。
* **安装步骤**:
    1. 访问 PostgreSQL 官方网站: [https://www.postgresql.org/download/](https://www.postgresql.org/download/)
    2. 下载适用于您操作系统的安装包 (推荐使用图形化安装程序，如 EDB Postgres)。
    3. 按照安装向导的指示完成安装。在安装过程中，请记住您设置的**管理员密码**和**端口号** (默认为 5432)。
    4. **初始配置**:
        * 安装完成后，PostgreSQL 服务通常会自动启动。
        * 创建新的数据库用户和数据库（例如，用户名为 `fangcun_user`，密码 `your_password`，数据库名为 `fangcun_db`）。您可以使用 `psql` 命令行工具或 pgAdmin 来完成。

### 2.4 PostgreSQL 管理工具

* **用途**: 可视化管理 PostgreSQL 数据库，执行 SQL 查询，查看数据。
* **推荐**:
  * **pgAdmin**: 官方推荐的免费工具。
        1. 访问 pgAdmin 官方网站: [https://www.pgadmin.org/download/](https://www.pgadmin.org/download/)
        2. 下载并安装。
        3. 启动 pgAdmin，连接到您本地的 PostgreSQL 服务器 (使用您在安装 PostgreSQL 时设置的管理员密码)。
  * **JetBrains DataGrip**: 付费但功能强大的通用数据库 IDE。如果您购买了 JetBrains All Products Pack，则已包含。
        1. 访问 JetBrains 官方网站: [https://www.jetbrains.com/datagrip/download/](https://www.jetbrains.com/datagrip/download/)
        2. 下载并安装。
        3. 启动 DataGrip，配置新的 PostgreSQL 数据源，连接到您的 `fangcun_db`。

### 2.5 Postman / Insomnia (API 测试工具)

* **用途**: 测试和调试后端 ASP.NET Core Web API。
* **安装步骤**:
    1. **Postman**: [https://www.postman.com/downloads/](https://www.postman.com/downloads/)
    2. **Insomnia**: [https://insomnia.rest/download](https://insomnia.rest/download)
    3. 选择您偏好的工具并安装。

## 3. 前端特定配置 (Avalonia UI)

* **Avalonia UI 模板**: Rider 通常会内置 Avalonia UI 的项目模板。如果没有，您可能需要通过 .NET CLI 安装：
    `dotnet new install Avalonia.Templates`
* **Avalonia UI 设计器**: Rider 提供了 Avalonia UI 的 XAML 设计器，可以在编写 XAML 时进行实时预览。

## 4. 验证环境

完成以上所有工具的安装和配置后，您应该能够：

1. 在 Rider 中创建新的 .NET Solution。
2. 在 Solution 中添加 ASP.NET Core Web API 项目和 Avalonia UI 项目。
3. 配置数据库连接，并能够通过 EF Core 进行数据库迁移。
4. 通过 Postman/Insomnia 访问和测试 API。
5. 在 Rider 中启动并运行 Avalonia UI 应用。

这份指南旨在提供一个起点。在实际开发过程中，您可能会根据需要安装其他辅助工具或插件。
