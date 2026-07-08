# CodeStrata

> A layered toolchain of AI coding skills — from code-level refactoring to system-level architecture.  
> 分层递进的 AI 编码技能组合 — 从代码级重构到系统级架构设计。

---

## Philosophy

Good software isn't built in one pass. It's shaped in layers — you spot a smell in a function, refactor it; you find a pattern that fits a cluster of classes, apply it; you see the component boundaries blurring, re-draw them. **CodeStrata** bundles skills that work at distinct layers, so you always have the right lens for the problem in front of you.

好的软件不是一次成型的，它是一层层打磨出来的。**CodeStrata** 把不同抽象层次的 skill 组合在一起，让你面对任何代码问题时都有正确的视角。

```
  ┌──────────────────────────────────────┐
  │         code-super-advisor           │  ← 编排层：审查代码 / 审查方案 / 辅助设计
  │         "The orchestrator"           │
  ├──────────────────────────────────────┤
  │         clean-architecture-advisor   │  ← 系统层：组件 / 边界 / 依赖方向
  │         "The big picture"            │
  ├──────────────────────────────────────┤
  │         gof-architecture-advisor      │  ← 模式层：23 种设计模式 / SOLID
  │         "How classes relate"         │
  ├──────────────────────────────────────┤
  │         refactoring-advisor          │  ← 代码层：坏味道 / 72 种重构手法
  │         "The bricks and mortar"      │
  └──────────────────────────────────────┘
```

---

## Skills

### Orchestrator

| Skill | Role | What It Does |
|-------|------|--------------|
| **code-super-advisor** | 🎯 编排层 | 编排子 skill，完成代码审查 / 方案评审 / 设计辅助，输出结构化检查文档 |

### Layered Advisors

| Skill | Layer | Source | What It Does |
|-------|-------|--------|--------------|
| **refactoring-advisor** | 🔬 代码级 | Martin Fowler《重构》 | 诊断 22 种代码坏味道，执行 72 种逐步重构变换 |
| **gof-architecture-advisor** | 🧩 模式级 | GoF《设计模式》 | 识别设计问题，推荐合适的 23 种设计模式，给出具体重构方案 |
| **clean-architecture-advisor** | 🏗️ 系统级 | Bob Martin《架构整洁之道》 | 组件划分、边界设计、依赖管理、分层策略、架构评审 |

### code-super-advisor

**定位**：编排型 skill — 本身不提供具体知识，而是判断工作模式后自顶向下调度子 skill，汇总输出文档

**三大职责**：
- **审查代码** — 给定代码，从架构到代码逐层诊断，输出代码审查报告
- **审查实现方案** — 给定设计文档/方案，评估合理性与风险，输出方案评审报告
- **辅助设计** — 给定需求，从架构到代码逐层给出建议，输出设计建议书

**何时用 code-super-advisor vs 单独的子 skill**：
- 需要全层次、系统性分析 → 用 code-super-advisor
- 只涉及单一层次（如"帮我重构这个函数"）→ 直接用对应子 skill

### refactoring-advisor

**关注点**：函数、方法、类的内部结构  
**核心能力**：坏味道诊断 → 重构手法选择 → 逐步代码变换 → 验证  
**经典场景**：代码审查、技术债务清理、既有代码改善

### gof-architecture-advisor

**关注点**：类之间的关系与协作模式  
**核心能力**：设计问题识别 → 模式推荐 → SOLID 原则校验 → 解耦方案  
**经典场景**：新功能设计、旧代码重构、vibe coding 架构提升

### clean-architecture-advisor

**关注点**：组件/模块/服务的边界与依赖  
**核心能力**：架构诊断 → 边界划分 → 依赖方向检查 → 策略层次设计  
**经典场景**：系统架构设计、架构评审、技术选型评估

---

## Usage

Copy the skill folders into your AI coding tool's skills directory:

```bash
# Replace <skills-dir> with your tool's skills path (see table below)
cp -r code-super-advisor <skills-dir>/
cp -r clean-architecture-advisor <skills-dir>/
cp -r gof-architecture-advisor <skills-dir>/
cp -r refactoring-advisor <skills-dir>/
```

| Tool | Skills Directory |
|------|-----------------|
| WorkBuddy | `~/.workbuddy/skills/` |
| Claude Code | `~/.claude/skills/` |
| OpenClaw | `~/.openclaw/skills/` |
| Cursor | `~/.cursor/skills/` |
| Continue (VS Code) | `~/.continue/skills/` |

Then invoke them naturally in conversation — your AI coding tool will load the right skill based on your intent.

---

## Roadmap

- [ ] More code-quality skills (testing, performance, security)
- [ ] Domain-specific architecture patterns (microservices, event-driven)
- [ ] Language-specific refactoring catalogs
- [ ] Interactive decision trees for pattern selection

---

## License

MIT
