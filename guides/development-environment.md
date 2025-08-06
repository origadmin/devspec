# 开发环境配置

## 1. 目的

本规范旨在提供统一的开发环境配置指南，确保所有开发人员能够快速、一致地搭建开发环境，减少因环境差异导致的问题，提高开发效率。

## 2. 操作系统要求

推荐使用以下操作系统进行开发：

*   **Windows**：Windows 10/11 (推荐使用 WSL2)
*   **macOS**：最新稳定版本
*   **Linux**：Ubuntu 20.04+ 或其他主流发行版

## 3. 核心工具与依赖

请确保您的开发环境中安装了以下核心工具和依赖，并保持在推荐版本：

### 3.1 版本控制

*   **Git**：`2.30.0` 及以上版本
    *   [Git 官方下载](https://git-scm.com/downloads)

### 3.2 编程语言与运行时

*   **Go**：`1.23` 及以上版本
    *   [Go 官方下载](https://golang.org/dl/)
    *   **环境变量配置**：确保 `GOPATH` 和 `GOBIN` 配置正确。
*   **Node.js**：`18.x` 及以上版本 (推荐使用 nvm 或 fnm 管理)
    *   [Node.js 官方下载](https://nodejs.org/en/download/)
    *   [nvm (Node Version Manager)](https://github.com/nvm-sh/nvm)
    *   [fnm (Fast Node Manager)](https://github.com/Schniz/fnm)
*   **Python**：`3.9` 及以上版本 (如果项目涉及 Python)
    *   [Python 官方下载](https://www.python.org/downloads/)

### 3.3 包管理工具

*   **Go Modules**：Go 1.11+ 自带
*   **npm / Yarn / pnpm / Bun**：根据前端项目实际使用的包管理器选择安装
    *   [npm](https://www.npmjs.com/get-npm)
    *   [Yarn](https://yarnpkg.com/)
    *   [pnpm](https://pnpm.io/)
    *   [Bun](https://bun.sh/)

### 3.4 数据库

*   **PostgreSQL**：`12.x` 及以上版本 (推荐使用 Docker 运行)
    *   [PostgreSQL 官方下载](https://www.postgresql.org/download/)
    *   [Docker Desktop](https://www.docker.com/products/docker-desktop)
*   **其他数据库**：[如果项目使用其他数据库，请在此处列出并提供安装指南]

### 3.5 容器化

*   **Docker Desktop**：最新稳定版本
    *   [Docker Desktop 官方下载](https://www.docker.com/products/docker-desktop)

## 4. 开发工具

### 4.1 集成开发环境 (IDE)

*   **Visual Studio Code (VS Code)**：推荐使用，并安装以下推荐插件：
    *   [Go](https://marketplace.visualstudio.com/items?itemName=golang.go)
    *   [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
    *   [Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
    *   [Tailwind CSS IntelliSense](https://marketplace.visualstudio.com/items?itemName=bradlc.vscode-tailwindcss)
    *   [Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)
    *   [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)
*   **GoLand**：如果主要进行 Go 开发，推荐使用。

### 4.2 其他辅助工具

*   **Postman / Insomnia**：API 测试工具
*   **DBeaver / DataGrip**：数据库管理工具
*   **GitKraken / SourceTree**：Git GUI 工具 (可选)

## 5. 环境配置步骤

1.  **安装 Git**：按照官方指南安装 Git。
2.  **安装 Go**：按照官方指南安装 Go，并配置好环境变量。
3.  **安装 Node.js**：推荐使用 nvm 或 fnm 安装 Node.js。
4.  **安装 Docker Desktop**：用于运行数据库、消息队列等服务。
5.  **克隆项目代码**：
    ```bash
    git clone [项目仓库地址]
    cd [项目目录]
    ```
6.  **安装后端依赖**：
    ```bash
    go mod tidy
    ```
7.  **安装前端依赖**：
    ```bash
    # 根据项目实际使用的包管理器选择
    npm install
    # 或 yarn install
    # 或 pnpm install
    # 或 bun install
    ```
8.  **启动数据库/其他服务**：
    ```bash
    # 例如，使用 Docker Compose 启动数据库
    docker-compose up -d
    ```
9.  **运行项目**：
    ```bash
    # 后端
    go run main.go
    # 前端
    npm run dev
    ```

## 6. 常见问题与故障排除

*   **问题**：[常见问题描述]
    *   **解决方案**：[解决方案]
*   **问题**：[另一个常见问题描述]
    *   **解决方案**：[解决方案]

## 7. Dev Container (可选)

本项目支持 Dev Container 配置，推荐使用以获得一致的开发环境。详情请参考 [Dev Container 文档链接]。
