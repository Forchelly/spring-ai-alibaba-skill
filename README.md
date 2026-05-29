# Spring AI Alibaba Skill

一个 Claude Code Skill（斜杠命令），为使用 Spring AI Alibaba / Spring AI 构建 Java AI 应用提供中文开发指导。

基于本地 `references/` 文档集，覆盖从快速开始到多智能体编排的完整开发流程。

## 功能特性

- **智能体开发** — ReactAgent、SequentialAgent、ParallelAgent、LlmRoutingAgent 等模式
- **工具调用与 MCP** — 函数工具、MCP 工具集成、智能体作为工具组合
- **结构化输出** — JSON / POJO 结构化响应
- **记忆与持久化** — 短期记忆、长期记忆、Redis 检查点、状态持久化
- **图工作流** — StateGraph / CompiledGraph 编排、并行分支、子图、流式处理
- **多智能体** — 监督者模式、A2A 通信、agent-as-tool 组合
- **人工介入** — 人工审批、中断恢复、时间回溯
- **RAG 检索增强** — 向量检索、上下文工程、提示设计
- **选型指导** — Agent Framework vs Graph Core 的明确选型建议

## 安装

将本仓库克隆到 Claude Code skill 目录：

```bash
# 全局安装（推荐）
git clone https://github.com/Forchelly/spring-ai-alibaba-skill.git ~/.claude/skills/spring-ai-alibaba-skill

# 或项目级安装
git clone https://github.com/Forchelly/spring-ai-alibaba-skill.git <your-project>/.claude/skills/spring-ai-alibaba-skill
```

## 使用方式

在 Claude Code 中通过斜杠命令调用：

```
/spring-ai-alibaba 帮我构建一个基于 Spring AI Alibaba 的 Spring Boot AI 应用
```

### 示例

```bash
# 创建带工具调用的智能体
/spring-ai-alibaba 创建一个 ReactAgent，能够调用天气查询和网页搜索工具

# 设计图工作流
/spring-ai-alibaba 使用 StateGraph 实现一个多步骤的内容审核流程

# 搭建 RAG 应用
/spring-ai-alibaba 基于 Spring AI Alibaba 实现一个企业知识库 RAG 应用

# 多智能体协作
/spring-ai-alibaba 用监督者模式编排多个智能体完成代码审查任务

# 排查问题
/spring-ai-alibaba 我的 ReactAgent 工具调用不生效，帮我排查
```

## 项目结构

```
spring-ai-alibaba-skill/
├── SKILL.md              # Skill 定义（入口文件，含选型指南与文档路由）
├── agents/
│   └── openai.yaml       # Agent 接口配置
├── references/            # 本地参考文档集（来源于 java2ai-docs）
│   ├── overview.md        # 项目概览
│   ├── quick-start.md     # 快速开始
│   ├── versions.md        # 版本说明
│   ├── frameworks_agent-framework_*   # Agent Framework 文档
│   └── frameworks_graph-core_*        # Graph Core 文档
├── LICENSE               # MIT License
└── README.md
```

## 工作原理

1. 用户通过 `/spring-ai-alibaba` 发起请求
2. Skill 分析任务类型，选择 Agent Framework 或 Graph Core 方案
3. 按需加载 `references/` 中的最小必要文档集
4. 基于文档产出可运行的代码、配置和依赖

## 参考文档覆盖范围

| 分类 | 文档数 | 内容 |
|------|--------|------|
| 入门与基础 | 3 | 概览、快速开始、版本说明 |
| 智能体核心 | 3 | Agent、模型、消息 |
| 工具与 MCP | 3 | 工具调用、MCP 节点、agent-as-tool |
| 结构化输出 | 1 | JSON / POJO 输出 |
| 记忆与持久化 | 6 | 基础/高级记忆、Redis 检查点、持久化 |
| 工作流与图 | 9 | StateGraph、并行、子图、流式、取消、时间回溯 |
| 多智能体 | 3 | 多智能体模式、监督者、A2A |
| 人工介入 | 3 | 人工审批、中断恢复、长时间任务 |
| RAG | 2 | RAG 模式、上下文工程 |
| 扩展能力 | 2 | Hook、Skill 组合 |

## 许可证

[MIT License](LICENSE) - Copyright (c) 2026 Forchelly
