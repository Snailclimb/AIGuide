---
name: java-coding-standards
description: |
  Java/Spring Boot 编码规范：用于编写、审查、重构和讲解 Java 代码。适用于 Java 风格、Spring Boot 架构、Controller/Service/Manager/DAO 分层、API 边界、Entity/VO/Form/DTO/BO 设计、MyBatis/MyBatis-Plus 持久化、事务、异常、日志、安全、性能、测试和 Java 代码评审等场景。默认结合 Google Java Style 与 Spring Boot 最佳实践，同时允许仓库内的 AGENTS.md、CLAUDE.md、Checkstyle、Spotless 以及相邻代码风格覆盖这些通用规则。
---

# Java 编码规范

将这个 skill 视为 Java 17+ / Spring Boot 项目的简明工作卡。`SKILL.md` 只保留流程和路由，具体规则按需加载 `references/` 下的参考文件。

## 使用原则

- 仓库内规则优先于本 skill。若 `AGENTS.md`、`CLAUDE.md`、本地格式化配置或相邻代码与本 skill 冲突，优先遵循仓库约定；只有当冲突会影响结果时，再明确指出。
- 不要为了满足本 skill 而大规模重构无关代码。优先贴近现有模式，除非当前实现已经带来真实的正确性、安全性、事务或可维护性风险。
- 规则要尽量具体、可检查。维护本 skill 时，始终追问一句：删掉这条后，AI 是否更容易犯真实错误？
- 禁止项要配上推荐替代方案。例如：不要用字段注入；优先使用构造器注入，通常配合 Lombok `@RequiredArgsConstructor`。
- 机械性规则尽量交给工具。优先依赖 formatter、Checkstyle、Spotless、测试、CI 或 hooks，不要只靠自然语言约束格式。

## 工作流程

### 编写或重构代码时

1. 先看项目上下文：构建文件、本地说明文件、formatter/lint 配置、包结构以及相似类的现有写法。
2. 只加载当前改动真正需要的参考文件，按下方“参考路由”选择。
3. 控制改动面，尽量只改行为相关部分，不顺手做无关清理。
4. 把逻辑放到正确层级：
   Controller 负责路由、认证边界、校验注解和响应转换；
   Service 负责业务编排；
   Manager 负责事务和 DAO 组合；
   DAO/Mapper 负责持久化。
5. 用最小但有效的命令验证：优先跑定向测试、模块编译、formatter 或静态检查；如果没跑，要说明原因。

### 代码评审时

1. 先看 bug 和风险，不要先盯风格细节。
2. 按严重程度排序问题，并尽量给出文件/行号引用。
3. 每个问题都说明：违反了什么规则、为什么重要、推荐怎么修。
4. 风格问题只有在会带来维护成本，或与仓库工具链冲突时才提。
5. 如果没有发现问题，要明确说明，同时补充剩余的测试或验证缺口。

### 回答规范类问题时

优先按这个结构回答：规则、原因、示例、以及仓库内是否存在本地覆盖约定。

## 参考路由

| 需求 | 加载文件 |
| --- | --- |
| Java 格式、导入、括号、缩进、换行、switch、注解、修饰符、Javadoc、命名 | `references/google-style.md` |
| 项目结构、JavaBean 后缀、Optional、Stream、泛型、不可变性、现代 Java 特性 | `references/code-style.md` |
| 模块化目录、Controller/Service/Manager/DAO 分层、URL 规范、`@Transactional`、设计模式、统一响应 | `references/architecture.md` |
| 异常、日志、性能、线程池、远程调用、安全、测试、数据库设计 | `references/best-practices.md` |

## 核心默认规则

- 格式：2 空格缩进，不用 Tab，100 列限制，K&R 大括号风格，不用通配符导入，修饰符顺序标准化，可见 API 在有必要时补 Javadoc。
- 命名：类使用 `UpperCamelCase`，方法/字段使用 `lowerCamelCase`，常量使用 `UPPER_SNAKE_CASE`，包名全小写且不用下划线，项目名全小写并使用中划线。
- API 边界：不要直接暴露 `Entity`。请求用 `Form`，返回用 `VO`，跨层传输用 `DTO`，内部业务对象用 `BO`。`BO` 不应出现在 Controller API 中。
- 布尔字段：优先使用 `xxxFlag` / `xxx_flag`，避免 `isXxx` 字段，以免序列化和 RPC 映射歧义。
- 参数数量：单个方法参数尽量不超过 5 个；超过时优先封装为请求对象或值对象。
- URL 风格：默认跟随仓库现有约定。若仓库没有明确约定，本 skill 默认偏向非 RESTful 的 `GET/POST /module/submodule/action`，并且把路由注解写在方法上，便于搜索。
- 文档注解：遵循仓库当前使用的 Swagger/OpenAPI 体系。若项目使用 Swagger `@ApiOperation`，需要沿用既有 author 习惯；若项目使用 springdoc，则跟随本地 `@Operation` 写法。

