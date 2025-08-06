# 常见问题及解决方案 (FAQ)

本文档收集了项目开发过程中常见的技术问题、环境问题以及其他疑问，并提供了相应的解决方案。在提问之前，请先查阅本文档。

## 1. 开发环境问题

### 1.1 问题：Go 模块依赖下载失败或速度慢。

*   **描述**：运行 `go mod tidy` 或 `go build` 时，模块下载卡住或报错。
*   **解决方案**：
    *   **配置 Go Proxy**：设置 `GOPROXY` 环境变量为国内镜像站，例如：
        ```bash
        go env -w GOPROXY=https://goproxy.cn,direct
        ```
    *   **检查网络连接**：确保网络连接正常，没有防火墙或代理问题。
    *   **清理缓存**：尝试清理 Go 模块缓存：`go clean -modcache`。

### 1.2 问题：前端依赖安装失败或速度慢。

*   **描述**：运行 `npm install` 或 `yarn install` 时，依赖下载卡住或报错。
*   **解决方案**：
    *   **配置 npm/Yarn 镜像**：设置淘宝镜像源：
        ```bash
        npm config set registry https://registry.npmmirror.com
        # 或 yarn config set registry https://registry.npmmirror.com
        ```
    *   **清理缓存**：尝试清理 npm/Yarn 缓存：`npm cache clean --force` 或 `yarn cache clean`。
    *   **检查 Node.js 版本**：确保 Node.js 版本符合项目要求。

### 1.3 问题：Docker 容器无法启动或端口冲突。

*   **描述**：`docker-compose up` 报错，提示端口已被占用或容器无法启动。
*   **解决方案**：
    *   **检查端口占用**：使用 `netstat -ano | findstr :<端口号>` (Windows) 或 `lsof -i :<端口号>` (Linux/macOS) 查找占用端口的进程，并终止它。
    *   **重启 Docker 服务**：尝试重启 Docker Desktop 或 Docker Daemon。
    *   **检查 Docker Compose 文件**：确认 `docker-compose.yml` 中的配置是否正确。

## 2. 代码相关问题

### 2.1 问题：Go 项目编译报错，提示包找不到。

*   **描述**：`go build` 报错，提示 `package xxx is not in GOROOT or GOPATH`。
*   **解决方案**：
    *   **运行 `go mod tidy`**：确保所有依赖都已正确下载和管理。
    *   **检查 `go.mod` 文件**：确认 `go.mod` 文件中是否正确声明了模块路径和依赖。
    *   **检查导入路径**：确保代码中的 `import` 路径与实际模块路径一致。

### 2.2 问题：前端项目 Lint 报错或格式化不通过。

*   **描述**：运行 `npm run lint` 或 `npm run format` 报错。
*   **解决方案**：
    *   **运行修复命令**：尝试运行 `npm run lint:fix` 或 `npm run format` 来自动修复问题。
    *   **检查配置文件**：确认 `.eslintrc.js`, `.prettierrc`, `.stylelintrc.json` 等配置文件是否正确。
    *   **IDE 插件**：确保您的 IDE 安装了相应的 Lint 和格式化插件，并已启用。

### 2.3 问题：数据库连接失败。

*   **描述**：应用程序无法连接到数据库。
*   **解决方案**：
    *   **检查数据库服务**：确保数据库服务正在运行。
    *   **检查连接字符串**：确认应用程序中的数据库连接字符串（主机、端口、用户名、密码、数据库名）是否正确。
    *   **检查防火墙**：确认防火墙是否阻止了应用程序与数据库之间的连接。
    *   **检查网络**：如果数据库在远程服务器上，检查网络连通性。

## 3. 部署相关问题

### 3.1 问题：CI/CD 流水线失败。

*   **描述**：GitHub Actions 或其他 CI/CD 工具的流水线执行失败。
*   **解决方案**：
    *   **查看日志**：仔细检查流水线日志，定位具体的错误信息。
    *   **本地复现**：尝试在本地复现流水线中的步骤，找出问题所在。
    *   **检查配置文件**：确认 `.github/workflows/*.yml` 或其他 CI/CD 配置文件是否正确。

### 3.2 问题：服务部署后无法访问。

*   **描述**：服务部署到 Kubernetes 或其他环境后，无法通过浏览器或 API 访问。
*   **解决方案**：
    *   **检查服务状态**：确认 Kubernetes Pods, Deployments, Services 等资源是否正常运行。
    *   **检查端口暴露**：确认服务端口是否正确暴露，并且防火墙没有阻止访问。
    *   **检查 Ingress/路由**：如果使用了 Ingress 或其他路由配置，确认其规则是否正确。
    *   **查看日志**：检查服务日志，看是否有启动错误或运行时错误。

## 4. 其他问题

### 4.1 问题：如何提交 Bug 或功能请求？

*   **解决方案**：请参考 [Issue 管理规范](workflow-issue-management.md) 文档。

### 4.2 问题：如何贡献代码？

*   **解决方案**：请参考 [贡献指南](contribution-guide.md) 文档。

### 4.3 问题：项目架构是怎样的？

*   **解决方案**：请参考 [项目概述](project-overview.md) 文档。

## 5. 寻求帮助

如果以上解决方案无法解决您的问题，请通过以下方式寻求帮助：

*   **提交 Issue**：在项目 Issue 跟踪系统中提交一个 Issue，详细描述您的问题。
*   **团队协作**：通过 [团队协作](team-collaboration.md) 中提到的沟通渠道联系团队成员。
