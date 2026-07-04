# 代码坏味道诊断指南

> 来源：Martin Fowler《重构：改善既有代码的设计》第3章
> 22 种坏味道是重构的起点——先闻到味道，才知道哪里需要重构。

---

## 使用说明

**坏味道是启发式（heuristics），不是精确衡量标准。**

不同人对同一段代码是否有坏味道会有不同判断。以下原则是原书第3章开头强调的：

1. **相信你的嗅觉，但也要理性判断**——坏味道是信号，不是判决
2. **上下文决定一切**——在某些情况下，"坏味道"可能是合理的设计选择（见各条目中的"例外"说明）
3. **不要试图一次性消除所有坏味道**——先消除阻碍你前进的那个
4. **消除一个坏味道可能引入另一个**——这是正常的，重构是持续的过程

> 原书观点："所谓重构，是这样一个过程：在你不知道该保留哪些坏味道的时候，计算各种选择的成本。"

---

## 坏味道速查矩阵

> 注：部分坏味道名称做了本土化调整以更清晰表达含义，原书对应名称见各条目括号内标注。

| # | 坏味道 | 核心症状 | 首选重构手法 |
|---|--------|---------|-------------|
| 1 | 重复代码 | 相同结构出现在多个位置 | Extract Method, Pull Up Method |
| 2 | 过长函数 | 函数太长，难以理解 | Extract Method, Replace Temp with Query |
| 3 | 过大的类 | 一个类承担太多责任 | Extract Class, Extract Subclass |
| 4 | 过长参数列 | 参数太多，难以使用 | Introduce Parameter Object, Preserve Whole Object |
| 5 | 发散式变化 | 一个类因不同原因在不同方向变化 | Extract Class |
| 6 | 霰弹式修改 | 一种变化需要修改散布多处的代码 | Move Method, Move Field, Inline Class |
| 7 | 依恋情结 | 函数对另一个类的数据更感兴趣 | Move Method, Extract Method |
| 8 | 数据泥团 | 相同数据项总是一起出现 | Extract Class, Introduce Parameter Object |
| 9 | 基本类型偏执 | 过度使用基本类型而非小对象 | Replace Data Value with Object, Replace Type Code with Class |
| 10 | 分支惊悚现身（原书：switch惊悚现身） | 相同的 switch/match 散落多处 | Move Method, Replace Conditional with Polymorphism |
| 11 | 平行继承体系 | 加一个子类型必须同时加另一个 | Move Method, Move Field |
| 12 | 冗赘类 | 类存在但没做足够的工作 | Collapse Hierarchy, Inline Class |
| 13 | 夸夸其谈未来性 | 为"未来可能"预留没用的抽象 | Collapse Hierarchy, Inline Class, Remove Parameter |
| 14 | 令人迷惑的暂时字段 | 某些字段只在特定场景有效 | Extract Class, Introduce Null Object |
| 15 | 过度耦合的消息链 | 一长串 getter 链导航 | Hide Delegate, Extract Method, Move Method |
| 16 | 中间人 | 类的一半函数都在做委托转发 | Remove Middle Man, Inline Method |
| 17 | 狎昵关系 | 两个类过度互相访问内部细节 | Move Method, Move Field, Extract Class |
| 18 | 异曲同工的类 | 两个函数做同样的事但接口不同 | Rename Method, Move Method, Extract Superclass |
| 19 | 不完美的库类 | 依赖的库功能不足 | Introduce Foreign Method, Introduce Local Extension |
| 20 | 纯稚的数据类 | 类只有字段和 getter/setter，没有行为 | Encapsulate Field, Remove Setting Method, Move Method |
| 21 | 被拒绝的遗赠 | 子类型不需要继承的大部分功能 | Push Down Method, Replace Inheritance with Delegation |
| 22 | 过多的注释 | 注释存在是因为代码不够清晰 | Extract Method, Rename Method, Introduce Assertion |

---

## 坏味道分类

### 一、代码重复与冗余类

#### 1. Duplicated Code（重复代码）

**症状**：相同的程序结构出现在多个位置。

**为什么是问题**：修改时需要找到所有副本同步修改，容易遗漏。代码越长，正确的修改越困难。

**诊断要点**：
- 同一个类内两个函数含相同表达式 → Extract Method
- 两个兄弟子类型含相同表达式 → Extract Method + Pull Up Method
- 代码类似但不完全相同 → Extract Method 分离差异 + Form Template Method
- 两个不相关的类出现重复 → Extract Class 提取到独立类