## 分层默认规则

- Controller：不写业务逻辑，不做数据组装，不直接访问 DB/缓存/MQ。只有在请求边界层，且项目采用请求线程上下文时，才在这里获取当前用户。
- Service：负责业务编排和校验；大 Service 按 use case 拆分。
- Manager：默认把 `@Transactional(rollbackFor = Exception.class)` 放在这里，负责 DAO 组合和第三方平台细节封装，并尽量缩小事务范围。
- DAO/Mapper：只做持久化。对于采用这套约定的 MyBatis-Plus 项目，优先 XML mapper SQL，避免 Wrapper 查询构造器，常量通过参数传入，业务逻辑不要写进 XML。

## 没有本地覆盖时不要做的事

- 不要在数据库事务里调用外部 HTTP、LLM、S3、MQ、邮件或其他慢速远程接口。事务前准备数据，必要时在提交后触发副作用。
- 不要依赖同类内部调用触发 `@Transactional`；应把事务方法移动到另一个 Spring Bean，通常是 Manager，或使用仓库既有代理方案。
- 不要创建静态异常实例，否则堆栈信息会失真。
- 不要写空的 `catch (Exception e) {}`；要么处理、要么带上下文包装、要么明确说明为何可以忽略。
- 不要在同一层既记录日志又继续抛出同一个异常，除非该层就是最终处理层。
- 不要把 N+1 数据库调用或远程调用藏进 Stream 或循环里；应先批量查，再在内存中组装。
- 不要硬编码密钥、Token、供应商 base URL；使用配置与仓库既有的 URL 校验/客户端工厂。
- 不要在生产路径使用 `Executors.newFixedThreadPool()` 或 `newCachedThreadPool()`；要显式创建带边界队列、线程名、监控和关闭逻辑的 `ThreadPoolExecutor` Bean。

## 评审检查清单

架构与 API：
- Controller / Service / Manager / DAO 职责是否清晰分离。
- 请求/响应边界是否使用 Form / VO / DTO 和统一响应包装。
- 是否遵循本地 URL 与 Swagger/OpenAPI 规范。

事务与持久化：
- 需要事务的方法是否正确使用 `rollbackFor = Exception.class`。
- 事务内是否混入远程调用、大循环或慢操作。
- DAO/Mapper 是否保持持久化职责，没有出现 N+1 访问。

数据模型与 Java 风格：
- JavaBean 后缀与布尔 `flag` 规则是否正确。
- 方法是否足够短、可读，参数数量是否控制在 5 个以内。
- Optional、Stream、record、switch expression 是否在提升可读性时才使用。

异常、日志与安全：
- 领域异常是否包含足够上下文，且不是静态实例。
- 日志是否使用 SLF4J，保留堆栈，避免重复记录，并对敏感数据做脱敏。
- 外部 URL、密钥和用户输入是否走本地安全工具或约束。

测试与验证：
- 关键分支、边界和异常路径是否有聚焦测试覆盖。
- 是否运行了最小但有意义的编译/测试/lint 命令；若未运行，是否说明原因。

## Skill 维护规则

维护这个 skill 时，遵循与优秀 `CLAUDE.md` / `AGENTS.md` 相同的原则：

- 让 `SKILL.md` 只承担路由和执行层角色；长示例和细则放入 `references/`。
- 只有当某条规则长期有效、足够通用、并且确实能减少重复错误时，才把它固化进来。
- 删除模糊规则，例如“写出干净代码”，除非它包含明确检查点或替代方案。
- 如果某条规则本可以由 formatter、linter、hook、测试或 CI 强制执行，优先记录工具和验证命令，而不是只写成文字说明。
- 对 skill 做了较大修改后，用 `skill-creator` 的验证流程再检查一遍。
