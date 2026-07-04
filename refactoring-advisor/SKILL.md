---
name: refactoring-advisor
description: "基于 Martin Fowler《重构：改善既有代码的设计》的代码重构顾问。当用户需要改善既有代码质量、识别代码坏味道、执行逐步重构变换时使用。与 GoF 设计模式 Skill 和 Clean Architecture Skill 形成三级工具链：本 Skill 聚焦函数/方法/类级别的代码变换技术（22种坏味道诊断 + 72种重构手法），GoF 聚焦类级别设计模式选择，Clean Architecture 聚焦系统级别架构决策。触发词：重构、代码坏味道、代码质量、代码异味、重复代码、过长函数、代码审查、代码改进、refactor、code smell、technical debt、代码腐化、代码混乱、改善代码。"
---

# 重构顾问

基于 Martin Fowler《重构：改善既有代码的设计》的代码级重构顾问。

与 GoF 设计模式 Skill 和 Clean Architecture Skill 形成三级工具链：
- **本 Skill**：函数/方法/类级别——诊断坏味道，执行逐步代码变换
- **GoF Skill**：类级别——选择设计模式来组织类的关系
- **Clean Architecture Skill**：系统级别——组织组件、划分边界、管理依赖

## 核心理念

重构的本质是：**在不改变软件可观察行为的前提下，以微小的步伐调整代码结构，使其更易理解和修改。**

重构不是"重写"，不是"优化性能"，不是"添加功能"。重构是一种节奏：

```
测试 → 小修改 → 测试 → 小修改 → 测试 → 小修改……
```

> "使重构能够成功的，不是各自独立的技术，而是这种节奏。" —— Kent Beck

---

## 三个 Skill 的分工

| 场景 | 用哪个 Skill |
|------|-------------|
| "这段代码太长了/重复了/难以理解" | **本 Skill** |
| "这段代码该用什么设计模式？" | GoF Skill |
| "这个类太复杂了，怎么重构？" | **本 Skill** 诊断 → GoF 选择模式 |
| "整个系统怎么分层？" | Clean Architecture Skill |
| "组件之间耦合太紧" | Clean Architecture Skill |
| "重构过程中发现需要引入 State/Strategy 模式" | GoF Skill |
| "代码审查，这段代码有什么问题？" | **本 Skill**（先闻味道） |
| "vibe coding 出来的代码质量太差" | **本 Skill** |

### 三级工具链

```
坏代码 → [本 Skill] 诊断+修复（函数/方法/类级别变换）
         ↓ 变换中发现模式机会
       → [GoF Skill] 选择模式（类级别设计）
         ↓ 系统层面需要重组
       → [Clean Architecture Skill] 组织组件（系统级别架构）
```

---

## 工作流程

### 阶段1：诊断 —— 闻代码的味道

面对一段代码时，先用 22 种坏味道清单做诊断。不要急着给方案——先确认问题是什么。

**快速嗅探清单**（按优先级排列）：

| 优先级 | 坏味道 | 30秒判断法 |
|--------|--------|-----------|
| 高 | 重复代码 | 有没有相同的结构出现在多个位置？ |
| 高 | 过长函数 | 函数是否超过屏幕一页？是否需要注释才能理解？ |
| 高 | 过大的类 | 类是否有超过 7 个字段？是否有不相关的职责？ |
| 高 | 分支惊悚现身 | 是否有相同 switch/match 散落在多处？ |
| 中 | 发散式变化 | 一个类是否因不同原因被修改？ |
| 中 | 霰弹式修改 | 一种变化是否要修改多个文件？ |
| 中 | 依恋情结 | 函数是否大量调用另一个对象的方法？ |
| 中 | 数据类 | 类是否只有字段和 getter/setter 没有行为？ |
| 中 | 基本类型偏执 | 是否用数字/字符串表示本该有类型的概念？ |
| 低 | 过多注释 | 注释是否在解释"代码做了什么"而非"为什么"？ |
| 低 | 冗赘类/夸夸其谈 | 是否有没有用到的类或参数？ |
| 低 | 中间人 | 类是否一半函数都在做委托转发？ |

> 完整 22 种坏味道详见 `references/bad_smells.md`。

### 阶段2：准备 —— 确保有安全网

在开始任何重构之前，确认以下前提：

1. **是否有自动化测试？**——这是重构的安全网
   - 有测试 → 确认测试覆盖了即将修改的代码路径
   - 没有测试 → **先写测试**（这是非协商性的）
   - 测试不可靠 → 先修复或补充测试

2. **测试是否可自动验证？**——测试必须输出 OK/失败，而非需要人工检查

3. **是否清楚戴着哪顶帽子？**
   - 重构帽：只改结构，不加功能
   - 功能帽：只加功能，不改结构
   - 不要同时戴两顶帽子

> "重构前，先检查自己是否有一套可靠的测试机制。这些测试必须有自我检验能力。"

### 阶段3：选择 —— 匹配重构手法

根据诊断出的坏味道，从 72 种重构手法中选择。核心决策树：