**修复手法**：Extract Method, Substitute Algorithm, Extract Class, Pull Up Method, Form Template Method

---

#### 12. Lazy Class（冗赘类）

**症状**：类存在但没有做足够的工作来证明其存在的价值。

**为什么是问题**：维护一个类是有成本的。如果一个类的存在只是为了少数几个简单函数，或者经过重构后变得几乎为空，它的维护成本就超过了价值。

**诊断要点**：
- 经过重构后类变得几乎为空 → Collapse Hierarchy 或 Inline Class
- 创建时过度设计，从未真正使用 → 同上

**修复手法**：Collapse Hierarchy, Inline Class

---

#### 13. Speculative Generality（夸夸其谈未来性）

**症状**：为"未来可能需要"而预留了没用到的抽象、钩子、参数。

**为什么是问题**：过度抽象增加理解成本。如果这些抽象从未被使用，它们就在浪费维护者的注意力。

**诊断要点**：
- 有没被使用的抽象类或接口 → Collapse Hierarchy
- 有没被调用的函数参数 → Remove Parameter
- 有只被测试代码调用的"扩展点" → 重新评估是否真需要

**修复手法**：Collapse Hierarchy, Inline Class, Remove Parameter

> **与 GoF Skill 的设计哲学文档呼应**：过度设计警告——"一个相对简单的系统功能也许只需要几个类就能实现"。

---

### 二、代码长度与复杂度

#### 2. Long Method（过长函数）

**症状**：函数过长，难以理解和维护。

**为什么是问题**：函数越长越难理解。短函数更有价值：复用机会更大、高层读起来像注释、更容易覆写。

**诊断要点**：
- 关键不在于函数长度，而在于"做什么"和"如何做"之间的语义距离
- 需要注释来说明的地方 → 考虑提炼成独立函数
- 条件表达式和循环常常是提炼信号
- 临时变量太多阻碍提炼 → 先用 Replace Temp with Query

**修复手法**：Extract Method, Replace Temp with Query, Replace Method with Method Object, Decompose Conditional, Preserve Whole Object, Introduce Parameter Object

> **Fowler 原则**："每当感觉需要以注释来说明点什么的时候，就把需要说明的东西写进一个独立函数中，并以其用途命名。"

---

#### 3. Large Class（过大的类）

**症状**：单个类承担了太多责任，往往有太多字段或太多代码。

**为什么是问题**：字段过多 → 代码重复；代码过多 → 混乱和死亡。

**诊断要点**：
- 字段有相同前缀/后缀 → 可能可以提炼为独立类
- 并非所有时刻都使用所有字段 → 考虑 Extract Subclass
- 类内函数有大量相同代码 → 先提炼再组织

**修复手法**：Extract Class, Extract Subclass, Extract Interface, Duplicate Observed Data

---

#### 4. Long Parameter List（过长参数列）

**症状**：函数参数列表过长，难以使用和理解。

**为什么是问题**：参数列太长难以理解，前后不一致，且一旦需要更多数据就必须修改签名。

**诊断要点**：
- 可通过已有对象的方法获取 → Replace Parameter with Method
- 多个参数来自同一对象 → Preserve Whole Object
- 数据缺乏合理归属 → Introduce Parameter Object
- 例外：刻意避免与较大对象的依赖关系时，传值参数是合理的

**修复手法**：Preserve Whole Object, Replace Parameter with Method, Introduce Parameter Object

---

### 三、变化模式问题

#### 5. Divergent Change（发散式变化）

**症状**：一个类因不同原因在不同方向上变化。

**为什么是问题**：修改时需要在一个类中跳来跳去处理不相关的变化。应该让"外界变化"与"需要修改的类"一一对应。

**诊断要点**：
- "加新数据库要改这三个函数；加新金融工具要改那四个函数" → 拆分类
- 与 SRP（单一职责原则）直接对应

**修复手法**：Extract Class

---

#### 6. Shotgun Surgery（霰弹式修改）

**症状**：一种变化需要修改散布在多个类中的代码。

**为什么是问题**：修改点散布四处，很难找到所有需要修改的地方，容易遗漏。

**诊断要点**：
- 与 Divergent Change 互为反面：Divergent Change 是"一个类受多种变化影响"，Shotgun Surgery 是"一种变化引发多个类修改"
- 理想状态：外界变化与需要修改的类一一对应

**修复手法**：Move Method, Move Field, Inline Class

---

#### 7. Feature Envy（依恋情结）

**症状**：函数对另一个类的数据/方法兴趣超过对自己所属类的兴趣。

