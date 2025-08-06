# 贡献指南规范

## 1. 目的

本规范旨在为项目内部和外部贡献者提供一套清晰、可遵循的指南，确保所有贡献活动能够高效、有序地进行，并符合项目的质量标准和协作原则。我们欢迎并感谢您的任何形式的贡献！

## 2. 贡献前须知

在开始贡献之前，请确保您已阅读并理解以下内容：

*   **行为准则**：请遵守项目的 [行为准则规范](../guides/team-collaboration.md#行为准则)。该规范的外部实现请参考 GitHub 仓库的 [CODE_OF_CONDUCT.md](https://github.com/origadmin/.github/blob/main/CODE_OF_CONDUCT.md)。
*   **开发规范**：请务必遵循项目的 [开发规范总纲](../development-standards.md)，包括编码规范、目录结构、提交规范等，以确保代码风格和质量的一致性。
*   **Issue 管理规范**：了解如何正确提交 Issue，请参考 [Issue 管理规范](../workflow-issue-management.md)。Issue 提交的外部实现请参考 GitHub 仓库的 [Issue 模板](https://github.com/origadmin/.github/tree/main/ISSUE_TEMPLATE)。

## 3. 如何贡献

我们鼓励通过以下方式进行贡献：

### 3.1 报告 Bug

如果您发现任何 Bug，请按照 [Issue 管理规范](../workflow-issue-management.md) 中的指引，提交一个 Bug Issue。请提供详细的复现步骤、环境信息和错误日志。建议使用 GitHub 仓库的 [Bug 报告模板](https://github.com/origadmin/.github/blob/main/ISSUE_TEMPLATE/bug_report.md)。

### 3.2 提交功能请求或改进建议

如果您有新的功能想法或对现有功能的改进建议，请按照 [Issue 管理规范](../workflow-issue-management.md) 中的指引，提交一个 Feature 或 Enhancement Issue。请详细描述您的想法和预期效果。建议使用 GitHub 仓库的 [功能请求模板](https://github.com/origadmin/.github/blob/main/ISSUE_TEMPLATE/feature_request.md)。

### 3.3 贡献代码

#### 3.3.1 准备工作

1.  **Fork 仓库**：首先，Fork 本项目的 GitHub/GitLab 仓库到您自己的账户。
2.  **克隆仓库**：将 Fork 后的仓库克隆到本地：
    ```bash
    git clone [您的 Fork 仓库地址]
    cd [项目目录]
    ```
3.  **添加上游仓库**：将原始仓库添加为上游仓库，以便同步最新代码：
    ```bash
    git remote add upstream [原始仓库地址]
    ```
4.  **创建新分支**：从 `main` 分支创建新的功能分支。分支命名应清晰，例如 `feature/your-feature-name` 或 `fix/bug-description`。
    ```bash
    git checkout main
    git pull upstream main
    git checkout -b feature/your-feature-name
    ```
5.  **配置开发环境**：按照 [开发环境配置](./development-environment.md) 文档的指引，确保您的本地开发环境已正确配置。

#### 3.3.2 开发流程

1.  **编写代码**：在您的功能分支上进行开发。请确保您的代码符合项目的 [开发规范总纲](../development-standards.md)。
2.  **编写测试**：为您的新功能或 Bug 修复编写相应的单元测试、集成测试或端到端测试，确保代码的正确性和稳定性。
3.  **运行测试**：在提交代码前，请确保所有测试通过。
    ```bash
    # 例如：
    npm test
    go test ./...
    ```
4.  **代码格式化与 Lint**：使用项目配置的工具（如 Prettier, ESLint, golangci-lint）对代码进行格式化和 Lint 检查。
    ```bash
    # 例如：
    npm run format
    npm run lint
    golangci-lint run ./...
    ```
5.  **提交代码**：按照 [代码提交规范](../workflow-commit.md) 编写清晰、有意义的提交信息。
    ```bash
    git add .
    git commit -m "feat: Add new feature"
    ```
6.  **同步上游代码**：在提交 Pull Request 之前，请确保您的分支与上游 `main` 分支保持同步，解决任何冲突。
    ```bash
    git checkout feature/your-feature-name
    git pull --rebase upstream main
    ```
7.  **推送分支**：将您的功能分支推送到您的 Fork 仓库。
    ```bash
    git push origin feature/your-feature-name
    ```

#### 3.3.3 提交 Pull Request (PR)

1.  **创建 PR**：在 GitHub/GitLab 页面上，从您的功能分支向原始仓库的 `main` 分支创建 Pull Request。建议使用 GitHub 仓库的 [Pull Request 模板](https://github.com/origadmin/.github/blob/main/PULL_REQUEST_TEMPLATE.md)。
2.  **填写 PR 描述**：请提供清晰、详细的 PR 描述，包括：
    *   **解决了什么问题/实现了什么功能**。
    *   **相关的 Issue 编号**（例如：`Closes #123` 或 `Fixes #456`）。
    *   **技术实现细节**（如果需要）。
    *   **如何测试**。
3.  **等待审查**：提交 PR 后，项目维护者会进行代码审查。请积极响应审查意见，并根据反馈进行修改。
4.  **合并**：一旦 PR 通过审查并所有检查通过，项目维护者会将其合并到 `main` 分支。

## 4. 代码审查

本项目遵循 [代码审查规范](../workflow-review.md)。所有提交到 `main` 分支的代码都必须经过至少一位项目维护者的审查。

## 5. 行为准则

我们致力于维护一个开放、友好和包容的社区。所有参与者都必须遵守 [行为准则规范](../guides/team-collaboration.md#行为准则)。

## 6. 疑问与帮助

如果您在贡献过程中遇到任何问题，请随时在 Issue 中提出，或通过 [团队协作](./team-collaboration.md) 中提到的方式寻求帮助。
