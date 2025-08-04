# 代码发布规范 (Code Release Guidelines)

## 目的

本规范旨在定义一个清晰、一致且健壮的发布流程，确保每个版本的发布都是可追溯、稳定且高效的，并包含明确的回滚预案。

## 版本号规范

项目严格遵循 **[语义化版本 2.0.0 (Semantic Versioning)](https://semver.org/lang/zh-CN/)** 规范。版本号格式为 `v<主版本号>.<次版本号>.<修订号>`，例如 `v1.2.3`。

-   **主版本号 (MAJOR)**: 当你做了不兼容的 API 修改。
-   **次版本号 (MINOR)**: 当你做了向下兼容的功能性新增。这对应于 `feat` 类型的提交。
-   **修订号 (PATCH)**: 当你做了向下兼容的问题修正。这对应于 `fix` 类型的提交。

## 发布流程

#### 步骤 1: 创建发布分支 (`release/vX.Y.Z`)

当 `develop` 分支已经集成了所有计划包含在新版本中的功能和修复后，从 `develop` 分支创建一个新的 `release` 分支。

```bash
# 假设我们将要发布 1.2.0 版本
git checkout develop
git pull origin develop
git checkout -b release/v1.2.0
```

#### 步骤 2: 在发布分支上完成最终准备

在 `release` 分支上，进行发布前的最后准备工作：

-   **最终测试**: 进行全面的回归测试和集成测试。
-   **文档更新**: 更新 `README.md`、API 文档等所有与本次发布相关的文档。
-   **版本号更新**: 在项目代码中（如果需要）更新版本号常量。
-   **生成更新日志 (Changelog)**:

    ```bash
    # 推荐使用工具根据 commit message 自动生成 CHANGELOG.md
    # npm install -g conventional-changelog-cli
    conventional-changelog -p angular -i CHANGELOG.md -s
    git add CHANGELOG.md
    git commit -m "docs: update changelog for v1.2.0"
    ```

#### 步骤 3: 触发发布 (决策点)

当 `release` 分支经过测试，被认为是“可发布”状态后，将其合并到 `main` 分支。这一步可以是手动的，也可以是自动的。

-   **手动发布 (推荐用于关键版本)**:
    1.  由拥有权限的开发负责人（Lead）审查 `release` 分支。
    2.  手动执行 `git merge` 将 `release` 分支合并到 `main`。
    3.  手动在 `main` 分支上打上版本标签 (`git tag`) 并推送到远程。

-   **自动发布**:
    1.  可以配置 CI/CD 流水线，当 `release` 分支被合并到 `develop` 分支后，自动触发一个“准备发布”的工作流。
    2.  该工作流成功后，可以设置一个需要手动点击“批准”的步骤，来触发后续到 `main` 的合并和打标签操作。

#### 步骤 4: 合并回 `develop` 并清理

发布完成后，将 `release` 分支也合并回 `develop`，以确保 `develop` 分支同步了所有发布前的最终修改，然后删除临时的 `release` 分支。

## 部署与监控

-   **自动化部署**: CI/CD 系统应配置为**监听**推送到仓库的新版本标签 (`v*.*.*`)。一旦检测到新标签，自动触发部署流水线，将该版本的代码部署到相应的环境（测试、预发、生产）。
-   **健康检查**: 部署完成后，自动化脚本必须立即执行一系列健康检查，例如：
    -   检查服务是否成功启动。
    -   调用核心 API 端点，确认返回状态码正常。
    -   监控短时间内（如 5 分钟内）的错误率和延迟，确保没有异常飙升。
-   **部署失败**: 如果上述任何检查失败，则立即判定为“上线失败”，并触发下面的回滚预案。

## 回滚预案 (Rollback Plan)

当一次上线被判定为失败时，必须立即执行回滚操作以恢复服务的稳定性。

### 1. 自动回滚流程

理想情况下，回滚应由自动化脚本完成。CI/CD 平台应提供一个“一键回滚”的按钮，或在健康检查失败时自动触发。该脚本执行以下操作：

1.  **识别失败版本和前一成功版本**: 脚本需要知道当前失败的版本标签（如 `v1.2.0`）和上一个成功部署的版本标签（如 `v1.1.5`）。
2.  **重新部署旧版本**: 立即使用上一个成功版本的构建产物（Docker 镜像等）重新进行部署。
3.  **发送警报**: 向开发团队和运维团队的指定渠道（如 Slack, DingTalk）发送紧急警报，通知回滚已发生，并附上相关日志的链接。
4.  **自动创建 Issue**:
    -   通过 API 调用项目管理平台（如 GitHub/GitLab），自动创建一个新的 Issue。
    -   **Issue 标题**: 应包含关键信息，例如 `[Production Rollback] Deployment of v1.2.0 failed`。
    -   **Issue 内容 (Body)**: 应自动填充以下信息：
        -   失败的版本号 (`v1.2.0`)
        -   回滚到的版本号 (`v1.1.5`)
        -   触发回滚的时间
        -   指向失败的 CI/CD 流水线日志的链接
        -   相关的 Commit SHA 或 Merge Request 链接
    -   **Issue 标签 (Labels)**: 自动为该 Issue 打上 `bug`, `production-issue`, `high-priority` 等预设标签。
    -   **指派负责人 (Assignee)**: 自动将该 Issue 指派给当前发布周期的负责人或团队主管。

### 2. 手动回滚流程 (代码库层面)

在服务已通过重新部署旧版本恢复后，需要对代码库进行同步，以反映这次失败的发布。

1.  **Revert 合并提交**: 在 `main` 分支上，找到导致失败发布的那个合并提交（Merge Commit），并使用 `git revert` 将其撤销。

    ```bash
    # 假设 abcdef 是将 release/v1.2.0 合并到 main 的 commit hash
    git checkout main
    git revert -m 1 abcdef
    ```

2.  **创建新的修复版本号**: 回滚本身也是一次变更，需要一个新的版本号。

    ```bash
    # 创建一个新的修订版本号来标记这次回滚
    git tag -a v1.2.1 -m "Revert to v1.1.5 due to deployment failure of v1.2.0"
    ```

3.  **推送变更**: 将 `main` 分支和新的标签推送到远程。

    ```bash
    git push origin main
    git push origin v1.2.1
    ```

### 3. 事后处理 (Post-mortem)

-   在**自动创建的 Issue** 中进行问题的跟踪、讨论和根本原因分析 (Root Cause Analysis)。
-   所有相关的修复工作都应链接到这个 Issue，直到问题最终关闭。