```
代码有什么问题？
├── 函数太长
│   ├── 临时变量不多 → Extract Method
│   ├── 临时变量过多 → Replace Temp with Query → Extract Method
│   └── 临时变量泛滥 → Replace Method with Method Object
├── 类太大
│   └── Extract Class / Extract Subclass
├── 参数太多
│   └── Introduce Parameter Object / Preserve Whole Object
├── 重复代码
│   ├── 同类内 → Extract Method
│   ├── 兄弟子类 → Extract Method + Pull Up Method
│   └── 不相关类 → Extract Class
├── switch/match 散布
│   └── Move Method → Replace Type Code with State/Strategy → Replace Conditional with Polymorphism
├── 数据和操作分离
│   └── Move Method（搬行为到数据类）
├── 过度委托
│   └── Remove Middle Man / Inline Method
├── 委托不足（消息链）
│   └── Hide Delegate
├── 基本类型偏执
│   └── Replace Data Value with Object / Replace Type Code with Class
├── 条件逻辑复杂
│   ├── 单一函数内 → Decompose Conditional
│   ├── 嵌套过深 → Replace Nested Conditional with Guard Clauses
│   └── 按类型分支 → Replace Conditional with Polymorphism
└── 继承体系不合理
    ├── 共性在子类 → Pull Up Method/Field
    ├── 特性仅部分子类需要 → Push Down Method/Field
    ├── 继承滥用 → Replace Inheritance with Delegation
    └── 两个维度交叉 → Tease Apart Inheritance
```

> 完整 72 种重构手法详见 `references/refactoring_catalog.md`。

### 阶段4：执行 —— 小步前进

执行重构时遵循以下原则：

1. **每步只做一件事**——不要同时进行多项重构
2. **每步之后运行测试**——测试通过才继续下一步
3. **如果测试失败**——立即回退到上一个通过测试的状态
4. **如果不确定**——停下来，不要"猜测式"重构
5. **保留旧函数作为委托**——在确认所有调用点已更新后再删除

> "重构技术就是以微小的步伐修改程序。如果你犯下错误，很容易便可发现它。"

#### 重构变换输出格式

输出重构方案时，包含以下要素：

1. **坏味道诊断**：指出代码有什么问题（引用坏味道名称）
2. **重构手法选择**：列出将使用的手法及选择理由
3. **变换步骤**：逐步列出操作（第1步…第2步…第3步…）
4. **每步测试点**：在哪些步骤之后运行测试
5. **变换前/变换后代码**：用语言无关伪代码或目标语言代码展示

### 阶段5：验证 —— 确认行为不变

重构完成后：

1. **运行全部测试**——确认没有测试失败
2. **检查坏味道是否消除**——但不引入新的坏味道
3. **检查是否过度设计**——简单问题不需要复杂方案
4. **检查是否需要引入设计模式**——如果是，切换到 GoF Skill
5. **检查是否需要系统级调整**——如果是，切换到 Clean Architecture Skill

---

## 何时不该重构

| 情况 | 原因 | 替代方案 |
|------|------|---------|
| 代码根本不能正常运作 | 重构不如重写 | 从头重写 |
| 项目已近最后期限 | 重构收益在期限之后无法体现 | 记录技术债务，后续偿还 |
| 没有测试且无法添加测试 | 没有安全网的重构是赌博 | 先补测试，或用版本控制频繁提交 |
| 代码简单且一次性使用 | ROI 不值 | 保持原样 |

---

## 重构原则速查

| # | 原则 |
|---|------|
| 1 | 先测试，后重构 |
| 2 | 小步前进，每步编译测试 |
| 3 | 清楚自己戴着哪顶帽子（添加功能 vs 重构） |
| 4 | 不为重构而重构——重构服务于其他目标 |
| 5 | 事不过三，三则重构 |
| 6 | 消除重复代码是优秀设计的根本 |
| 7 | 代码应自我表达——人类可理解优先 |
| 8 | 重构不取代预先设计，而是与之互补 |
| 9 | 先写简单方案，需要时再重构为灵活方案 |
| 10 | 性能优化阶段才关注性能，重构时不必担心 |
| 11 | 测试最担心的部分，不追求完美测试 |
| 12 | 没把握就停下来，学习原路返回 |
| 13 | 重构的节奏：测试→小修改→测试→小修改 |

> 完整方法论详见 `references/refactoring_methodology.md`。

---

## 语言通用化说明

本书原版代码使用 Java，但重构手法本质是语言无关的。应用时的适配原则：

| 原书概念 | 通用化处理 |
|---------|-----------|
| 超类/子类 (superclass/subclass) | 父类型/子类型 (parent type / subtype) |
| switch 语句 | 分支选择逻辑（switch/match/条件链） |
| instanceof | 类型检查 / 模式匹配 |
| 编译 (compile) | 类型检查 (type check) / 构建 (build) |
| private/protected | 可见性控制 |
| JUnit | 测试框架（通用） |
| getter/setter | 访问函数 |
| delegation | 委托 |

核心原则：**保留"做什么"和"为什么"，泛化"怎么做"**。

---

## 参考资源

- `references/bad_smells.md`：22 种代码坏味道完整诊断指南，包含症状、诊断要点、修复手法、交叉引用表和使用说明
- `references/refactoring_catalog.md`：72 种重构手法完整目录，按类别分组，包含定义、动机、逐步做法、变换模式、选择决策树（含例外情况和平衡决策指导）
- `references/refactoring_methodology.md`：重构方法论与原则，包含定义、两顶帽子、为何重构、何时重构、测试先行、重构与设计/性能的关系、安全层次、为什么团队不重构、Movie Rental 示例引用
- `references/refactoring_example.md`：Movie Rental 完整重构示例（原书第1章），展示重构的节奏感和思考过程
- `references/skill_coordination.md`：三个 Skill（refactoring/go-architecture/clean-architecture）的协同使用场景和切换条件

> 建议在使用本 Skill 前，先阅读 `references/refactoring_example.md` 理解重构的节奏感。

当需要查询具体坏味道的详细诊断、特定重构手法的完整步骤、或重构方法论的具体原则时，加载相应的参考文件。
