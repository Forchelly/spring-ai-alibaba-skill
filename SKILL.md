---
name: spring-ai-alibaba
description: "当用户使用 Spring AI Alibaba 或 Spring AI 开发 Java AI 应用时使用，尤其适用于 ReactAgent、工具调用、结构化输出、记忆、RAG、MCP、StateGraph 工作流和多智能体编排，并基于 java2ai-docs 文档集提供实现指引。"
---

# Spring AI Alibaba

当用户希望基于 Spring AI Alibaba 设计、实现、排查、重构或解释 Java AI 应用时，使用这个 skill。

这个 skill 以本地 `references/` 文档集为准，目标不是只回答概念问题，而是指导实际开发落地。

## 适用场景

当任务涉及以下一项或多项内容时，使用这个 skill：
- 构建 `Spring Boot + Spring AI Alibaba` 项目
- 创建 `ReactAgent` 或智能体团队
- 为 Java 应用添加工具调用或 MCP 工具集成
- 使用 `StateGraph` / `CompiledGraph` 设计图工作流
- 实现记忆、持久化、人工审批、流式输出
- 基于 Spring AI 抽象实现 RAG 检索增强应用
- 解读 `java2ai-docs` 中的框架 API、示例和推荐模式

## 工作规则

1. 先判断用户真正要实现的能力，再只加载最相关的参考文档。
2. 优先使用框架已经提供的能力，不随意自定义编排层。
3. 代码保持 Java + Spring Boot 的常见工程风格。
4. 产出代码时，至少给出可运行所需的依赖、配置项、Bean 和示例类。
5. 只要涉及框架行为判断，就优先根据本地 `references/` 文档核实后再回答。
6. 如果用户在 Agent Framework 与 Graph Core 之间选型，要明确给出判断，不要混用得含糊不清。

## 选型指南

根据任务特点选择实现方式：

- 当应用以“对话 + 工具调用”为主时，优先使用 `ReactAgent`。
- 当应用是多智能体协作，但整体仍然偏智能体驱动时，优先使用 `SequentialAgent`、`ParallelAgent`、`LlmRoutingAgent` 或 agent-as-tool 组合方式。
- 当流程包含显式步骤、分支、检查点、中断恢复或长流程编排时，优先使用 `StateGraph`。
- 当任务依赖企业私有知识或检索增强时，优先采用 RAG 模式。
- 当任务要求跨轮次、跨会话或跨流程恢复上下文时，优先采用记忆与持久化模式。

## 中文索引与文档路由

按任务只读取必要文档：

### 入门与基础
- `references/overview.md`：项目概览与整体架构
- `references/quick-start.md`：快速开始
- `references/versions.md`：版本说明与变更记录

### 智能体核心开发
- `references/frameworks_agent-framework_tutorials_agents.md`：`ReactAgent` 核心概念
- `references/frameworks_agent-framework_tutorials_models.md`：模型配置与接入
- `references/frameworks_agent-framework_tutorials_messages.md`：消息类型与消息组织

### 工具调用与 MCP
- `references/frameworks_agent-framework_tutorials_tools.md`：工具调用、函数工具、MCP 工具
- `references/frameworks_agent-framework_advanced_agent-tool.md`：智能体作为工具的组合方式
- `references/frameworks_graph-core_examples_mcp-node.md`：Graph Core 中的 MCP 节点示例

### 结构化输出
- `references/frameworks_agent-framework_tutorials_structured-output.md`：JSON / POJO 结构化输出

### 记忆与持久化
- `references/frameworks_agent-framework_tutorials_memory.md`：基础记忆能力
- `references/frameworks_agent-framework_advanced_memory.md`：高级记忆与长期记忆
- `references/frameworks_graph-core_core_memory.md`：Graph Core 的记忆机制
- `references/frameworks_graph-core_core_persistence.md`：持久化与检查点
- `references/frameworks_graph-core_examples_checkpoint-redis.md`：基于 Redis 的检查点示例
- `references/frameworks_graph-core_examples_persistence.md`：持久化示例

