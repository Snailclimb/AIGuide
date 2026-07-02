# AIGuide Skills

这个目录用于维护可直接安装的 Agent Skill。每个 skill 都是一个独立目录，至少包含：

```text
skill-name/
├── SKILL.md
└── agents/
    └── openai.yaml
```

这些 skill 主要面向 Codex、Claude Code、Cursor 等支持 Skills 机制的 AI Coding Agent。

## 如何安装

### 方式一：使用 Skills CLI 安装

适合已经在使用 [skills.sh](https://skills.sh/) / `npx skills` 生态的用户。

安装单个 skill：

```bash
npx skills add Snailclimb/AIGuide --path skills/<skill-name>
```

例如安装 `drawio-chart`：

```bash
npx skills add Snailclimb/AIGuide --path skills/drawio-chart
```

### 方式二：在 Codex 中直接从 GitHub 安装

适合主要在 Codex 中使用 skill 的用户。

```bash
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo Snailclimb/AIGuide \
  --path skills/<skill-name>
```

例如：

```bash
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo Snailclimb/AIGuide \
  --path skills/drawio-chart
```

安装完成后，建议重启 Codex，让新 skill 生效。

## 如何使用

安装完成后，通常可以直接在对话里显式引用 skill：

```text
使用 $drawio-chart 生成一个用户登录流程图，并导出为 PNG。
```

如果当前 Agent 支持隐式触发，也可以只描述任务，让它自动选择合适的 skill。不过在首次使用时，显式写出 `$skill-name` 往往更稳定。

## 当前已提供的 Skills

### drawio-chart

- 目录：[drawio-chart](/Users/guide/Documents/GitHub/AIGuide/skills/drawio-chart)
- 作用：根据需求生成专业的 draw.io 图表，支持流程图、架构图、时序图、ER 图等，并可导出为 PNG/SVG/PDF。
- 适用场景：技术文章配图、系统架构图、业务流程图、调用链路图、数据库关系图。

安装：

```bash
npx skills add Snailclimb/AIGuide --path skills/drawio-chart
```

使用示例：

```text
使用 $drawio-chart 生成一个微服务架构图，包含网关、用户服务、订单服务、Redis、MySQL 和 Kafka，并导出为 PNG。
```

### java-coding-standards

- 目录：[java-coding-standards](/Users/guide/Documents/GitHub/AIGuide/skills/java-coding-standards)
- 作用：为 Java / Spring Boot 项目提供分层架构、编码风格、事务、异常、日志、性能与测试方面的工程规范。
- 适用场景：Java 后端代码生成、重构、代码评审、架构边界检查、Spring Boot 项目规范问答。

安装：

```bash
npx skills add Snailclimb/AIGuide --path skills/java-coding-standards
```

使用示例：

```text
使用 $java-coding-standards 审查这个 Spring Boot PR，重点看分层、事务边界、异常处理和测试覆盖。
```

## 维护建议

- 对外引用 skill 时，优先使用 GitHub 仓库路径或安装命令，不要引用本机绝对路径。
- 每个 skill 目录尽量保持精简，核心内容放在 `SKILL.md` 和必要的 `references/` 中。
- 如果某个 skill 同时在本地开发和仓库分发，建议本地目录直接软链接到仓库目录，避免重复维护。
