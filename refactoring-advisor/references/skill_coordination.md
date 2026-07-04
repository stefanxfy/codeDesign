# 三个 Skill 协同使用指南

> 本文档描述 `refactoring-advisor`、`gof-architecture-advisor`、`clean-architecture-advisor` 三个 Skill 的协同使用场景和切换条件。

---

## 三个 Skill 的定位差异

| Skill | 抽象层次 | 核心问题 | 典型输出 |
|-------|---------|---------|---------|
| **refactoring-advisor** | 函数/方法/类 | "这段代码臭了，怎么一步步改成好代码？" | 逐步变换步骤 + 坏味道诊断 |
| **gof-architecture-advisor** | 类/模式 | "这几个类用什么模式组织？" | 类结构图 + 接口定义 + 模式选择 |
| **clean-architecture-advisor** | 系统/组件 | "整个系统怎么划分边界？" | 分层架构图 + 组件清单 + 边界清单 |

---

## 协同使用场景

### 场景 1：从坏代码到好设计

```
用户有一段坏代码
  ↓
[refactoring-advisor] 激活
  - 诊断坏味道（22 种坏味道清单）
  - 选择重构手法（72 种手法目录）
  - 执行逐步变换（小步前进，测试先行）
  ↓
在变换过程中，发现需要引入设计模式
  ↓
切换到 [gof-architecture-advisor]
  - 匹配适合的设计模式（23 种模式目录）
  - 输出类结构方案和代码示例
  - 评估模式引入的效果
  ↓
如果系统层面需要重新组织（如组件划分、依赖管理）
  ↓
切换到 [clean-architecture-advisor]
  - 画边界（同心圆模型）
  - 管理依赖（依赖规则：箭头只指向内层）
  - 隔离细节（数据库/Web/框架都是实现细节）
```

### 场景 2：新系统架构设计

```
用户描述一个新系统需求
  ↓
[clean-architecture-advisor] 激活（系统级优先）
  - 划分组件边界
  - 设计依赖方向
  - 确定分层策略
  ↓
在组件内部，需要设计类结构
  ↓
切换到 [gof-architecture-advisor]
  - 选择适合的设计模式
  - 输出类结构方案
  ↓
实现过程中，发现代码有坏味道
  ↓
切换到 [refactoring-advisor]
  - 诊断坏味道
  - 执行逐步重构
```

### 场景 3：代码审查

```
用户提交一段代码要求审查
  ↓
[refactoring-advisor] 先激活（代码级问题优先）
  - 闻坏味道（22 种坏味道）
  - 输出重构建议
  ↓
如果发现架构层面问题
  ↓
切换到 [clean-architecture-advisor]
  - 检查组件依赖是否合理
  - 检查边界是否清晰
  - 检查细节是否隔离
```

---

## 触发词与 Skill 激活优先级

| 用户说... | 首先激活哪个 Skill | 说明 |
|----------|-------------------|------|
| "这段代码太长了/重复了/难以理解" | refactoring-advisor | 代码级问题优先 |
| "这段代码该用什么设计模式？" | gof-architecture-advisor | 模式选择 |
| "这个类太复杂了，怎么重构？" | refactoring-advisor → gof-architecture-advisor | 先诊断，再选模式 |
| "整个系统怎么分层？" | clean-architecture-advisor | 系统级问题 |
| "组件之间耦合太紧" | clean-architecture-advisor | 依赖管理 |
| "vibe coding 出来的代码质量太差" | refactoring-advisor | 代码质量改进 |
| "架构评审" | clean-architecture-advisor | 系统级架构评审 |
| "设计模式选择" | gof-architecture-advisor | 模式匹配 |

---

## Skill 之间的知识引用

### refactoring-advisor → gof-architecture-advisor

当重构过程中需要引入设计模式时：