**为什么是问题**：违背了对象技术的核心——将数据和对数据的操作打包在一起。

**诊断要点**：
- 函数从另一个对象调用大量 getter → 把函数移到那个对象
- 函数只用部分数据对另一个类感兴趣 → 先 Extract Method 再 Move Method
- 例外：Strategy 和 Visitor 模式故意将行为与数据分离，这是对抗 Divergent Change 的手段

**修复手法**：Move Method, Extract Method

---

#### 10. Switch Statements（分支惊悚现身）

**症状**：相同的 switch/match/条件链散落在多个地方。

**为什么是问题**：面向对象程序应少用 switch。switch 的问题在于重复——加一个 case 要找到所有 switch 并修改。

**诊断要点**：
- switch 根据类型码选择 → 用多态替代
- 只在单一函数中选择且不想改动 → Replace Parameter with Explicit Methods
- 选择条件之一是 null → Introduce Null Object

**修复手法**：Extract Method, Move Method, Replace Type Code with Subclasses, Replace Conditional with Polymorphism, Introduce Null Object, Replace Parameter with Explicit Methods

---

#### 11. Parallel Inheritance Hierarchies（平行继承体系）

**症状**：为一个类添加子类型时必须同时为另一个类添加子类型。类名前缀相同是信号。

**为什么是问题**：Shotgun Surgery 的特殊情况。修改一处继承体系要同步修改另一处。

**诊断手法**：让一个继承体系的实例引用另一个继承体系的实例，然后逐步 Move Method 和 Move Field。

**修复手法**：Move Method, Move Field

---

### 四、数据与类型问题

#### 8. Data Clumps（数据泥团）

**症状**：相同的数据项总是一起出现在字段、参数中。

**为什么是问题**：绑在一起出现的数据应该拥有自己的对象。

**诊断要点**：
- 删掉其中一项，其他是否失去意义？如果是 → 提炼为对象
- 提炼后可进一步发现 Feature Envy 机会

**修复手法**：Extract Class, Introduce Parameter Object, Preserve Whole Object

---

#### 9. Primitive Obsession（基本类型偏执）

**症状**：过度使用基本类型（数字、字符串）而非小对象来表示概念。

**为什么是问题**：像金额（数值+币种）、范围（起始+结束）、电话号码（特殊字符串）等概念应该有自己的类型，而非散落在基本类型中。

**诊断要点**：
- 单独的数值 → Replace Data Value with Object
- 类型码 → Replace Type Code with Class / Subclasses / State-Strategy
- 数组中挑选数据 → Replace Array with Object

**修复手法**：Extract Class, Replace Data Value with Object, Replace Array with Object, Replace Type Code with Class/Subclasses/State-Strategy, Introduce Parameter Object

---

#### 14. Temporary Field（令人迷惑的暂时字段）

**症状**：对象的某些字段只在特定场景下有效，其他时候为 null 或无意义。

**为什么是问题**：字段并非总是有效会让代码阅读者迷惑。

**诊断要点**：
- 字段只在某个算法中有效 → Extract Class 提取为独立对象
- 字段有时为 null → Introduce Null Object

**修复手法**：Extract Class, Introduce Null Object

---

#### 20. Data Class（纯稚的数据类）

**症状**：类只有字段和 getter/setter，没有行为。

**为什么是问题**：数据类是纯粹的数据容器，行为散落在其他类中（通常是 Feature Envy 的来源）。

**诊断要点**：
- 先 Encapsulate Field 和 Encapsulate Collection 封装数据
- 然后 Move Method 把行为搬移到数据类中
- 移除不必要的 setter（Remove Setting Method）——字段不可变更好

**修复手法**：Encapsulate Field, Encapsulate Collection, Remove Setting Method, Move Method

---

### 五、关系与封装问题

#### 15. Message Chains（过度耦合的消息链）

**症状**：客户端通过一长串 getter 调用导航对象关系（`a.getB().getC().getD()`）。

**为什么是问题**：客户端与导航结构紧密耦合。对象间关系变化时，客户端必须修改。

**诊断要点**：
- 先看消息链最终得到的对象用来做什么
- 用 Extract Method 把使用代码提炼为函数，再 Move Method 推入链中
- 或用 Hide Delegate 隐藏委托

**修复手法**：Hide Delegate, Extract Method, Move Method

---

#### 16. Middle Man（中间人）

**症状**：类的一半函数都在做简单的委托转发。

**为什么是问题**：过度委托 = 过度间接层。封装是有价值的，但如果类只是转发，它的存在价值存疑。

