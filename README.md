# Super Composer

## 1. Product introduction

Super Composer is a traceable writing workflow exposed as a stdio MCP server. It turns a source set and a style brief into an auditable manuscript process instead of one opaque generation call. A project stores immutable draft revisions, role taskbooks, card receipts, decision jobs, open issues, collected reports, and the final version.

The workflow separates three roles. Composer gathers facts, angles, quotations, and uncertainty from the declared materials without writing the first draft. Auditor checks evidence, structure, scope, and factual risk. Polish changes only the requested passages and saves a new revision. A local decision layer checks sentence style, factual risk, paragraph progression, duplicate sentence pairs, and three-sentence windows.

## 2. Problems it solves

A single model call often mixes research, drafting, fact checking, rewriting, and deduplication. That makes it hard to tell which claim came from which file, why a sentence changed, whether a risk was accepted, or whether the final text is actually the audited revision.

Super Composer makes those transitions explicit. Invalid or unavailable decision results become open issues instead of being treated as approval. Card reports are collected by card ID, Polish output is saved as a new immutable revision, and finalize copies the selected processed revision only after the caller has reviewed the issue state.

## 3. Usage guide

Requirements: Python 3.11 or newer and an MCP client.

~~~bash
python mcp_entry.py
~~~

Register the entry point as a stdio MCP server and call the tools in this order:

1. create_project with a project directory, style brief, and declared source materials.
2. save_draft to create revision 1. Every later edit is another revision; old revisions are never overwritten.
3. launch_role_cards when external material collection, auditing, or scoped polishing is useful. Each lane has its own profile, model, effort, taskbook, and report directory.
4. run_decision_audit for local sentence, risk, paragraph, duplicate, and window checks.
5. get_status to inspect versions, cards, jobs, and open_issues.
6. collect_results to retrieve the report belonging to each card ID.
7. finalize with the reviewed revision and confirm=true after unresolved findings have been handled.

The current policy supports explicit MiniMax, CX Luna, and OpenRouter lanes. MiniMax is pinned to MiniMax-M3.1-Flash; the CX lane uses CX-U-OFFICIAL with gpt-6-luna; the OpenRouter lane requires an openrouter/ model and keeps the model choice explicit. Provider credentials remain in local configuration and are never copied into taskbooks or reports.

## 4. Notes and boundaries

Composer reports are evidence maps, not a substitute for reading the source. Auditor and decision results are review signals and do not prove a claim by themselves. An invalid, timed-out, empty, or unavailable decision is recorded as an issue and cannot silently pass.

finalize always creates final_article.md from the selected revision. It does not invent missing edits and it does not merge an older draft over a newer Polish result. External card processes can finish with a non-zero exit code or without a declared report; those states must remain visible in the project status.

## 5. Technical implementation

mcp_entry.py provides the stdio JSON-RPC transport. server.py exposes create_project, save_draft, launch_role_cards, run_decision_audit, get_status, collect_results, and finalize. core.py owns project directories and SQLite tables for project, versions, cards, jobs, and issues.

decision.py launches bounded local checks and writes decision-results.json. The core records every hit with revision, kind, location, text, and status; invalid judgments create a decision_unavailable issue and mark the job finished_with_issues. prompts/ contains the Composer, Auditor, Polish, and decision prompts. The dispatch path writes one taskbook and one card directory per role card, and result collection reads only the report declared by that card.

---

# Super Composer

## 1. 产品介绍

Super Composer 是一个通过 stdio MCP 提供的可追溯写作工作流。它把素材和风格简报转换为可审计的成稿过程，而不是一次无法解释的生成调用。项目会保存不可变草稿版本、角色任务书、卡片回执、判断任务、未关闭问题、收集到的报告和最终版本。

它把工作拆成三个角色。Composer 只从声明的素材中收集事实、角度、引用和不确定项，不直接写第一版。Auditor 审查证据、结构、范围和事实风险。Polish 只修改被指定的段落，并把结果保存为新的版本。判断层负责检查句子风格、事实风险、段落推进、重复句对和三句窗口。

## 2. 解决的痛点

一次模型调用经常把检索、起草、事实核验、改写和去重混在一起，导致使用者无法知道某个结论来自哪个文件、为什么修改某句话、风险是否被接受，以及最终文本是不是经过审校的版本。

Super Composer 把这些转折点显式化。判断结果 invalid、超时、空结果或不可用时会写入未关闭问题，不会被当成通过。卡片报告按 card ID 收集，Polish 结果保存为新的不可变版本，finalize 只在调用方检查问题状态后复制选定的处理版本。

## 3. 使用指南

需要 Python 3.11 或更高版本和 MCP 客户端。

~~~bash
python mcp_entry.py
~~~

将入口注册为 stdio MCP 服务，并按以下顺序调用：

1. create_project：传入项目目录、风格简报和声明的素材。
2. save_draft：保存第 1 版草稿。后续修改都保存为新版本，不覆盖旧版本。
3. launch_role_cards：需要外部素材整理、审计或指定范围润色时再派发。每个通道都有独立 profile、模型、推理档、任务书和报告目录。
4. run_decision_audit：运行本地句子、风险、段落、重复和窗口判断。
5. get_status：查看版本、卡片、任务和 open_issues。
6. collect_results：按 card ID 收回对应报告。
7. 处理未关闭发现后，用选定版本调用 finalize 并传入 confirm=true。

当前策略支持明确的 MiniMax、CX Luna 和 OpenRouter 通道。MiniMax 固定使用 MiniMax-M3.1-Flash；CX 通道使用 CX-U-OFFICIAL 和 gpt-6-luna；OpenRouter 通道必须显式传入 openrouter/ 前缀模型。供应商密钥只从本机配置读取，不写入任务书和报告。

## 4. 注意事项

Composer 报告是事实地图，不能代替阅读源文件。Auditor 和判断结果是审校信号，不单独证明事实。判断模型 invalid、超时、空结果或不可用时会记录问题，不能静默通过。

finalize 总是从选定版本创建 final_article.md，不会自行补写缺失修改，也不会把旧版本覆盖到更新后的 Polish 版本上。外部卡片非零退出或没有声明报告时，项目状态必须继续暴露这些事实。

## 5. 技术实现

mcp_entry.py 提供 stdio JSON-RPC 传输层。server.py 暴露 create_project、save_draft、launch_role_cards、run_decision_audit、get_status、collect_results 和 finalize。core.py 管理项目目录，并使用 SQLite 保存 project、versions、cards、jobs 和 issues 表。

decision.py 启动有边界的本地判断并写出 decision-results.json。核心逻辑按版本、类型、位置、文本和状态记录每个发现；判断不可用会创建 decision_unavailable 问题，并把任务标为 finished_with_issues。prompts/ 保存 Composer、Auditor、Polish 和 decision 提示词。派发流程为每张角色卡写入独立任务书和工作目录，结果收集只读取该卡声明的报告。