### 工作流与图编排
- `references/frameworks_agent-framework_advanced_workflow.md`：Agent Framework 工作流能力
- `references/frameworks_graph-core_quick-start.md`：Graph Core 快速开始
- `references/frameworks_graph-core_core_core-library.md`：核心图编排 API
- `references/frameworks_graph-core_core_streaming.md`：流式处理
- `references/frameworks_graph-core_examples_parallel-branch.md`：并行分支示例
- `references/frameworks_graph-core_examples_subgraph.md`：子图示例
- `references/frameworks_graph-core_examples_subgraph-as-compiledgraph.md`：子图作为 `CompiledGraph`
- `references/frameworks_graph-core_examples_subgraph-as-nodeaction.md`：子图作为 `NodeAction`
- `references/frameworks_graph-core_examples_subgraph-as-stategraph.md`：子图作为 `StateGraph`

### 多智能体
- `references/frameworks_agent-framework_advanced_multi-agent.md`：多智能体模式
- `references/frameworks_graph-core_examples_multi-agent-supervisor.md`：监督者式多智能体示例
- `references/frameworks_agent-framework_advanced_a2a.md`：A2A 智能体通信

### 人工介入与中断恢复
- `references/frameworks_agent-framework_advanced_human-in-the-loop.md`：人工介入流程
- `references/frameworks_graph-core_examples_human-in-the-loop.md`：Graph Core 人工审批示例
- `references/frameworks_graph-core_examples_cancellation.md`：取消与中断控制
- `references/frameworks_graph-core_examples_time-travel.md`：时间回溯与状态恢复
- `references/frameworks_graph-core_examples_long-time-running-task.md`：长时间运行任务

### RAG 与上下文工程
- `references/frameworks_agent-framework_advanced_rag.md`：RAG 检索增强模式
- `references/frameworks_agent-framework_advanced_context-engineering.md`：上下文工程与提示设计

### Hook 与扩展能力
- `references/frameworks_agent-framework_tutorials_hooks.md`：Hook 机制
- `references/frameworks_agent-framework_tutorials_skills.md`：Skill 组合与扩展

## 默认工作流

当用户要求“使用 Spring AI Alibaba 开发”时，按下面流程处理：

1. 判断当前任务更适合 Agent Framework 还是 Graph Core。
2. 从 `references/` 中读取最小必要文档集合。
3. 提取文档里的关键类型、Builder API 和配置模式。
4. 产出最小可运行结构：
   - Maven 依赖
   - `application.yml` 或环境变量
   - Spring Bean / 配置类
   - Agent 或 Graph 相关类
   - Tool / Model / Memory 接线代码
5. 如果用户已经有代码，优先适配现有包结构与项目风格。
6. 如果存在歧义，明确写出假设，并优先选择文档中更直接、更简单的实现路径。

## 输出约定

对于实现类任务，优先按以下顺序输出：
- 简短说明架构选型与原因
- 必需依赖与配置
- 最小可运行代码
- 可选的下一步增强建议

对于解释或排障类任务，优先按以下顺序输出：
- 先说明该框架能力是什么
- 再指出应参考哪些文档
- 再分析可能原因或推荐修复方式
- 最后给出修正后的代码或配置示例

## 常用开发模板

### 最小智能体应用
优先参考：
- `references/quick-start.md`
- `references/frameworks_agent-framework_tutorials_agents.md`
- `references/frameworks_agent-framework_tutorials_models.md`
- `references/frameworks_agent-framework_tutorials_tools.md`

### 带工具调用的助手
优先参考：
- `references/frameworks_agent-framework_tutorials_tools.md`
- `references/frameworks_agent-framework_tutorials_messages.md`
- `references/frameworks_agent-framework_tutorials_structured-output.md`

### 图工作流应用
优先参考：
- `references/frameworks_agent-framework_advanced_workflow.md`
- `references/frameworks_graph-core_quick-start.md`
- `references/frameworks_graph-core_core_core-library.md`

### 多智能体监督者模式
优先参考：
- `references/frameworks_agent-framework_advanced_multi-agent.md`
- `references/frameworks_graph-core_examples_multi-agent-supervisor.md`
- `references/frameworks_agent-framework_advanced_agent-tool.md`

### 企业级 RAG 应用
优先参考：
- `references/frameworks_agent-framework_advanced_rag.md`
- `references/frameworks_agent-framework_advanced_memory.md`
- `references/frameworks_agent-framework_advanced_context-engineering.md`

## 补充说明

- 细节敏感的问题优先依赖本地 `references/` 文档，而不是直接凭记忆回答。
- 如果框架已有明确类或模式，不要臆造未文档化 API。
- 如果问题与版本相关，先检查 `references/versions.md`。