**诊断要点**：
- 大量"不干实事"的委托函数 → Remove Middle Man，直接和真正负责的对象打交道
- 少数委托 → Inline Method 放入调用端
- 中间人还有其他行为 → Replace Delegation with Inheritance

**修复手法**：Remove Middle Man, Inline Method, Replace Delegation with Inheritance

---

#### 17. Inappropriate Intimacy（狎昵关系）

**症状**：两个类过度互相访问对方的内部细节。

**为什么是问题**：过度亲密的类耦合太紧，变化时会互相影响。

**诊断要点**：
- Move Method 和 Move Field 帮它们划清界限
- 如果有共同点 → Extract Class 提取到安全地点
- 双向关联变单向 → Change Bidirectional Association to Unidirectional
- 继承造成的亲密 → Replace Inheritance with Delegation

**修复手法**：Move Method, Move Field, Extract Class, Hide Delegate, Change Bidirectional Association to Unidirectional, Replace Inheritance with Delegation

---

#### 18. Alternative Classes with Different Interfaces（异曲同工的类）

**症状**：两个函数做同样的事但签名不同。

**为什么是问题**：重复但接口不统一，无法替换使用。

**诊断要点**：
- Rename Method 统一命名
- 反复 Move Method 直到协议一致
- 如果需要大量重复移入 → Extract Superclass

**修复手法**：Rename Method, Move Method, Extract Superclass

---

#### 19. Incomplete Library Class（不完美的库类）

**症状**：依赖的库功能不足，但无法修改库代码。

**为什么是问题**：库作者无法预知所有需求，但调用方有扩展需求。

**诊断要点**：
- 只需修改一两个函数 → Introduce Foreign Method（外加函数）
- 需要添加大量行为 → Introduce Local Extension（本地扩展）

**修复手法**：Introduce Foreign Method, Introduce Local Extension

---

#### 21. Refused Bequest（被拒绝的遗赠）

**症状**：子类型不需要继承的大部分功能。

**为什么是问题**：继承体系设计错误——子类型只想用父类型的一部分。

**诊断要点**（原书 nuanced 观点）：
- **拒绝继承父类型的"实现"**——这一点我们不介意（子类型复用父类型接口但覆写实现是正常现象）
- **拒绝继承父类型的"接口"**——我们不以为然（子类型不想实现父类型的某些接口，说明继承体系设计有误）
- 即使你不愿意继承接口，也不要胡乱修改继承体系——应该先考虑用 Replace Inheritance with Delegation
- 气味不浓时可不理睬（如果子类型只额外使用了父类型的一两个方法，代价不大的话可以接受）

**修复手法**：Push Down Method, Push Down Field, Replace Inheritance with Delegation

---

### 六、代码表达问题

#### 22. Comments（过多的注释）

**症状**：注释过多，往往说明代码本身不够清晰。

**为什么是问题**：注释不是坏味道，但常被当作"除臭剂"使用。注释存在是因为代码不够好。

**诊断要点**：
- 需要注释解释代码做了什么 → Extract Method（让函数名解释）
- 函数已提炼但还需注释 → Rename Method（让名字解释）
- 需要注释说明系统需求 → Introduce Assertion（用断言表达约束）

> "当你感觉需要撰写注释时，请先尝试重构，试着让所有注释都变得多余。"

**修复手法**：Extract Method, Rename Method, Introduce Assertion

---

## 坏味道交叉引用表

| 坏味道 | 最常配合的设计模式 | 最常配合的重构链 |
|--------|-------------------|-----------------|
| 重复代码 | Template Method | Extract Method → Pull Up Method |
| 过长函数 | — | Extract Method → Replace Temp with Query → Replace Conditional with Polymorphism |
| 过大的类 | Facade, Strategy | Extract Class → Extract Interface |
| 分支惊悚 | State, Strategy, Factory Method | Move Method → Replace Type Code with State/Strategy → Replace Conditional with Polymorphism |
| 发散式变化 | — | Extract Class（按变化原因拆分） |
| 霰弹式修改 | — | Move Method + Move Field（按变化聚拢） |
| 基本类型偏执 | Value Object | Replace Data Value with Object → Replace Type Code with Class |
| 数据类 | — | Encapsulate Field → Move Method（搬入行为） |

> **与 GoF Skill 的关系**：当坏味道修复过程中发现需要引入设计模式时（如分支惊悚 → State/Strategy），切换到 gof-architecture-advisor 获取模式选择指导。
