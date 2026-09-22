# AI PM Workflow Lab

用小型实验和通俗笔记，理解 AI 产品中的模型、工具、知识与工作流。

这个项目记录我从 AI 产品经理视角学习大模型应用的过程：不只记概念，也尝试把概念放回实际产品链路中，理解它解决什么问题、需要哪些产品判断，以及如何验证结果。

## 学习路径

```text
Prompt
  -> Structured Output
  -> Workflow / Router
  -> Tool Calling
  -> State / Memory
  -> Agent / Multi-turn Agent
  -> RAG
  -> MCP / LangChain
  -> Token / Context
```

## 概念笔记

| 顺序 | 主题 | 这篇在回答什么 |
| --- | --- | --- |
| 01 | [Prompt](concepts/01-prompt.md) | 怎样把目标、上下文、约束和验收标准讲清楚？ |
| 02 | [Structured Output](concepts/02-structured-output.md) | 怎样让模型按字段和 Schema 整理信息？ |
| 03 | [Workflow 和 Router](concepts/03-workflow-and-router.md) | 怎样编排任务步骤，并根据条件选择分支？ |
| 04 | [Tool Calling](concepts/04-tool-calling.md) | 模型如何请求程序调用工具，谁负责校验和执行？ |
| 05 | [State 和 Memory](concepts/05-state-and-memory.md) | 怎样区分当前任务状态与可复用的长期背景？ |
| 06 | [Agent 基础](concepts/06-agent-basics.md) | 大模型、Agent、知识库和工具如何协作？ |
| 07 | [多轮 Agent](concepts/07-multi-turn-agent.md) | Agent 怎样通过追问补齐可执行任务所需的信息？ |
| 08 | [RAG](concepts/08-rag.md) | 怎样检索外部资料，再让模型基于资料回答？ |
| 09 | [MCP](concepts/09-mcp.md) | 怎样用标准协议连接外部工具和数据？ |
| 10 | [LangChain](concepts/10-langchain.md) | 应用框架怎样连接模型、Prompt、检索器和工具？ |
| 11 | [Token 和 Context](concepts/11-token-and-context.md) | 模型如何处理文本，以及应用怎样管理有限的上下文空间？ |

## 实验方向

后续计划围绕本地模型逐步搭建和记录小型实验，例如结构化输出、条件路由、工具调用、多轮状态和知识库检索。实验会注明运行环境、验证方法和已知限制，不把概念演示描述成生产级系统。

## 关于这些笔记

这是个人学习与产品思考记录，内容会持续修订。示例用于解释概念，不应直接视为生产系统设计或安全建议。涉及真实数据、外部 API 或业务操作时，需要额外进行事实校验、权限控制和人工确认。
