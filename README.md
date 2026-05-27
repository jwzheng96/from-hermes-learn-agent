# 从 Hermes 学 Agent

> A code-level Chinese tutorial book on agent engineering — taught by reading [Hermes Agent](https://github.com/NousResearch/hermes-agent) source.

一本中文电子书,14 章约 11 万字。通过精读 [Nous Research](https://nousresearch.com) 的开源 agent 项目
[Hermes Agent](https://github.com/NousResearch/hermes-agent) 源码,配合 2022–2026 年间 Agent 研究的关键论文和工业实践
(ReAct、Voyager、MemGPT、Anthropic Context Engineering、MCP、Agent Skills 标准化、SWE-Bench Verified 等),
教你怎么造一个工业级 Agent。

## 怎么读

```bash
git clone --recursive https://github.com/jwzheng96/from-hermes-learn-agent.git
cd from-hermes-learn-agent/tutorial
python3 -m http.server 8000
# 浏览器打开 http://localhost:8000
```

需要本地 HTTP server,因为 mermaid 图表从 CDN 加载 ES module,`file://` 协议会拒绝。

如果你已经 clone 但忘了 `--recursive`:

```bash
git submodule update --init --recursive
```

## 目录

| Part | 章 | 内容 |
|------|----|------|
| **I 基础** | 1 · Agent 的前世今生 | 从 1956 到 2026,Agent 这个词变了什么 |
| | 2 · 心智模型 | ReAct / CoT / Workflow vs Agent / 四种 multi-agent 抽象 |
| **II 核心循环** | 3 · run_conversation 解剖 | 3589 行 while 循环的完整拆解 |
| | 4 · 消息协议 &amp; Tool Calling | OpenAI 协议、reasoning content、Provider quirks |
| | 5 · System Prompt 与缓存经济 | 三层 stable/context/volatile + cache 节省 10× 成本 |
| **III 工具系统** | 6 · Tool Registry 与发现 | 79 个工具如何不互相打架 |
| | 7 · Toolset、安全与 Hook | Capability security · MCP · prompt injection 2026 攻防 |
| **IV 学习闭环** | 8 · Skills 程序性记忆 | Voyager → Hermes → 2025.12 Anthropic 开放标准 |
| | 9 · Memory 与 FTS5 跨会话搜索 | MemGPT/Letta/Mem0/A-Mem 四家对比 |
| | 10 · Curator 自我维护 | active → stale → archived 状态机 |
| **V 多面架构** | 11 · CLI / Gateway / Profile | 20+ 平台共享一份核心 |
| | 12 · Provider &amp; Plugin 生态 | 30 个 ProviderProfile · MCP host |
| **VI 前沿研究** | 13 · 2024–2026 Agent Frontier | Benchmark 6 件套 · Multi-agent 大洗牌 · Agent RL (GRPO) |
| **VII 实践** | 14 · 动手与设计哲学 | 写 Plugin / Skill / MCP server + 10 条原则 |

## 适合谁

- 写过 LangChain / OpenAI Assistants,想看真正生产级 agent 怎么搭
- 想跟上 2024–2026 Agent 工程前沿 (Anthropic Context Engineering / Harness / MCP / Agent Skills 标准化)
- 准备自建 agent 产品,想避开 framework 抽象债

## 项目结构

```
tutorial/                 — 电子书本体 (15 个 HTML + 共享 CSS)
hermes-agent/             — 上游源码,git submodule pinned 在 920b350e5
CLAUDE.md                 — 给 AI 助手的本仓库工作指南
```

## 致谢

本书的代码示例和工程实践 100% 基于 [Hermes Agent](https://github.com/NousResearch/hermes-agent)(MIT),
Nous Research 团队的开源工作。Hermes submodule 固定在 commit `920b350e5`——
这样书里引用的代码行号永远对得上。如需更新到上游新版,先审计章节里的 `filename` 行号引用。

特别感谢以下研究的作者,本书广泛引用了他们的工作:Yao et al. (ReAct)、Wang et al. (Voyager)、
Park et al. (Generative Agents)、Packer et al. (MemGPT)、Anthropic Engineering 团队
(Building Effective Agents / Context Engineering / Harness Engineering / MCP)、
DeepSeek 团队 (R1 / GRPO post-training)、Sierra (τ²-Bench)、OSWorld / METR / GAIA / SWE-Bench 各团队。

## License

本书内容 (`tutorial/` 目录) 以 [MIT](LICENSE) 协议发布。

`hermes-agent/` submodule 的版权归 Nous Research,遵循其原始 MIT 协议
([参见上游 LICENSE](https://github.com/NousResearch/hermes-agent/blob/main/LICENSE))。
