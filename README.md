# Memory-R1: Reinforcement-Learned Memory Management for LLM Agents / 记忆强化学习框架

This repository summarizes **Memory-R1** from the paper *"Memory-R1: Enhancing Large Language Model Agents to Manage and Utilize Memories via Reinforcement Learning"* (arXiv: 2508.19828). It highlights how reinforcement learning is used to **store, update, forget, and utilize memories** instead of hand-crafted heuristics. For the full methodology and experiments, please read the [original paper](https://arxiv.org/abs/2508.19828).

本仓库总结论文 *Memory-R1: Enhancing Large Language Model Agents to Manage and Utilize Memories via Reinforcement Learning* 的核心思路，强调通过强化学习而非启发式规则来驱动大模型的 **记、改、删、用** 记忆。

## Motivation / 动机

- Standard LLMs are effectively **stateless** and context-limited, making long-horizon, multi-turn, or multi-session reasoning difficult.
- Conventional memory banks rely on **heuristics** for what to store or retrieve, with no learning tied to downstream task success.
- Memory-R1 aims for **persistent, adaptive memory** so agents can remember, forget, and leverage information based on task outcomes.

- 传统 LLM 近似“**无状态**”且上下文窗口有限，难以支持长时、多轮、多会话推理。
- 常见外部记忆方案依赖 **启发式** 决策存取内容，缺乏面向任务表现的学习机制。
- Memory-R1 希望实现 **持久且自适应的记忆**，根据任务奖励学会何时记、何时忘并有效利用。

## Core Framework / 核心框架

Memory-R1 introduces two RL-finetuned agents that operate over an external memory bank and are optimized jointly with QA reward:

1. **Memory Manager** — Issues structured actions **{ADD, UPDATE, DELETE, NOOP}** turn by turn to curate the memory bank.
2. **Answer Agent** — Retrieves up to 60 candidate memories, applies a **Memory Distillation** policy to pick the most relevant subset, then answers with the distilled memories.

Both agents are trained with outcome-driven RL (e.g., **PPO** or **GRPO**). Rewards come from downstream QA performance (exact match or semantic correctness), aligning memory behaviors with end-task success.

Memory-R1 包含两个基于强化学习微调的代理，围绕外部记忆库运作：

1. **记忆管理器**：逐轮输出 **{添加、更新、删除、保持不变}** 操作，对记忆进行结构化管理。
2. **回答代理**：检索最多 60 条候选记忆，使用 **记忆蒸馏策略** 选出最相关子集，再结合精炼记忆生成答案。

两者通过以任务表现为奖励的 RL（如 **PPO**、**GRPO**）训练，奖励来自 QA 的准确度或语义匹配度，使记忆行为与最终任务目标对齐。

> 请结合原始论文阅读，以获取模型架构、训练细节（如 PPO/GRPO 配置、Memory Distillation 策略实现）以及数据处理流程的完整描述。

## Why It Matters / 价值

- Replaces brittle heuristics with **learned memory management** tuned to task rewards.
- Enables **persistent memory** across sessions and **long-horizon reasoning** via learned remembering/forgetting.
- Works across model scales with **modest training data** (~152 QA pairs with temporal memory context reported).

- 用 **可学习的记忆策略** 取代脆弱的启发式规则，直接对齐任务奖励。
- 通过学会“记与忘”，实现跨会话的 **持久记忆** 与 **长程推理** 能力。
- 在不同规模模型上适用，并能在 **较少数据**（论文报告约 152 组 QA + 时间序列记忆）下取得较好效果。

## High-Level Pipeline / 流程概览

```
User Input / 用户输入
   ↓
Memory Manager (RL) → ADD / UPDATE / DELETE / NOOP → 更新 Memory Bank
   ↓
Memory Bank (external storage) / 外部记忆库
   ↓
Answer Agent: retrieve top-k → Memory Distillation → distilled subset
回答代理：检索候选 → 记忆蒸馏 → 精炼子集
   ↓
LLM + distilled memories + query → generate answer
LLM 结合精炼记忆与查询生成答案
   ↓
Answer correctness → reward → RL updates for both agents
答案正确性 → 奖励 → 强化学习更新两类策略
```

## Reported Results / 实验结果

- On long-context benchmarks like **LoCoMo**, Memory-R1 **outperforms heuristic pipelines** on F1, BLEU-1, and semantic-judge metrics.
- Demonstrates strong gains in **multi-turn, multi-session QA** and **long-horizon reasoning** with learning-based memory control.
- Reports competitive performance with **minimal supervision** (~152 QA pairs + temporal memory traces), highlighting data efficiency.

- 在长上下文基准（如 **LoCoMo**）上，Memory-R1 在 F1、BLEU-1、语义评测等指标上 **显著优于启发式方案**。
- 在 **多轮、多会话 QA** 与 **长程推理** 上体现了基于学习的记忆控制带来的明显提升。
- 论文指出在 **较少监督数据**（约 152 组 QA + 时间序列记忆）下仍能取得竞争性表现，体现数据效率。

## Reference / 原始论文链接

- Memory-R1: Enhancing Large Language Model Agents to Manage and Utilize Memories via Reinforcement Learning — [arXiv:2508.19828](https://arxiv.org/abs/2508.19828)

## Key Takeaways / 关键要点

- Memory-R1 shows that **memory storage and usage can be optimized end-to-end** via reinforcement learning, not just rules.
- The paired agents (Memory Manager + Answer Agent) jointly enable **adaptive memory curation** and **memory-aware generation**.
- The framework is **data-efficient** and **backbone-agnostic**, offering a path to more persistent, coherent LLM agents.

- Memory-R1 证明 **记忆的存取与使用可以端到端优化**，不仅依赖规则。
- 记忆管理器与回答代理协同，实现 **自适应的记忆编排** 与 **记忆感知的生成**。
- 框架 **高效且主干无关**，为更持久、连贯的 LLM 代理提供了一条通用路径。
