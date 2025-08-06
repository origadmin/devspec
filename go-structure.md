# 目录结构

**注意：** 本文档描述的是一个标准的、独立的服务型应用推荐的目录结构。对于框架或库类型的项目，其目录结构可能遵循不同的、更适合其库特性的组织方式（例如，将核心代码平铺在根目录或特定的模块目录下）。

项目的目录结构通常也是门面，内行人通过目录结构基本就能看出开发者是否有经验。

Go 官网并没有给出一个目录结构的标准模板，但是 golang-standards 倒是给出了一个，目录结构如下：

```
├── api             # 外部 API 定义 (e.g., OpenAPI, Protobuf)
├── assets          # 静态资源 (e.g., 图片, CSS, JS)
├── build           # 打包和持续集成相关文件
│   ├── ci          # 持续集成配置和脚本
│   └── package     # 打包配置和脚本 (e.g., Docker, deb, rpm)
├── cmd             # 主应用程序入口 (可执行文件)
│   └── <app_name>  # 每个子目录对应一个可执行应用
├── configs         # 配置文件模板或默认配置
├── deployments     # 部署配置和模板 (e.g., Kubernetes, Helm, Terraform)
├── docs            # 项目文档 (设计, 用户手册等)
├── examples        # 应用程序或公共库的示例程序
├── githooks        # Git 钩子脚本
├── init            # 系统初始化和进程管理配置 (e.g., systemd, supervisord)
├── internal        # 私有应用程序代码库 (不希望被外部导入)
│   ├── app         # 应用程序特定的内部代码
│   │   └── <app_name> # 应用程序内部逻辑
│   └── pkg         # 应用程序共享的内部库
│       └── <private_lib> # 私有库代码
├── pkg             # 外部应用程序可以使用的公共库代码
│   └── <public_lib> # 公共库代码
├── scripts         # 各种构建, 安装, 分析等操作的脚本
├── test            # 外部测试应用程序和测试数据
├── third_party     # 外部辅助工具, fork 的代码和其他第三方工具
├── tools           # 此项目的支持工具 (可导入 internal/pkg 代码)
├── vendor          # 应用程序的依赖关系 (Go Modules 缓存)
├── web             # Web 应用程序特定组件
│   ├── app         # Web 应用逻辑
│   ├── static      # 静态 Web 资源
│   └── template    # 服务器端模板
├── website         # 项目网站数据
├── .gitignore
├── LICENSE.md
├── Makefile
├── README.md
└── go.mod
```

## Go 目录

### cmd

当前项目的可执行文件。`cmd` 目录下的每一个子目录名称都应该匹配可执行文件。例如，如果您的项目是一个 gRPC 服务，在
`/cmd/your_app/main.go` 中就包含了启动服务进程的代码，编译后生成的可执行文件就是 `your_app`。

**最佳实践**：不要在 `/cmd` 目录中放置过多的代码。您应该将公共代码放置到 `/pkg` 中，将私有代码放置到 `/internal` 中，并在
`/cmd` 中引入这些包，以保证 `main` 函数中的代码尽可能简单和精炼。

### internal

私有的应用程序代码库。这些是不希望被其他项目导入的代码。请注意：这种模式是 Go 编译器强制执行的，即只有当前模块内部的代码才能导入
`internal` 目录下的包。在项目的目录树中的任意位置都可以有 `internal` 目录，而不仅仅是在顶级目录中。

**最佳实践**：可以在内部代码包中添加一些额外的结构，来分隔共享和非共享的内部代码。例如：应用程序代码放在 `/internal/app`
目录（如，`internal/app/your_app`），而应用程序的共享代码放在 `/internal/pkg` 目录（如，`internal/pkg/your_private_lib`）中。

### pkg

外部应用程序可以使用的库代码（如，`/pkg/your_public_lib`）。其他项目将会导入这些库来保证项目可以正常运行，所以在将代码放在这里前，一定要慎重考虑。请注意，
`internal` 目录是一个更好的选择来确保项目私有代码不会被其他人导入，因为这是 Go 强制执行的。使用 `/pkg`
目录来明确表示代码可以被其他人安全地导入仍然是一个好方式。

