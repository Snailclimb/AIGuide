# AI 应用开发

你好，我是 [JavaGuide](https://javaguide.cn/) 的作者。最近这段时间，我一直在补 AI 应用开发、AI 编程实战和 AI 面试这几块内容。

这份开源指南写给所有想系统学习 **AI 应用开发与 AI 工程化落地** 的同学：后端、前端、测试、架构师、技术管理者、产品技术同学都可以看。你不需要先转算法岗，也不需要一上来就啃训练框架和论文公式；这里重点放在 LLM、Agent、RAG、MCP、Prompt、评测、系统设计、Claude Code、Codex 这些做应用时会真正用到的东西。

如果你本来是 Java / Go 后端，会更容易把高并发、缓存、数据库、消息队列、可观测性这些经验迁移过来；如果你是前端、测试或产品技术方向，也可以从 Prompt、RAG、Agent、AI Coding、评测和系统设计切入，理解一个 AI 功能从 Demo 到上线到底要补哪些环节。

- **项目地址**：<https://github.com/Snailclimb/AIGuide>
- **在线阅读**：<https://javaguide.cn/ai/>
- **AI 编程专题**：<https://javaguide.cn/ai-coding/>

目前每篇文章都会尽量配上真实工程场景、关键参数、踩坑点和图解。内容还在持续更新，有帮助的话欢迎 Star，也欢迎提 Issue 一起补充。

![AIGuide 内容概览，大量配图](https://oss.javaguide.cn/github/aiguide/aiguide-overview.png)

发布之后，收到了不少读者朋友的反馈和推荐。感谢大家，我会继续维护。

![AIGuide 收到了很多读者朋友的好评和推荐](https://oss.javaguide.cn/github/aiguide/ai-guide-received-many-positive-reviews-and-recommendations-from-readers.png)

## Agent Skills

AIGuide 仓库里也会同步维护一部分可直接安装的 Agent Skill，目前已提供：

- `drawio-chart`：根据需求生成专业的 draw.io 图表，支持流程图、架构图、时序图、ER 图等，并可导出为 PNG/SVG/PDF。
- `java-coding-standards`：为 Java / Spring Boot 项目提供分层架构、编码风格、事务、异常、日志、性能与测试方面的工程规范。

安装方式：

```bash
npx skills add Snailclimb/AIGuide --path skills/drawio-chart
```

如果你主要在 Codex 里使用，也可以直接从 GitHub 安装：

```bash
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo Snailclimb/AIGuide \
  --path skills/drawio-chart
```

## 怎么读

如果你是第一次系统学 AI 应用开发，建议按这个顺序走：

1. 先看 **大模型基础**：把 Token、上下文窗口、采样参数、API 调用、结构化输出和评测搞清楚。
2. 再看 **RAG**：企业知识库问答最常见，坑也最多，文档处理、向量检索、更新链路和评测都得补。
3. 接着看 **AI Agent**：重点理解 Tool Calling、Memory、MCP、Skills、Workflow / Graph / Loop。
4. 最后看 **AI 系统设计**：把 Demo 放进生产环境，处理网关、限流、fallback、成本、观测、安全和灰度。

AI Coding 不是另一条完全独立的线。它更像是日常研发方式的升级，写业务代码、改前端页面、补测试、做重构、查线上问题都能用。建议一边学 AI 应用开发，一边用 Claude Code、Codex、Cursor、Trae 这类工具练起来。

## 学习路线与建议

- [学习路线合集（2026 最新版）](https://javaguide.cn/roadmap/)：统一整理 Java 后端、AI 应用开发、AI Agent 和全栈方向的系统学习建议。
- [Java/Go 开发者 AI 应用开发与 Agent 学习路线（2026 最新版）](https://javaguide.cn/roadmap/java-to-ai-roadmap.html)：面向后端开发者，按大模型基础、LLM API、Prompt、RAG、Agent、工程化和项目实战拆解学习路径。
- [后端开发者转型 AI Agent 学习建议（2026 最新版）](https://javaguide.cn/roadmap/backend-to-ai-agent-roadmap.html)：先判断是否适合转型，再看 Java AI 与 Python AI 怎么选、能投什么岗位、应该如何学习。
- [后端开发者全栈学习路线（2026 最新版）：AI 时代如何补齐前端和交付能力](https://javaguide.cn/roadmap/full-stack-roadmap.html)：结合 AI 编程工具，补齐前端、接口联调、权限、部署和独立交付能力。

## 面试题

- [AI 应用开发面试题专题](https://javaguide.cn/ai/interview-questions/)：按大模型基础、AI Agent、RAG 和 AI 系统设计组织复习路线。
- [AI 应用开发面试指南](https://javaguide.cn/ai/interview-questions/ai-interview-guide.html)：把 LLM、RAG、Agent、系统设计这几条线串起来，适合系统复盘。
- [大模型基础面试题总结](https://javaguide.cn/ai/interview-questions/llm-interview-questions.html)：Token、上下文窗口、结构化输出、模型调用等高频问题。
- [AI Agent 面试题总结](https://javaguide.cn/ai/interview-questions/agent-interview-questions.html)：Agent Loop、Tool Calling、Memory、MCP、工作流编排等问题。
- [RAG 面试题总结](https://javaguide.cn/ai/interview-questions/rag-interview-questions.html)：Embedding、向量数据库、混合检索、Rerank、GraphRAG、RAG 评测。
- [AI 系统设计面试题总结](https://javaguide.cn/ai/interview-questions/ai-system-design-interview-questions.html)：AI 应用架构、模型网关、成本控制、可观测、安全合规。

## 大模型基础

- [大模型基础专题](https://javaguide.cn/ai/llm-basis/)：从模型运行机制、API 调用、结构化输出到 AI 应用评测，先把调用链路看明白。
- [LLM 运行机制：Token、上下文窗口与采样参数怎么影响输出](https://javaguide.cn/ai/llm-basis/llm-operation-mechanism.html)：把 Token、上下文窗口、Temperature、Top-p、Max Tokens 这些基础概念讲透。
- [大模型 API 调用工程实践：流式输出、重试、限流与结构化返回](https://javaguide.cn/ai/llm-basis/llm-api-engineering.html)：从 Hello World 走到生产可用，重点看 SSE、超时、重试、取消、幂等和异常处理。
- [大模型结构化输出：从 JSON 契约到 Function Calling 落地](https://javaguide.cn/ai/llm-basis/structured-output-function-calling.html)：JSON Schema、Structured Outputs、Function Calling、MCP Tool 和服务端校验怎么配合。
- [AI 应用评测体系：从 Golden Set 构建到线上灰度闭环](https://javaguide.cn/ai/llm-basis/llm-evaluation.html)：不要靠体感上线，学会用 Golden Set、LLM-as-Judge、Trace 回放和 CI 回归评估 AI 功能。

## AI Agent

- [AI Agent 专题](https://javaguide.cn/ai/agent/)：从 Agent 基础概念、Memory、Prompt、Context 到 MCP、Skills 和 Harness Engineering。
- [AI Agent 核心概念：Agent Loop、Plan-and-Execute、A2A、Agentic Workflows、Tools 注册](https://javaguide.cn/ai/agent/agent-basis.html)：先搞清 Agent 到底比普通 LLM 调用多了什么。
- [AI Agent 记忆系统：短期记忆、长期记忆与记忆演化机制](https://javaguide.cn/ai/agent/agent-memory.html)：短期记忆、长期记忆、Markdown 记忆、记忆写入和检索怎么落地。
- [大模型提示词工程（Prompt Engineering）是什么？提示词技巧有哪些？](https://javaguide.cn/ai/agent/prompt-engineering.html)：Prompt 四要素、Few-Shot、CoT、Prompt Injection 防御和结构化输出。
- [上下文工程(Context Engineering) 是什么？和 Prompt Engineering 有什么区别？](https://javaguide.cn/ai/agent/context-engineering.html)：重点看上下文组装、Token 预算、动态信息挂载和降级策略。
- [Agent Skills 是什么？和 Prompt、MCP 到底差在哪？](https://javaguide.cn/ai/agent/skills.html)：理解 Skills 的触发方式、延迟加载、渐进式披露和工程边界。
- [什么是 Model Context Protocol (MCP)？和 Function Calling、Agent 什么关系？](https://javaguide.cn/ai/agent/mcp.html)：MCP 的 Host、Client、Server、Tools、Resources、Prompts 分别做什么。
- [一文搞懂 Harness Engineering：六层架构、上下文管理与一线团队实战](https://javaguide.cn/ai/agent/harness-engineering.html)：看 Agent 外面那层工程外壳怎么管上下文、工具、状态、评测和安全。
- [AI 工作流中的 Workflow、Graph 与 Loop：从概念到实现](https://javaguide.cn/ai/agent/workflow-graph-loop.html)：用工作流、图和循环把 Agent 的不确定性关进可控边界里。

## RAG（检索增强生成）

- [RAG 专题](https://javaguide.cn/ai/rag/)：围绕企业知识库问答，梳理文档处理、向量数据库、GraphRAG、检索优化和知识库更新。
- [万字详解 RAG 基础概念](https://javaguide.cn/ai/rag/rag-basis.html)：RAG 是什么，为什么需要它，和长上下文、微调、传统搜索怎么选。
- [RAG 文档处理与切分策略：从解析、清洗、Chunking 到多模态内容处理](https://javaguide.cn/ai/rag/rag-document-processing.html)：RAG 效果上限经常卡在文档处理，PDF、表格、图片、Chunking 都要处理好。
- [万字详解 RAG 向量索引算法和向量数据库](https://javaguide.cn/ai/rag/rag-vector-store.html)：HNSW、IVFFLAT、pgvector、Milvus、Elasticsearch 等怎么选。
- [RAG 知识库文档如何更新：增量更新、版本控制、去重与全量重建](https://javaguide.cn/ai/rag/rag-knowledge-update.html)：文档变更后怎么同步、去重、回滚、灰度和监控。
- [万字详解 GraphRAG：为什么只靠向量检索撑不起复杂知识问答](https://javaguide.cn/ai/rag/graphrag.html)：实体、关系、社区发现、局部检索、全局检索和 Text2Cypher。
- [万字详解 RAG 优化：从召回、重排到上下文工程的系统调优](https://javaguide.cn/ai/rag/rag-optimization.html)：Query Rewrite、Hybrid Search、Rerank、上下文压缩、失败样本定位。

## AI 系统设计

- [AI 系统设计专题](https://javaguide.cn/ai/system-design/)：把 Prompt Demo 放进真实后端系统里看，重点关注架构、模型网关、语音链路、可观测、评测和安全治理。
- [AI 应用系统设计：从 Prompt Demo 到生产级架构](https://javaguide.cn/ai/system-design/ai-application-architecture.html)：把 Prompt Demo 放进真实业务系统，补齐 Prompt 管理、模型网关、RAG、Memory、Tool、评测和安全。
- [大模型网关详解：多模型路由、fallback、限流与成本控制](https://javaguide.cn/ai/system-design/llm-gateway.html)：多供应商适配、模型路由、fallback、限流配额、Token 预算、成本归因和审计。
- [AI 语音技术详解：从 ASR、TTS 到实时语音 Agent 的工程化落地](https://javaguide.cn/ai/system-design/ai-voice.html)：ASR、TTS、VAD、打断处理、低延迟优化和实时语音 Agent。

## AI 编程实战

- [AI 编程实践指南](https://javaguide.cn/ai-coding/)：系统整理 Claude Code、Codex、AI IDE、CLI Agent、上下文管理和 AI 辅助开发工作流。
- [IDEA + Qoder 插件多场景实战：接口优化与代码重构](https://javaguide.cn/ai-coding/cases/idea-qoder-plugin.html)：JetBrains 用户怎么在 IDEA 里用 AI 做接口优化和代码重构。
- [Trae + MiniMax 多场景实战：Redis 故障排查与跨语言重构](https://javaguide.cn/ai-coding/cases/trae-m2.7.html)：用 Trae 接入大模型，处理 Redis 问题和跨语言改造。
- [Claude Code 接入第三方模型实战：JVM 智能诊断与慢查询治理](https://javaguide.cn/ai-coding/cases/cc-glm5.1.html)：Claude Code 接入 GLM-5.1，做 JVM 诊断和慢 SQL 治理。
- [DeepSeek V4 + Claude Code 实战：代码能力深度测评](https://javaguide.cn/ai-coding/cases/deepseek-v4-claude-code.html)：看 DeepSeek V4 在代码审计、数据库迁移、多模型协作里的表现。
- [MiniMax M3 + Claude Code 实战：Redis 故障排查、SCAN 算法复刻与监控面板搭建](https://javaguide.cn/ai-coding/cases/cc-m3.html)：用长上下文和编码模型处理更完整的工程任务。
- [Claude Desktop 接入第三方模型实战：用 CC Switch 打通 DeepSeek](https://javaguide.cn/ai-coding/cases/claude-desktop-cc-switch.html)：用 CC Switch 接管 Claude Desktop 的本地代理配置，拆解模型映射、协议转换和故障转移。
- [IDEA 爽用 Claude Code 和 Codex 的终极方案，很丝滑！](https://javaguide.cn/ai-coding/project/cc-guide.html)：JetBrains 里接 Claude Code / Codex GUI 的实践。

## AI 编程技巧

- [Vibe Coding 实用技巧总结：Git、Spec、上下文管理与多 Agent 协作](https://javaguide.cn/ai-coding/practices/the-cool-tricks-for-vibe-coding.html)：从 Git、Spec、上下文、任务拆分和多 Agent 协作几个角度讲怎么少踩坑。
- [Claude Code 使用指南：配置、工作流与进阶技巧](https://javaguide.cn/ai-coding/practices/claudecode-tips.html)：Claude Code 的配置、常用工作流、Sub-Agent、多实例协作和上下文管理。
- [CLAUDE.md 最佳实践：该写什么、不该写什么、项目变大后怎么拆](https://javaguide.cn/ai-coding/practices/claude-md-best-practices.html)：把项目规则写给 AI 看，避免每次都从零解释仓库。
- [Claude Code 核心命令详解：simplify、code-review、loop、batch、run、verify](https://javaguide.cn/ai-coding/practices/claudecode-commands.html)：常用命令怎么用，适合哪些任务，边界在哪里。
- [AI 编程必备 Skills 推荐：TDD、代码审查与网页自动化实战](https://javaguide.cn/ai-coding/practices/programmer-essential-skills.html)：推荐一批能直接改善 AI 编程体验的 Skills。
- [Codex 使用指南：配置、AGENTS.md 与 Agentic 工作流](https://javaguide.cn/ai-coding/practices/codex-best-practices.html)：Codex 的配置、AGENTS.md、任务拆解、验证和安全边界。
- [AI 编程选 CLI 还是 IDE？一文帮你彻底搞清楚](https://javaguide.cn/ai-coding/practices/cli-vs-ide.html)：Claude Code、Codex、Cursor、Trae、Qoder 这类工具怎么选。
- [Claude Code Agent View：多会话并行管理实战](https://javaguide.cn/ai-coding/practices/claudecode-agentview.html)：多个 Agent 会话并行时，怎么管理状态、权限确认和任务进度。
- [10 道 AI 编程相关的开放性面试问题](https://javaguide.cn/ai-coding/practices/ai-ide.html)：Cursor、Claude Code、Skills、Spec Coding、AI 对开发工作的影响等问题。
- [Spec Coding 规范驱动编程实战：从 Vibe Coding 到 AI 代码规范](https://javaguide.cn/ai-coding/practices/spec-coding.html)：用 Spec 把需求、约束、验收标准写清楚，再让 AI 动手。
