# HeyaLab Direction Alignment Form

> Fill in your choices for each section. Leave blank if unsure — we'll discuss.

---

## A. Immediate Actions (Phase 2 收尾)

| # | Question | Your Answer |
|---|----------|-------------|
| A1 | Git commit 当前未提交的工作（ADR-008 + 四组件设计文档 + 文档更新）？ | |
| A2 | 清理 `ARCHITECTURE/` 目录（01-06）中旧的 Factory 隐喻文档？ | □ 正式标记 deprecated  □ 删除  □ 保留不动  □ 重写为新范式 |
| A3 | 处理 ADR-006 与涌现范式的张力（SOP Encoding / Emergent Social Behavior）？ | □ 写新 ADR 正式解决  □ 标记为待审查  □ 暂不处理 |

---

## B. Phase 3: Specification Freeze (规格冻结)

### B1. Priority Ranking

Rank the following specifications from 1 (highest) to 6 (lowest):

| Rank | Specification | Description |
|------|--------------|-------------|
| ___ | Memory System Spec | 记忆层级协议（working/episodic/semantic/artifact） |
| ___ | Component Creation Protocol | 组件发明流程（角色、输入输出、涌现） |
| ___ | Human Checkpoint Protocol | 人工检查点类型与上下文打包 |
| ___ | Verification Pipeline Spec | 验证管线（0-4 级验证标准） |
| ___ | Emergence & Evolution Framework | 涌现检测、适应度、自然选择 |
| ___ | MVP Scope Definition | MVP 最小可行范围与技术栈选型 |

### B2. Specification Approach

| # | Question | Your Answer |
|---|----------|-------------|
| B2a | 规格文档的详细程度？ | □ 概念级（原则 + 接口定义）  □ 实现级（含伪代码和数据结构）  □ 介于两者之间 |
| B2b | 每个规格是否需要独立的 ADR？ | □ 是，每个规格一个 ADR  □ 否，合并为一个 Phase 3 ADR  □ 只对有争议的写 ADR |
| B2c | 规格是否需要学术论文引用支撑？ | □ 是，每个关键决策引用论文  □ 只在关键决策处引用  □ 不需要 |

---

## C. New Component Detail Design (新组件详细设计)

| # | Question | Your Answer |
|---|----------|-------------|
| C1 | Mantle 需要展开详细子文档（如 ARCHITECTURE.md、INTERFACES.md）吗？ | □ 是，Phase 3 前完成  □ 是，Phase 3 中完成  □ 不需要，README 够用 |
| C2 | Veil 需要展开详细子文档吗？ | □ 是，Phase 3 前完成  □ 是，Phase 3 中完成  □ 不需要，README 够用 |
| C3 | Ember 需要展开详细子文档吗？ | □ 是，Phase 3 前完成  □ 是，Phase 3 中完成  □ 不需要，README 够用 |
| C4 | Grove 需要展开详细子文档吗？ | □ 是，Phase 3 前完成  □ 是，Phase 3 中完成  □ 不需要，README 够用 |

---

## D. MVP Scope (MVP 范围)

### D1. MVP 包含哪些组件？

| Component | Include in MVP? | Notes |
|-----------|----------------|-------|
| Hearth | □ Yes  □ No | |
| Hey | □ Yes  □ No | |
| Mantle | □ Yes  □ No | |
| Ember | □ Yes  □ No | |
| Veil | □ Yes  □ No | |
| Grove | □ Yes  □ No | |

### D2. MVP Technology Preferences

| # | Question | Your Answer |
|---|----------|-------------|
| D2a | 核心语言？ | □ TypeScript/Node.js  □ Python  □ Go  □ Rust  □ Other: ______ |
| D2b | LLM Provider（MVP 只支持一家）？ | □ OpenAI  □ Anthropic  □ 本地模型  □ Other: ______ |
| D2c | 数据库？ | □ PostgreSQL  □ SQLite  □ MongoDB  □ Other: ______ |
| D2d | 前端框架？ | □ React  □ Vue  □ Svelte  □ Next.js  □ 暂无前端（CLI 优先） |
| D2e | 部署方式？ | □ 本地优先  □ Docker  □ 云原生  □ 暂不考虑 |

### D3. MVP Success Criteria

| # | Criterion | Must-Have or Nice-to-Have? |
|---|-----------|---------------------------|
| D3a | 用户能用自然语言描述目标，60 秒内启动 Hearth 空间 | □ Must  □ Nice |
| D3b | 智能体能发明至少 3 种类型的组件 | □ Must  □ Nice |
| D3c | 组件之间能产生至少 1 种涌现模式 | □ Must  □ Nice |
| D3d | 人工检查点（至少 2 种类型） | □ Must  □ Nice |
| D3e | 项目级记忆跨会话持久化 | □ Must  □ Nice |
| D3f | 创作物存储在 Mantle 中，带血统追踪 | □ Must  □ Nice |
| D3g | Ember 种子捕获和点燃 | □ Must  □ Nice |
| D3h | 基础 Veil 仪表盘 | □ Must  □ Nice |

---

## E. Documentation & Process (文档与流程)

| # | Question | Your Answer |
|---|----------|-------------|
| E1 | 文档语言？ | □ 全英文  □ 全中文  □ 英文为主，中文注释允许 |
| E2 | 是否需要更新 README.md 首页？ | □ 是，反映六组件架构  □ 暂不更新 |
| E3 | 是否需要 CONTRIBUTING.md 更新贡献指南？ | □ 是  □ 暂不 |
| E4 | Phase 3 完成后是否需要做一次整体文档审查？ | □ 是  □ 不需要 |

---

## F. Strategic Questions (战略问题)

| # | Question | Your Answer |
|---|----------|-------------|
| F1 | HeyaLab 这个 repo 的定位？ | □ 纯研究仓库  □ 研究 + 设计文档  □ 研究 + 设计 + 参考实现  □ Other: ______ |
| F2 | 是否计划开源？ | □ 是，Phase 3 后  □ 是，MVP 后  □ 暂不计划  □ 已开源 |
| F3 | 产品树策略（RUS → Van → G/H → HeyaCore）是否需要更新？ | □ 不需要，保持现状  □ 需要讨论调整 |
| F4 | 有没有你心里一直在想但还没提出来的想法或顾虑？ | （自由填写） |

---

## G. Timeline Expectations (时间预期)

| # | Question | Your Answer |
|---|----------|-------------|
| G1 | Phase 3（规格冻结）期望在多长时间内完成？ | ______ |
| G2 | 整体 MVP 期望在什么时间点可用？ | ______ |
| G3 | 你目前每周能投入多少时间在 Heya 上？ | ______ |

---

*填写完成后，把你的答案发给我，我们一起对齐方向。*