**最佳实践**：当根目录包含大量非 Go 组件和目录时，`/pkg` 也是一种将 Go 代码分组到一个位置的方法，从而使运行各种 Go
工具更加容易。如果项目确实很小并且嵌套的层次并不会带来多少价值，那么就不要使用它。但当项目变得很大，并且根目录中包含的内容相当繁杂时，可以考虑使用
`/pkg`。

### vendor

应用程序的依赖关系（通过 `go mod vendor` 命令生成）。`vendor` 目录包含了项目所有依赖的副本。当执行 `go build` 进行编译时，如果存在
`vendor` 目录，Go 编译器会优先从 `vendor` 目录中查找依赖。

**最佳实践**：在 Go Modules 时代，通常不需要提交 `vendor` 目录到版本控制系统。Go Modules 默认使用模块代理（如
`proxy.golang.org`）来管理依赖。只有在特定场景下（如离线构建、强制使用特定依赖版本等）才需要使用 `go mod vendor`。

## 服务端应用程序目录

### api

项目对外提供和依赖的 API 文件。这包括但不限于 OpenAPI/Swagger specs, JSON schema 文件, protocol 定义文件（如 gRPC 的
`.proto` 文件）等。这些文件定义了服务之间的契约。

**最佳实践**：将所有 API 定义集中存放于此，便于管理和版本控制。对于 gRPC 服务，`.proto` 文件及其生成的 Go 代码通常会放在此目录下。

## Web 应用程序目录

### web

Web 应用程序特定的组件。这通常包括静态 Web 资源（如 HTML, CSS, JavaScript 文件）、服务器端模板以及单页应用（Single-Page
Application, SPA）的构建产物。

**最佳实践**：将所有与 Web 界面直接相关的资源和代码组织在此目录下，便于前端和后端开发的分离与协作。

## 通用应用程序目录

### build

打包和持续集成所需的文件。此目录通常包含用于自动化构建、测试和部署的脚本和配置。

- `build/ci`：存放持续集成的配置和脚本（例如 Jenkinsfile, GitHub Actions workflows）。
- `build/package`：存放用于打包应用程序的配置和脚本，例如 Dockerfile、AMI 构建脚本、系统包（deb、rpm）配置等。

**最佳实践**：将所有构建和 CI/CD 相关的逻辑集中存放，便于管理和自动化。

### configs

配置文件模板或默认配置。此目录用于存放应用程序的各种配置信息，例如数据库连接字符串、API 密钥、服务端口等。通常会包含不同环境（开发、测试、生产）的配置示例。

**最佳实践**：将配置与代码分离，便于环境管理和安全。敏感信息应通过环境变量或秘密管理系统注入，而不是直接硬编码在配置文件中。

### deployments

IaaS (Infrastructure as a Service)，PaaS (Platform as a Service)，系统和容器编排部署配置和模板。这包括用于自动化部署应用程序到云平台或容器编排系统（如
Kubernetes, Helm, Terraform, Docker Compose）的脚本和配置文件。

**最佳实践**：将部署相关的配置和脚本集中存放，便于版本控制和自动化部署流程。

### init

系统初始化（systemd、upstart、sysv）和进程管理（runit、supervisord）配置。此目录用于存放操作系统级别的服务启动脚本和进程守护配置。

**最佳实践**：对于需要作为系统服务运行的应用程序，将相关的初始化和管理脚本放在此目录下。

### scripts

用于执行各种构建、安装、分析、清理等操作的脚本。这些脚本通常是辅助性的，用于自动化开发和运维任务。

**最佳实践**：将所有辅助性脚本集中存放，并确保它们是可执行的。这些脚本可以使根级别的 `Makefile` 变得更小更简单。

### test