| 重构手法 | 可能引入的设计模式 | 参考 GoF Skill |
|---------|-------------------|---------------|
| Replace Conditional with Polymorphism | State, Strategy, Factory Method | `references/gof_patterns_catalog.md` |
| Form Template Method | Template Method | `references/gof_patterns_catalog.md` |
| Replace Inheritance with Delegation | 无（用组合替代继承） | `references/gof_patterns_catalog.md` 的"组合优于继承" |
| Encapsulate Downcast | Factory Method, Abstract Factory | `references/gof_patterns_catalog.md` |
| Replace Type Code with State/Strategy | State, Strategy | `references/gof_patterns_catalog.md` |
| Introduce Null Object | Null Object | `references/gof_patterns_catalog.md` |

### refactoring-advisor → clean-architecture-advisor

当重构过程中发现系统级问题时：

| 坏味道 | 可能的系统级问题 | 参考 Clean Architecture Skill |
|--------|----------------|----------------------------|
| 霰弹式修改 | 组件边界不清晰 | `references/clean_architecture_principles.md` |
| 过度耦合的消息链 | 依赖方向错误 | `references/component_principles.md` |
| 依恋情结（跨组件） | 边界划分错误 | `references/architecture_patterns.md` |
| 被拒绝的遗赠（跨组件） | 组件职责划分错误 | `references/clean_architecture_principles.md` |

### gof-architecture-advisor → clean-architecture-advisor

当模式选择需要考虑系统级架构时：

| 设计模式 | 系统级考量 | 参考 Clean Architecture Skill |
|---------|-----------|----------------------------|
| Facade | 边界划分 | `references/architecture_patterns.md` |
| Observer | 组件间通信 | `references/component_principles.md` |
| Mediator | 组件耦合管理 | `references/component_principles.md` |
| Dependency Injection | 依赖方向管理 | `references/clean_architecture_principles.md` |

---

## 使用建议

1. **从低层到高层**——先解决代码级问题（refactoring），再考虑类级设计（GoF），最后考虑系统级架构（Clean Architecture）
2. **但不要僵化**——如果一眼就能看出是系统级问题，直接从 Clean Architecture 开始
3. **迭代进行**——重构不是一次性的，随着系统演化，可能需要多次回到 refactoring-advisor
4. **相信嗅觉**——如果代码闻起来不对，先用 refactoring-advisor 诊断；如果架构看起来不对，先用 clean-architecture-advisor 诊断

---

## 与其他工具的协同

### 与测试框架的协同

重构前必须有测试。如果用户说"没有测试"，先参考 `refactoring_methodology.md` 的"测试原则"章节，帮助用户建立测试。

### 与版本控制的协同

小步前进 + 频繁提交 = 安全网双重保障。建议：
- 每完成一个重构手法，提交一次
- 提交消息写明重构手法名称（如 "Extract Method: 提炼计算租金逻辑"）
- 如果测试失败，回退到上一个提交

### 与 IDE 重构工具的协同

现代 IDE 提供了自动重构工具（如 IntelliJ 的 Refactor 菜单）。使用建议：
- IDE 工具适合低风险的重构（Rename Method, Extract Method）
- 复杂重构（如 Replace Conditional with Polymorphism）仍需手动进行，IDE 工具只辅助部分步骤
- 无论用不用 IDE 工具，**测试先行**的原则不变

---

## 快速参考：三个 Skill 的功能对比

| 功能 | refactoring-advisor | gof-architecture-advisor | clean-architecture-advisor |
|------|---------------------|--------------------------|---------------------------|
| 坏味道诊断 | ✅ 22 种 | ✅ 8 种反模式 | ✅ 组件依赖问题 |
| 逐步变换指导 | ✅ 72 种手法 | ❌ | ❌ |
| 设计模式选择 | ❌ | ✅ 23 种 | ❌ |
| 组件边界划分 | ❌ | ❌ | ✅ |
| 依赖管理 | ❌ | ❌ | ✅ |
| 测试方法论 | ✅ | ❌ | ❌ |
| SOLID 原则 | ✅ 引用 | ✅ 完整讲解 | ✅ 架构视角 |
| 代码示例 | ✅ 变换前/后 | ✅ 模式结构 | ✅ 架构图 |

---

> **核心思想**：三个 Skill 不是竞争关系，而是**协作关系**。它们形成"修复 → 设计 → 组织"的完整工具链，各自聚焦不同抽象层次，互相补充而非重叠。
