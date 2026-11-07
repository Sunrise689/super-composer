# Super Composer

## Product overview

Super Composer is an MCP-driven writing workflow. It does more than generate prose. It creates a project with a style brief and source materials, stores draft revisions, runs separate Composer, Auditor, and Polish roles, uses decision checks for sentence style, fact risk, paragraph flow, duplicate information, and can launch role-specific review jobs through configurable lanes.

The result is a traceable writing process in which research angles, draft versions, audit findings, decisions, and finalization status remain connected to one project.

## The problem it solves

Writing systems often mix research, drafting, fact checking, rewriting, and deduplication into one model call. That makes it hard to know which sentence was checked, why a revision was made, whether a factual risk was confirmed, or whether a final draft still contains unresolved duplicate or style problems.

Super Composer separates these responsibilities. The Composer collects useful angles without writing the first draft, the Auditor identifies evidence and structural risks, and the Polish role improves expression after the audit. Its decision layer generates small yes/no checks for sentence style, fact risk, paragraph flow, duplicate pairs, and three-sentence windows, then keeps invalid or unconfirmed findings visible.

## Usage guide

Super Composer requires Python 3.11 or newer and an MCP client.

```bash
python server.py
```

Register `server.py` as a stdio MCP server. Call the tools in project order: create a project, save a draft revision, launch optional role jobs, run a decision audit, inspect status, collect results, and finalize only after unresolved findings are handled. Provide the project directory, style brief, materials, policy, and optional model or launcher configuration from the caller.

## Notes

The project directory is supplied by the user. External model bridges and sub-agent launchers are optional. A decision result is a review signal rather than proof of a factual claim. Finalization remains blocked when confirmed or unconfirmed findings require review, so the output should be read together with its audit evidence.

## Technical implementation

`server.py` is a stdio JSON-RPC MCP surface. `core.py` implements project initialization, SQLite state, draft revisions, role taskbooks, card receipts, decision audits, result collection, and finalization. `decision.py` creates bounded decision jobs for sentence style, fact risk, paragraph flow, duplicate pairs, and three-sentence windows. `prompts/` contains the Composer, Auditor, Polish, and decision prompts. SQLite stores projects, revisions, cards, jobs, and issues.

---

# Super Composer

## 产品介绍

Super Composer 是一个由 MCP 驱动的写作工作流。它不只是生成文字，而是从风格简报和素材开始建立项目，保存草稿版本，分别运行 Composer、Auditor 和 Polish 角色，检查句子表达、事实风险、段落推进、重复信息和局部结构问题，并可以通过可配置通道启动角色审阅任务。

它把研究角度、草稿版本、审校发现、判断结果和最终定稿状态连接在同一个项目里。

## 解决的痛点

很多写作系统把研究、起草、事实核验、改写和去重混成一次模型调用。这样很难知道哪句话被检查过，为什么要改，事实风险是否被确认，以及最终稿是否仍有重复或表达问题。

Super Composer 把职责拆开。Composer 负责收集有用角度，不直接代写初稿。Auditor 负责发现证据和结构风险。Polish 在审校后负责改善表达。判断层会针对句子风格、事实风险、段落流动、重复句对和三句窗口生成小型是非检查，并保留无效或未确认结果。

## 使用指南

需要 Python 3.11 或更高版本和一个 MCP 客户端。

```bash
python server.py
```

把 `server.py` 注册为 stdio MCP 服务。按项目顺序调用：创建项目、保存草稿版本、按需启动角色任务、运行判断审校、查看状态、收集结果，处理未解决发现后再定稿。项目目录、风格简报、素材、策略以及可选模型或派发配置由调用方传入。

## 注意事项

项目目录由使用者提供。外部模型桥和子智能体派发器都是可选项。判断结果是审校信号，不是事实本身的证明。存在需要复核的确认或未确认发现时，定稿会被阻止，最终结果必须和审校证据一起阅读。

## 技术实现

`server.py` 提供 stdio JSON-RPC MCP 接口。`core.py` 负责项目初始化、SQLite 状态、草稿版本、角色任务书、卡片回执、判断审校、结果收集和定稿。`decision.py` 为句子风格、事实风险、段落推进、重复句对和三句窗口生成有边界的判断任务。`prompts/` 保存 Composer、Auditor、Polish 和 decision 提示词。SQLite 保存项目、版本、卡片、任务和问题记录。
