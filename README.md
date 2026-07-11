# AI Knowledge Base — 多 Agent 设计与工程化行动营（第 2 期）配套代码

本仓库是黄佳老师【多 Agent 设计与工程化行动营（第 2 期）】的配套实战项目。

课程围绕一个真实场景——**AI 技术情报知识库**（自动采集 GitHub Trending / Hacker News 等来源，用 Agent 完成分析、整理、分发）——循序渐进地展开，同一个项目分四个版本迭代，每个版本对应课程的一个阶段，可独立运行：

| 版本 | 目录 | 本阶段新增能力 |
|---|---|---|
| V1 骨架 | [`v1-skeleton/`](v1-skeleton) | Agent 角色定义（Collector / Analyzer / Organizer），手动 `@` 调用 |
| V2 自动化 | [`v2-automation/`](v2-automation) | Pipeline 脚本、JSON 质量校验 Hook、GitHub Actions 定时采集 |
| V3 Multi-Agent | [`v3-multi-agent/`](v3-multi-agent) | LangGraph 工作流、审核循环（Review Loop）、Router / Supervisor 设计模式、成本守卫与安全模块 |
| V4 生产版 | [`v4-production/`](v4-production) | 多渠道分发（Telegram / 飞书）、交互式知识库机器人、OpenClaw 网关、Docker 部署 |

各版本内部都有自己的 `AGENTS.md`（OpenCode 加载的项目记忆文件）和 `RUN.md`（从零跑通指南），进入对应目录前建议先看这两个文件。

## 快速开始

```bash
cd v3-multi-agent   # 或其他版本目录
pip install -r requirements.txt
cp .env.example .env   # 填入 LLM_API_KEY / LLM_BASE_URL / LLM_MODEL
```

具体运行方式见每个版本目录下的 `RUN.md`。

## 课程资料

- [`docs/`](docs) — 课程配套 PDF（LoopEngineering 工程化、OpenClaw vs Claude Code 设计范式解析）
- [`资料包01/`](资料包01) — 第 1 节课件与讲义（PDF / Markdown 双版本）

## 技术栈

Python + OpenCode Agent 框架，LLM 使用 DeepSeek / Qwen（OpenAI 兼容接口），V3 起引入 LangGraph 做工作流编排，V4 用 Docker Compose 部署。
