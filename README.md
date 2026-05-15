# AI 应用开发

你好，我是 [JavaGuide](https://javaguide.cn/) 的作者。最近几个月一直在继续补充完善 AI 应用开发部分的内容。

目前的话，这份面向后端开发者的 AI 应用开发、AI 编程实战与面试指南已免费开源，涵盖 LLM、Agent、RAG、MCP、Claude Code、Codex 等核心技术与工程实践。对标 [JavaGuide](https://javaguide.cn/home.html)！有帮助的话，欢迎 Star！

- **项目地址**：<https://github.com/Snailclimb/AIGuide>
- **在线阅读**：<https://javaguide.cn/ai/>

这应该是当前最全面系统的讲解，每一篇都花费了大量时间完善和优化，每篇文章都画了大量配图辅助理解：

![AIGuide 内容概览，大量配图](https://oss.javaguide.cn/github/aiguide/aiguide-overview.png)

发布之后，也是收到了很多读者朋友的好评和推荐。非常感谢，一定会持续用心维护！

![AIGuide 收到了很多读者朋友的好评和推荐](https://oss.javaguide.cn/github/aiguide/ai-guide-received-many-positive-reviews-and-recommendations-from-readers.png)

## 大模型基础

- [万字拆解 LLM 运行机制](https://javaguide.cn/ai/llm-basis/llm-operation-mechanism.html)：Token、上下文窗口、Temperature 等如何还原成可落地的工程理解？
- [大模型 API 调用工程实践](https://javaguide.cn/ai/llm-basis/llm-api-engineering.html)：流式输出、重试、限流与结构化返回的 Java 后端落地
- [大模型结构化输出详解](https://javaguide.cn/ai/llm-basis/structured-output-function-calling.html)：JSON Schema、Function Calling、Tool Calling 与 MCP 的工程落地

## AI Agent

- [一文搞懂 AI Agent 核心概念](https://javaguide.cn/ai/agent/agent-basis.html)：梳理 AI Agent 六代进化史，掌握 Agent Loop、Context Engineering、Tools 注册等核心概念
- [AI Agent 记忆系统详解](https://javaguide.cn/ai/agent/agent-memory.html)：短期记忆与长期记忆设计，掌握记忆存储形式、生命周期操作与生产级工程优化策略
- [大模型提示词工程实践指南](https://javaguide.cn/ai/agent/prompt-engineering.html)：掌握 Prompt 四要素框架、六大核心技巧及企业级安全实践
- [上下文工程实战指南](https://javaguide.cn/ai/agent/context-engineering.html)：深入理解 Context Engineering 核心概念，掌握静态规则编排、动态信息挂载、Token 预算降级等关键技术
- [万字详解 Agent Skills](https://javaguide.cn/ai/agent/skills.html)：深入理解 Skills 的设计理念，掌握 Skills 与 Prompt、MCP、Function Calling 的本质区别
- [万字拆解 MCP 协议](https://javaguide.cn/ai/agent/mcp.html)：理解 MCP 协议的核心概念、架构设计和生产级最佳实践
- [一文搞懂 Harness Engineering](https://javaguide.cn/ai/agent/harness-engineering.html)：深度解析 Harness 六层架构、上下文管理，拆解 OpenAI、Anthropic、Stripe 等一线团队的 Agent 工程化实战经验
- [AI 工作流中的 Workflow、Graph 与 Loop](https://javaguide.cn/ai/agent/workflow-graph-loop.html)：理解 AI 工作流中三种核心编排模式的原理与适用场景

## RAG（检索增强生成）

- [万字详解 RAG 基础概念](https://javaguide.cn/ai/rag/rag-basis.html)：RAG 是什么？为什么需要 RAG？RAG 的核心优势和局限性是什么？
- [万字详解 RAG 向量索引算法和向量数据库](https://javaguide.cn/ai/rag/rag-vector-store.html)：HNSW、IVFFLAT 等索引算法的原理是什么？如何选择合适的向量数据库？
- [万字详解 GraphRAG](https://javaguide.cn/ai/rag/graphrag.html)：知识图谱驱动的 RAG，深入理解实体、关系、社区发现、全局检索与局部检索
- [万字详解 RAG 检索优化](https://javaguide.cn/ai/rag/rag-optimization.html)：Chunk 策略、Hybrid Search、Query Rewrite、Rerank、上下文压缩等实战优化

## AI 编程实战

⭐️ **AI 编程专栏**：[https://javaguide.cn/ai-coding/](https://javaguide.cn/ai-coding/)

- [IDEA + Qoder 插件多场景实战](https://javaguide.cn/ai-coding/idea-qoder-plugin.html)：通过深分页优化、祖传代码重构两个真实案例，展示 AI 辅助编程的实战效果
- [Trae + MiniMax 多场景实战](https://javaguide.cn/ai-coding/trae-m2.7.html)：使用 Trae IDE 接入 MiniMax 大模型，通过 Redis 故障排查和跨语言重构场景分享 AI 辅助编程实战经验
- [Claude Code 接入第三方模型实战](https://javaguide.cn/ai-coding/cc-glm5.1.html)：通过 Claude Code 接入 GLM-5.1，完成 JVM 智能诊断助手搭建和百万级数据量慢查询治理
- [DeepSeek V4 + Claude Code 实战](https://javaguide.cn/ai-coding/deepseek-v4-claude-code.html)：深入体验 DeepSeek V4 与 Claude Code 的集成，实测代码审计、Flyway 集成、多模型协同等场景

## AI 编程技巧

- [AI 编程必备 Skills 推荐](https://javaguide.cn/ai-coding/programmer-essential-skills.html)：实战分享 6 个 AI 编程 Skills，覆盖 TDD 开发流程、代码审查、UI 设计、网页自动化与 Skill 开发
- [Claude Code 核心命令详解](https://javaguide.cn/ai-coding/claudecode-commands.html)：深入解析 /simplify、/review、/loop、/batch 等核心命令的使用方法与实战技巧
- [Claude Code 使用指南](https://javaguide.cn/ai-coding/claudecode-tips.html)：整理自 Anthropic 官方技术文档并融合实战经验，系统梳理 Claude Code 的使用技巧
- [OpenAI Codex 最佳实践指南](https://javaguide.cn/ai-coding/codex-best-practices.html)：综合官方文档与实战经验，系统梳理 Codex 的提示工程、工具配置与安全策略
- [AI 编程选 CLI 还是 IDE？](https://javaguide.cn/ai-coding/cli-vs-ide.html)：深度对比 Claude Code、Cursor、Kiro、TRAE 等主流 AI 编程工具，解析 CLI 与 IDE 的核心差异与选型建议
- [AI 编程开放性面试题](https://javaguide.cn/ai-coding/ai-ide.html)：涵盖 Cursor、Claude Code 等 AI 编程 IDE 使用技巧，以及 AI 对后端开发影响等高频面试问题
