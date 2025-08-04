# 持续集成与部署 (CI/CD)

## 1. 目的

本规范旨在定义项目的持续集成 (CI) 和持续部署 (CD) 流程，确保代码变更能够快速、自动化地进行构建、测试和部署，从而提高开发效率、代码质量和发布可靠性。

## 2. CI/CD 工具

本项目主要使用以下 CI/CD 工具：

*   **GitHub Actions**：用于自动化工作流的执行。
*   **GoReleaser**：用于 Go 项目的自动化发布。
*   **Docker**：用于构建和管理容器镜像。
*   **Kubernetes**：用于容器化应用的部署和管理。

## 3. 持续集成 (CI) 流程

持续集成流程在每次代码提交到版本控制系统时触发，主要包括以下步骤：

1.  **代码拉取**：从 Git 仓库拉取最新代码。
2.  **依赖安装**：安装项目所需的各种依赖（Go Modules, npm 包等）。
3.  **代码构建**：编译项目代码，生成可执行文件或打包前端资源。
    *   **Go 项目**：`go build ./...`
    *   **前端项目**：`npm run build` 或 `rsbuild build`
4.  **代码质量检查**：
    *   **Linting**：使用 `golangci-lint` (Go) 和 `ESLint`, `Stylelint` (前端) 进行代码风格和潜在问题的检查。
    *   **格式化检查**：确保代码符合 Prettier 等格式化工具的规范。
5.  **单元测试**：运行所有单元测试，确保代码逻辑的正确性。
    *   **Go 项目**：`go test ./...`
    *   **前端项目**：`npm test` 或 `vitest`
6.  **集成测试**：运行集成测试，验证不同模块之间的交互是否正常。
7.  **安全扫描**：对代码依赖和镜像进行安全漏洞扫描 (可选)。
8.  **生成报告**：生成测试报告、代码覆盖率报告等。

### 3.1 GitHub Actions 配置示例

以下是一个简化的 GitHub Actions CI 工作流示例 (`.github/workflows/ci.yml`)：

```yaml
name: CI

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Go
        uses: actions/setup-go@v4
        with:
          go-version: '1.23'

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install Go dependencies
        run: go mod tidy

      - name: Run Go tests
        run: go test ./...

      - name: Install Frontend dependencies
        run: npm install

      - name: Run Frontend tests
        run: npm test

      - name: Build Frontend
        run: npm run build

      # ... 其他 Linting, 格式化检查步骤 ...
```

## 4. 持续部署 (CD) 流程

持续部署流程在 CI 成功通过后触发，主要包括以下步骤：

1.  **镜像构建**：根据最新代码构建 Docker 镜像，并打上版本标签。
2.  **镜像推送**：将构建好的 Docker 镜像推送到容器镜像仓库（例如 Docker Hub, Harbor）。
3.  **版本发布**：
    *   **Go 项目**：使用 GoReleaser 自动化生成发布版本、二进制文件、Docker 镜像并发布到 GitHub Releases。
    *   **前端项目**：将构建产物部署到静态文件服务器或 CDN。
4.  **环境部署**：将新的 Docker 镜像部署到开发、测试或生产环境的 Kubernetes 集群。
    *   更新 Kubernetes Deployment 配置，触发滚动更新。
5.  **健康检查**：部署后进行自动化健康检查，确保服务正常运行。
6.  **通知**：部署结果通知相关人员或系统。

### 4.1 GoReleaser 配置示例

GoReleaser 的配置通常在 `.goreleaser.yaml` 文件中，用于自动化 Go 项目的发布流程。

### 4.2 Kubernetes 部署

项目使用 Kubernetes 进行容器化应用的部署和管理。部署配置通常存储在 `deploy/kubernetes/` 目录下，包括 Deployment, Service, Ingress 等 YAML 文件。

## 5. 发布策略

*   **版本号**：本项目采用统一版本号策略，版本号基于 Git Tag (例如 `v1.0.0`)。
*   **发布触发**：
    *   **开发环境**：每次 `main` 分支合并都会触发 CI/CD 流程，自动部署到开发环境。
    *   **测试环境**：通常由特定分支（例如 `release/vX.Y`）或手动触发部署到测试环境。
    *   **生产环境**：通过创建 Git Tag (例如 `v1.0.0`) 触发 GoReleaser 自动化发布，并手动或自动化部署到生产环境。

## 6. 监控与告警

部署后，通过集成监控系统（如 Prometheus, Grafana）和告警系统（如 Alertmanager）对应用进行实时监控，确保服务的可用性和性能。一旦出现异常，及时触发告警。

## 7. 回滚策略

如果新版本部署后出现严重问题，可以通过以下方式进行回滚：

*   **Kubernetes 回滚**：使用 `kubectl rollout undo` 命令回滚到上一个稳定的版本。
*   **版本回退**：回退 Git Tag 或分支到上一个稳定版本，并重新触发部署流程。