外部测试应用程序和测试数据。此目录用于存放非单元测试（如集成测试、端到端测试）的代码和相关数据。Go 语言的单元测试通常与被测试代码放在同一个包下，以
`_test.go` 结尾。

**最佳实践**：对于较大的项目，可以有一个数据子目录（如 `/test/data` 或 `/test/testdata`）来存放测试所需的大型文件或数据集。Go
还会忽略以“.”或“_”开头的目录或文件。

## 其他目录

### assets

项目中使用的其他资源，例如图像、logo、字体文件等非代码资源。

### docs

设计和用户文档（除了 godoc 生成的文档）。此目录用于存放项目的架构设计文档、API 文档、用户手册、贡献指南等。

### examples

应用程序或公共库的示例程序。此目录用于展示如何使用项目中的库或如何运行应用程序的简单示例。

### githooks

Git 钩子脚本。此目录用于存放自定义的 Git 钩子，例如 `pre-commit`、`pre-push` 等，用于在 Git 操作时执行自动化任务。

### third_party

外部辅助工具，fork 的代码和其他第三方工具。此目录用于存放那些不通过 Go Modules 管理，或者需要特殊处理的第三方代码或工具。

### tools

此项目的支持工具。此目录用于存放辅助开发和维护的工具，这些工具通常是 Go 程序，并且可以从 `/pkg` 和 `/internal` 目录导入代码。

### website

如果项目有独立的网站（例如使用 Github Pages），则在这里放置项目的网站数据。

## 不应该出现的目录

### src

**不推荐使用。** 有些 Go 项目确实包含 `src` 文件夹，但这通常是受其他语言（如 Java）习惯的影响。在 Go 项目中，不建议使用项目级别的
`/src` 目录。

**原因**：Go 语言的工作空间（`$GOPATH`）本身就有一个 `/src` 目录，用于存放所有 Go 项目的源代码。如果您的项目内部再有一个
`/src` 目录，会导致路径冗余和混淆（例如：`/some/path/to/workspace/src/your_project/src/your_code.go`）。虽然 Go Modules
允许项目放在 `$GOPATH` 之外，但这并不意味着使用此布局模式是个好主意。

## 其他文件

### Makefile

在任何一个项目中都会存在一些需要运行的脚本，这些脚本文件应该被放到 `/scripts` 目录中并由 `Makefile` 触发。`Makefile`
通常作为项目自动化任务的入口点。

## 小结

每个公司、组织内部都有自己的组织方式，但每个项目都应该有一定的规范。虽然这种规范的约定没有那么强制，但是只要达成了一致之后，对于团队中组员快速理解和入门项目都是很有帮助的。有时候一些规范，就是团队的共同语言，定好了规范，减少了不必要的重复沟通，有利于提高整体的效率。

项目目录也一样，本篇文章讲的是参考 [golang-standards](https://github.com/golang-standards/project-layout)
提供的规范。但是，最重要的还是要与自己的团队商量，讨论并整理出适合自己的一套项目目录规范。

一致的项目目录规范，有助于组员快速理解其他人的代码，不容易造成团队的”单点故障“；团队团结一致，共同维护和升级项目目录结构，可不断沉淀，不断提高效率，减少犯错。

## 参考

- [golang-standards/project-layout](https://github.com/golang-standards/project-layout)
- [Go CodeReviewComments](https://github.com/golang/go/wiki/CodeReviewComments)
- [Effective Go](https://golang.org/doc/effective_go.html)
- [Commentary in Go](https://golang.org/doc/effective_go#commentary)
- [I’ll take pkg over internal](https://travisjeffery.com/blog/2019/11/i-will-take-pkg-over-internal/)
- [Package Oriented Design](https://www.ardanlabs.com/blog/2017/02/package-oriented-design.html)
- [GopherCon 2018: Kat Zien - How Do You Structure Your Go Apps](https://www.youtube.com/watch?v=oL6JBUk6tjF)
- [Golab 2018 Massimiliano Pippi - Project layout patterns in Go](https://www.youtube.com/watch?v=oL6JBUk6tjF)
