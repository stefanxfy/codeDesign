# GoF 设计模式 — 完整总结

> 来源：《设计模式：可复用面向对象软件的基础》(Design Patterns: Elements of Reusable Object-Oriented Software)
> 作者：Erich Gamma, Richard Helm, Ralph Johnson, John Vlissides (GoF)
> 中文版译者：李英军等

---

## 一、设计模式分类表 (来自 §1.5 组织编目)

设计模式按**目的**（创建型/结构型/行为型）和**范围**（类/对象）两个准则进行分类：

| | **创建型** | **结构型** | **行为型** |
|---|---|---|---|
| **类** | Factory Method (3.3) | Adapter (4.1, 类适配器) | Interpreter (5.3)<br>Template Method (5.10) |
| **对象** | Abstract Factory (3.1)<br>Builder (3.2)<br>Prototype (3.4)<br>Singleton (3.5) | Adapter (4.1, 对象适配器)<br>Bridge (4.2)<br>Composite (4.3)<br>Decorator (4.4)<br>Facade (4.5)<br>Flyweight (4.6)<br>Proxy (4.7) | Chain of Responsibility (5.1)<br>Command (5.2)<br>Iterator (5.4)<br>Mediator (5.5)<br>Memento (5.6)<br>Observer (5.7)<br>State (5.8)<br>Strategy (5.9)<br>Visitor (5.11) |

- **创建型类模式**将对象的部分创建工作延迟到子类，**创建型对象模式**将实例化委托给另一个对象。
- **结构型类模式**使用继承机制来组合类，**结构型对象模式**描述对象的组装方式。
- **行为型类模式**使用继承描述算法和控制流，**行为型对象模式**描述一组对象怎样协作完成任务。

---

## 二、23个设计模式意图一览 (来自 §1.4 设计模式的编目)

1. **Abstract Factory (抽象工厂)** — 提供一个创建一系列相关或相互依赖对象的接口，而无须指定它们具体的类。
2. **Adapter (适配器)** — 将一个类的接口转换成客户希望的另外一个接口。
3. **Bridge (桥接)** — 将抽象部分与它的实现部分分离，使它们都可以独立地变化。
4. **Builder (生成器)** — 将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。
5. **Chain of Responsibility (职责链)** — 解除请求的发送者和接收者之间的耦合，使多个对象都有机会处理这个请求。
6. **Command (命令)** — 将一个请求封装为一个对象，支持请求的参数化、排队、日志和撤销。
7. **Composite (组合)** — 将对象组合成树形结构以表示"部分-整体"的层次结构，使客户对单个对象和组合对象的使用具有一致性。
8. **Decorator (装饰)** — 动态地给一个对象添加一些额外的职责。比生成子类方式更为灵活。
9. **Facade (外观)** — 为子系统中的一组接口提供一个一致的界面，定义了一个高层接口。
10. **Factory Method (工厂方法)** — 定义一个用于创建对象的接口，让子类决定将哪一个类实例化。
11. **Flyweight (享元)** — 运用共享技术有效地支持大量细粒度的对象。
12. **Interpreter (解释器)** — 给定一个语言，定义它的文法的表示，并定义一个解释器来解释语言中的句子。
13. **Iterator (迭代器)** — 提供一种方法顺序访问一个聚合对象中的各个元素，而又不暴露该对象的内部表示。
14. **Mediator (中介者)** — 用一个中介对象来封装一系列的对象交互，使各对象耦合松散。
15. **Memento (备忘录)** — 在不破坏封装性的前提下，捕获一个对象的内部状态，并在对象之外保存这个状态。
16. **Observer (观察者)** — 定义对象间的一对多的依赖关系，当一个对象的状态改变时所有依赖者都得到通知。
17. **Prototype (原型)** — 用原型实例指定创建对象的种类，并且通过拷贝这个原型来创建新的对象。
18. **Proxy (代理)** — 为其他对象提供一个代理以控制对这个对象的访问。
19. **Singleton (单件)** — 保证一个类仅有一个实例，并提供一个访问它的全局访问点。
20. **State (状态)** — 允许一个对象在其内部状态改变时改变它的行为。
21. **Strategy (策略)** — 定义一系列的算法，把它们一个个封装起来，并且使它们可相互替换。
22. **Template Method (模板方法)** — 定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。
23. **Visitor (访问者)** — 表示一个作用于某对象结构中的各元素的操作，使你可以在不改变各元素类的前提下定义新操作。

---

## 三、面向对象设计核心原则 (来自 §1.6)

### 3.1 基本原则

**原则一：针对接口编程，而不是针对实现编程。**

不将变量声明为某个特定的具体类的实例对象，而是让它遵从抽象类所定义的接口。这极大地减少子系统实现之间的相互依赖关系。

**原则二：优先使用对象组合，而不是类继承。**

- **白箱复用（继承）**：父类的内部细节对子类可见，编译时静态定义，破坏封装性。
- **黑箱复用（组合）**：对象内部细节不可见，运行时动态定义，不破坏封装性。
- 委托（delegation）是一种组合方法，使组合具有与继承同样的复用能力。

### 3.2 重要的设计概念

1. **寻找合适的对象**：设计模式帮你确定并不明显的抽象，如 Strategy 描述可互换的算法族，State 将实体的每个状态描述为一个对象。

2. **决定对象的粒度**：Facade 描述用对象表示完整子系统，Flyweight 支持大量最小粒度对象，Abstract Factory 和 Builder 产生专门负责生成其他对象的对象。

3. **指定对象接口**：对象接口与其实现分离，动态绑定和多态是核心概念。设计模式通过确定接口的主要组成成分及经接口发送的数据类型来帮助定义接口。

4. **描述对象的实现**：
   - 区分类继承（代码和表示的共享）与接口继承（描述何时可以替代）
   - 抽象类的主要目的是为子类定义公共接口
   - 抽象操作在抽象类中定义但没有实现

5. **运用复用机制**：
   - 类继承（白箱复用）
   - 对象组合（黑箱复用）
   - 参数化类型/模板（泛型）
   - 委托（对象组合的特例）

6. **关联运行时和编译时的结构**：
   - **聚合**（aggregation）：一个对象拥有另一个对象或对其负责，生命周期相同。
   - **相识**（acquaintance）：一个对象仅仅知道另一个对象，松散的耦合关系。
   - 程序的运行时结构和编译时结构差别很大，设计模式显式记述了这种差别。

### 3.3 设计应支持变化 — 导致重新设计的原因及应对模式 (§1.6.7)

| # | 导致重新设计的原因 | 应对的设计模式 |
|---|---|---|
| 1 | 通过显式地指定一个类来创建对象 | Abstract Factory, Factory Method, Prototype |
| 2 | 对特殊操作的依赖 | Chain of Responsibility, Command |
| 3 | 对硬件和软件平台的依赖 | Abstract Factory, Bridge |
| 4 | 对对象表示或实现的依赖 | Abstract Factory, Bridge, Memento, Proxy |
| 5 | 算法依赖 | Builder, Iterator, Strategy, Template Method, Visitor |
| 6 | 紧耦合 | Abstract Factory, Command, Facade, Mediator, Observer, Chain of Responsibility |
| 7 | 通过生成子类来扩充功能 | Bridge, Chain of Responsibility, Composite, Decorator, Observer, Strategy |
| 8 | 不能方便地对类进行修改 | Adapter, Decorator, Visitor |

### 3.4 设计模式所支持的设计的可变方面

| 设计模式 | 可以独立变化的方面 |
|---|---|
| Abstract Factory | 产品对象家族 |
| Builder | 如何创建一个组合对象 |
| Factory Method | 被实例化的子类 |
| Prototype | 被实例化的类 |
| Singleton | 一个类的唯一实例 |
| Adapter | 对象的接口 |
| Bridge | 对象的实现 |
| Composite | 一个对象的结构和组成 |
| Decorator | 对象的职责，不生成子类 |
| Facade | 一个子系统的接口 |
| Flyweight | 对象的存储开销 |
| Proxy | 如何访问一个对象，该对象的位置 |
| Chain of Responsibility | 满足一个请求的对象 |
| Command | 何时、怎样满足一个请求 |
| Interpreter | 一个语言的文法及解释 |
| Iterator | 如何遍历、访问一个聚合的各元素 |
| Mediator | 对象间怎样交互、和谁交互 |
| Memento | 一个对象中哪些私有信息存放在该对象之外，以及在什么时候进行存储 |
| Observer | 多个对象依赖于另外一个对象，而这些对象如何保持一致 |
| State | 对象的状态 |
| Strategy | 算法 |
| Template Method | 算法中的某些步骤 |
| Visitor | 某些可作用于一个（组）对象上的操作，但不修改这些对象的类 |

---

## 四、创建型模式 (Creational Patterns) 详解

创建型设计模式抽象了实例化过程。它们帮助一个系统独立于如何创建、组合和表示它的那些对象。

**核心主题**：
1. 将关于系统使用哪些具体类的信息封装起来
2. 隐藏这些类的实例是如何被创建和放在一起的
3. 给予在**什么**被创建、**谁**创建它、**怎样**创建以及**何时**创建等方面的灵活性

---

### 4.1 Abstract Factory (抽象工厂) — 对象创建型模式

**别名**：Kit

**意图**：提供一个接口以创建一系列相关或相互依赖的对象，而无须指定它们具体的类。

**动机**：
考虑一个支持多种视感（look-and-feel）标准的用户界面工具包（如 Motif 和 Presentation Manager）。为保证视感风格之间的可移植性，应用不应为特定视感硬编码它的窗口组件。定义一个抽象的 WidgetFactory 类，声明用来创建每一类基本窗口组件的接口。具体的工厂子类（MotifWidgetFactory、PMWidgetFactory）实现这些接口，返回特定风格的产品。客户仅通过抽象接口与工厂交互。

**适用性**：
- 一个系统要独立于它的产品的创建、组合和表示
- 一个系统要由多个产品系列中的一个来配置
- 要强调一系列相关的产品对象的设计以便进行联合使用
- 提供一个产品类库，但只想显示它们的接口而不是实现

**结构 / 参与者**：
- **AbstractFactory (WidgetFactory)**：声明创建抽象产品对象的操作接口
- **ConcreteFactory (MotifWidgetFactory, PMWidgetFactory)**：实现创建具体产品对象的操作
- **AbstractProduct (Window, ScrollBar)**：为一类产品对象声明接口
- **ConcreteProduct (MotifWindow, MotifScrollBar)**：定义具体产品，实现 AbstractProduct 接口
- **Client**：仅使用 AbstractFactory 和 AbstractProduct 声明的接口

**效果**：
- 优点：分离了具体的类；易于交换产品系列；有利于产品的一致性
- 缺点：难以支持新种类的产品（需要修改接口及所有子类）

**关键实现要点**：
1. 工厂通常作为单件（Singleton）——每个产品系列只需一个 ConcreteFactory 实例
2. 创建产品常用工厂方法（Factory Method），也可使用原型（Prototype）
3. 可扩展工厂：用参数化方法避免接口膨胀，但损失类型安全
4. 基于原型的工厂在动态语言中更灵活

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 只有 1 个产品系列，未来也不会加 | **不需要 Abstract Factory**。用 Factory Method 或直接 new |
| 2-3 个产品系列，且系列内部一致性强（如 Windows/Mac/Linux 主题） | Abstract Factory 的标准用法 |
| 产品系列可能动态增加（插件机制） | 结合 **Prototype** 作为工厂，避免 ConcreteFactory 子类爆炸 |
| 每种产品只需要创建一次（如 UI 主题的按钮、滚动条） | 工厂类本身使用 **Singleton** |
| Java Spring 场景 | `@Autowired` + `@Qualifier` 替代手动 Abstract Factory；或用 `ObjectProvider` |

> **实务忠告**：Abstract Factory 是创建型模式中最重的一个。不要一开始就用它。GoF 的建议是**"以 Factory Method 开始"**，等到确定需要产品系列概念时再升级为 Abstract Factory。

**相关模式**：
- AbstractFactory 类通常用 Factory Method 实现，也可用 Prototype 实现
- 具体的工厂通常是 Singleton

---

### 4.2 Builder (生成器) — 对象创建型模式

**意图**：将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

**动机**：
一个 RTF (Rich Text Format) 文档阅读器应能将 RTF 转换为多种文本格式（ASCII、TeX、TextWidget）。用 TextConverter 对象配置 RTFReader，RTFReader 识别标记后发请求给 TextConverter 进行转换。每种转换器类将创建和装配复杂对象的机制隐藏在抽象接口后。转换器为生成器（Builder），阅读器为导向器（Director）。

**适用性**：
- 当创建复杂对象的算法应该独立于该对象的组成部分以及它们的装配方式时
- 当构造过程必须允许被构造的对象有不同的表示时

**结构 / 参与者**：
- **Builder (TextConverter)**：为创建 Product 对象的各个部件指定抽象接口
- **ConcreteBuilder (ASCIIConverter, TeXConverter, TextWidgetConverter)**：实现 Builder 接口，定义并跟踪创建的表示，提供检索产品的接口
- **Director (RTFReader)**：构造使用 Builder 接口的对象
- **Product (ASCIIText, TeXText, TextWidget)**：表示被构造的复杂对象

**效果**：
- 可以改变产品的内部表示
- 将构造代码和表示代码分开，提高模块性
- 对构造过程进行更精细的控制（一步一步构造）

**关键实现要点**：
1. Builder 类接口必须足够普遍，以支持各种具体生成器
2. 不同产品可能没有公共父类——由具体生成器生成的产品表示相差可能很大
3. Builder 中的缺省方法为空方法，便于子类只重定义感兴趣的操作

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 对象有 3 个以上构造参数，且参数之间有依赖/约束关系 | **Builder**（比 telescoping constructor 清晰得多） |
| 构造步骤固定，但每步的实现可替换（如 RTF→HTML vs RTF→Markdown） | 标准 Builder + Director |
| 对象的构造步骤不固定（如用户可以跳步骤地"定制"一辆车） | 省略 Director，让 Client 直接调用 Builder 的步骤方法 |
| 简单的 2-3 个可选参数的构造 | **不需要 Builder**，用构造器重载或静态工厂方法即可 |
| Java/Kotlin 场景 | Java: Lombok `@Builder` 注解自动生成；Kotlin: 语言级 named parameters + default values 天然替代 Builder |

> **实务忠告**：Builder 最有价值的场景是"构造参数繁多 + 参数之间有约束"。别为了 3 个参数就上 Builder——那属于过度设计。对应 GoF 警告："当被构造的对象较简单时，Builder 可能引入不必要的复杂性。"

**相关模式**：
- Abstract Factory 与 Builder 相似，但 Builder 着重于一步步构造复杂对象，最后一步返回产品；Abstract Factory 着重于多个系列的产品对象，产品立即返回
- Composite 通常由 Builder 生成

---

### 4.3 Factory Method (工厂方法) — 类创建型模式

**别名**：虚构造器 (Virtual Constructor)

**意图**：定义一个用于创建对象的接口，让子类决定实例化哪一个类。Factory Method 使一个类的实例化延迟到其子类。

**动机**：
框架使用抽象类定义和维护对象之间的关系。例如，Application 类负责管理 Document，但无法预测哪个 Document 子类将被实例化。解决方法：Application 子类重定义 CreateDocument 工厂方法，返回适当的 Document 子类对象。CreateDocument 是一个工厂方法。

**适用性**：
- 当一个类不知道它所必须创建的对象的类的时候
- 当一个类希望由它的子类来指定它所创建的对象的时候
- 当类将创建对象的职责委托给多个帮助子类中的某一个，并希望将代理者信息局部化时

**结构 / 参与者**：
- **Product (Document)**：定义工厂方法所创建的对象的接口
- **ConcreteProduct (MyDocument)**：实现 Product 接口
- **Creator (Application)**：声明工厂方法返回 Product 对象，可选定义缺省实现
- **ConcreteCreator (MyApplication)**：重定义工厂方法返回 ConcreteProduct 实例

**效果**：
- 不再将与特定应用有关的类绑定到代码中
- 为子类提供钩子（hook）——给子类一个机会提供对象的扩展版本
- 连接平行的类层次——如 Figure 和 Manipulator 层次
- 潜在缺点：客户可能仅为了创建特定 ConcreteProduct 就不得不创建 Creator 子类

**关键实现要点**：
1. 两种情况：Creator 是抽象类（强制子类实现）vs Creator 是具体类（提供缺省实现）
2. 参数化工厂方法：用一个参数标识要创建的对象种类，支持创建多种产品
3. 惰性初始化（lazy initialization）：构造器将产品初始化为 null，访问者首次访问时创建
4. 使用 C++ 模板避免创建子类：用 Product 类作为模板参数
5. C++ 中构造器不要调用工厂方法——ConcreteCreator 中该工厂方法还不可用

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 框架代码需要让用户扩展（如 Application 框架中的 createDocument） | **Factory Method**——最经典的用法 |
| 只有一个产品变体，未来也不会增加 | **不需要 Factory Method**，直接 new 更简单 |
| 不想创建 Creator 子类（避免类爆炸） | 用 **参数化工厂方法**（传一个参数标识产品类型） |
| Creator 类不想被继承（final class） | 用 **Prototype** 替代（不需要 Creator 子类） |
| Java Spring 场景 | `@Bean` 注解的方法就是工厂方法；或实现 `FactoryBean<T>` 接口 |

> **实务忠告**：Factory Method 是最轻量的创建型模式，GoF 建议把它作为**创建型模式的起点**。当你第一次想"我应该在哪儿创建这个对象？"时，先用 Factory Method。只有在后续需要更大灵活性时才升级为 Abstract Factory 或 Builder。

**相关模式**：
- Abstract Factory 经常用工厂方法实现
- 工厂方法通常在 Template Method 中被调用
- Prototype 不需要创建 Creator 的子类

---

### 4.4 Prototype (原型) — 对象创建型模式

**意图**：用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。

**动机**：
音乐编辑器中，GraphicTool（框架类）不知道如何创建特定应用的音符实例。解决方法：让 GraphicTool 通过拷贝/克隆一个已有 Graphic 子类实例（原型）来创建新 Graphic。只要所有 Graphic 子类支持 Clone 操作，GraphicTool 可以克隆所有种类。通过用不同原型初始化，每个 GraphicTool 实例产生不同的音乐对象。还可减少类的数目——使用不同位图和时延初始化的同一类实例表示不同音符。

**适用性**：
- 当一个系统应该独立于它的产品创建、构成和表示时
- 当要实例化的类是在运行时指定时（如动态装载）
- 为避免创建与产品类层次平行的工厂类层次时
- 当一个类的实例只能有几个不同状态组合中的一种时

**结构 / 参与者**：
- **Prototype (Graphic)**：声明一个克隆自身的接口
- **ConcretePrototype (Staff, WholeNote, HalfNote)**：实现克隆自身的操作
- **Client (GraphicTool)**：让一个原型克隆自身从而创建新对象

**效果**：
- 运行时增加和删除产品——比其他创建型模式更灵活
- 改变值以指定新对象——通过为对象变量指定值，无需定义新类
- 改变结构以指定新对象——支持深拷贝克隆复杂的用户自定义结构
- 减少子类的构造——不需要 Creator 类层次
- 用类动态配置应用——适用于动态装载类的运行时环境
- 主要缺陷：每个 Prototype 子类必须实现 Clone 操作，当存在循环引用时很困难

**关键实现要点**：
1. 使用原型管理器（Prototype Manager）——保持可用的原型注册表
2. 实现克隆操作：浅拷贝 vs 深拷贝问题。克隆结构复杂的原型通常需要深拷贝
3. 初始化克隆对象：在 Clone 操作后使用 Initialize 操作设定关键状态值
4. 在 C++ 中 Prototype 特别有用（类不是对象），而在 Smalltalk/Objective-C 中不太重要

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 创建成本高的对象（网络连接、数据库连接、复杂计算） | **Prototype + 原型管理器**（预创建原型池） |
| 对象状态组合多但每种组合的创建都类似 | 用不同初始值的原型来克隆，避免为每种组合创建子类 |
| 动态加载的插件/模块系统 | Prototype 是最佳选择——运行时注册原型，用的时候克隆 |
| 深拷贝很困难（循环引用、外部资源句柄） | **慎重**。考虑用 Builder 或其他创建方式替代 |
| JavaScript/TypeScript 场景 | `Object.create()` 和 `...spread` 就是原型模式的原生支持 |

> **实务忠告**：Prototype 的"隐藏成本"是每个子类必须实现 Clone/拷贝操作。在深拷贝场景下，对嵌套对象的递归拷贝容易出错。GoF 特别指出："当内部存在循环引用时，实现 Clone 可能非常困难。"

**相关模式**：
- Prototype 与 Abstract Factory 竞争但也一起使用——Abstract Factory 可存储被克隆的原型集合
- 大量使用 Composite 和 Decorator 的设计通常可从 Prototype 获益

---

### 4.5 Singleton (单件) — 对象创建型模式

**意图**：保证一个类仅有一个实例，并提供一个访问它的全局访问点。

**动机**：
对一个类来说只有一个实例很重要，如打印假脱机、文件系统、窗口管理器。全局变量使对象可被访问，但不能防止实例化多个对象。更好地：让类自身负责保存它的唯一实例，截取创建新对象的请求，提供访问该实例的方法。

**适用性**：
- 当类只能有一个实例而且客户可以从一个众所周知的访问点访问它时
- 当这个唯一实例应该是通过子类化可扩展的，并且客户应该无须更改代码就能使用扩展的实例时

**结构 / 参与者**：
- **Singleton**：定义 Instance 操作（静态成员函数/类方法）允许客户访问唯一实例；可能负责创建自己的唯一实例

**效果**：
- 对唯一实例的受控访问
- 缩小名字空间——避免全局变量污染
- 允许对操作和表示的精化——可以有子类
- 允许可变数目的实例——易于改变
- 比类操作更灵活——C++ 中静态成员函数不是虚函数，Singleton 的 Instance 可以是虚的

**关键实现要点**：
1. 保证唯一实例：将创建操作隐藏在静态成员函数后，使用惰性初始化
2. 构造函数为保护型（protected），防止直接实例化
3. 不要依赖全局/静态对象的自动初始化——可能无法保证只有一个实例、可能有依赖问题
4. 创建 Singleton 的子类：使用单件注册表（registry of singleton）实现灵活的运行时选择
5. C++ 中转换单元上全局对象构造器调用顺序未定义，因此单件间不能有依赖

**推荐变体与实务指南**：

| 实现方式 | 推荐度 | 适用场景 |
|---------|--------|---------|
| **饿汉式**（static final 字段，类加载时创建） | ⭐⭐⭐⭐⭐ | 大多数场景。简单、线程安全、无锁。Spring 容器的默认 Bean 作用域就是单例 |
| **静态内部类**（Holder 模式） | ⭐⭐⭐⭐ | 需要懒加载时的首选。利用 JVM 类加载机制保证线程安全，无锁 |
| **枚举（Enum）** | ⭐⭐⭐⭐ | Effective Java 推荐。天然防反射、防序列化攻击。适合"绝对只需要一个"的场景 |
| **双重检查锁定（DCL）** | ⭐⭐ | 仅在需要懒加载 AND 低版本 Java (<5) 时考虑。现代 JVM 中 volatile 语义复杂，易出错 |
| **懒汉式 synchronized** | ⭐ | 几乎不用。每次访问都加锁，性能极差。如果真需要懒加载 + 线程安全，用静态内部类 |

> **实务忠告**：**"大多数场景下用饿汉式就够。Spring 容器默认就是单例。"**（秒懂设计模式 2.4 节"大道至简"）。不要为了"万一以后要懒加载"就上复杂的 DCL——YAGNI 原则同样适用于模式的变体选择。

**相关模式**：
- 很多模式可使用 Singleton 实现，如 Abstract Factory、Builder、Prototype

---

### 创建型模式讨论 (来自 §3.6)

两种参数化系统的方法：
1. **生成子类**（Factory Method）：缺点是仅为了改变产品类就需要新子类，可能级联
2. **对象组合**（Abstract Factory、Builder、Prototype）：定义负责明确产品对象类的"工厂对象"

选择建议：
- 以 Factory Method 开始，简单且只需一个新操作
- 需要更大灵活性时向其他创建型模式演化
- Prototype 通常是最佳选择（仅需每个类实现 Clone，减少类的数目）
- Abstract Factory 仅在已有工厂类层次时更好

---

## 五、结构型模式 (Structural Patterns) 详解

结构型模式涉及如何组合类和对象以获得更大的结构。

---

### 5.1 Adapter (适配器) — 类/对象结构型模式

**别名**：包装器 (Wrapper)

**意图**：将一个类的接口转换成客户希望的另外一个接口。Adapter 模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。

**动机**：
绘图编辑器中，需要复用已有的 TextView 类来实现 TextShape 类，但 TextView 的接口与 Shape 不兼容。定义 TextShape 类来适配 TextView 接口和 Shape 接口。两种方法：①继承 Shape 接口和 TextView 实现（类适配器）；②将 TextView 实例作为 TextShape 的组成部分（对象适配器）。适配器还要负责提供被匹配类所没有的功能。

**适用性**：
- 你想使用一个已经存在的类，而它的接口不符合你的需求
- 你想创建一个可以复用的类，该类可以与其他不相关的类或不可预见的类协同工作
- （仅适用于对象 Adapter）你想使用一些已经存在的子类，但不可能对每一个都进行子类化

**结构 / 参与者**：
- **Target (Shape)**：定义 Client 使用的与特定领域相关的接口
- **Client (DrawingEditor)**：与符合 Target 接口的对象协同
- **Adaptee (TextView)**：定义一个已经存在的需要适配的接口
- **Adapter (TextShape)**：对 Adaptee 的接口与 Target 接口进行适配

**效果**：
- 类适配器：仅引入一个对象，不需要额外指针间接得到 Adaptee；可以重定义 Adaptee 部分行为；但不能匹配 Adaptee 及其所有子类
- 对象适配器：允许与多个 Adaptee 及子类同时工作；但重定义 Adaptee 行为较困难

**关键实现要点**：
1. C++ 中：Adapter 应以 public 方式继承 Target，以 private 方式继承 Adaptee
2. 可插入适配器（pluggable adapter）：使用抽象操作、代理对象或参数化模块
3. 双向适配器提供透明操作：一个对象同时适配两个接口
4. 适配器的工作量取决于 Target 与 Adaptee 接口的相似程度

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 集成第三方库 / 遗留代码，接口不兼容 | **对象适配器**（组合方式）。更灵活，能适配 Adaptee 及其所有子类 |
| 需要重定义 Adaptee 的部分行为，且 Adaptee 不大 | **类适配器**（多重继承方式）。效率更高（无中间对象），但语言需要支持多重继承（C++） |
| Java/Kotlin 场景（不支持多重继承） | 统一使用**对象适配器**。把 Adaptee 作为私用字段持有 |
| 适配工作量很大（Target 与 Adaptee 接口差异巨大） | 考虑是否应该用 **Facade** 重新设计接口，而不是硬适配 |
| 微服务/分布式场景 | API Gateway 就是 Adapter 的架构级应用 |

> **实务忠告**：Adapter 是"打补丁"模式——你手上有了两个不兼容的东西，Adapter 让它们能一起工作。如果你在**设计之初**就预见到接口会变，考虑用 **Bridge** 而不是事后用 Adapter。

**相关模式**：
- Bridge 结构与对象适配器类似，但 Bridge 在系统设计前使用，目的是分离接口和实现；Adapter 在系统设计后使用，目的是匹配已有接口
- Decorator 增强功能但不改变接口，支持递归组合
- Proxy 在不改变接口的条件下为另一个对象定义代理

---

### 5.2 Bridge (桥接) — 对象结构型模式

**别名**：Handle/Body

**意图**：将抽象部分与它的实现部分分离，使它们都可以独立地变化。

**动机**：
可移植的 Window 抽象：如果用继承实现 XWindow 和 PMWindow，则扩展 Window 类型或支持新平台都需要大量新类，且客户代码与平台耦合。Bridge 解决方法：将 Window 抽象和它的实现分别放在独立的类层次中。Window 接口（Window、IconWindow、TransientWindow）和一个独立的 WindowImp 层次（XWindowImp、PMWindowImp）。Window 操作使用 WindowImp 接口的抽象操作实现。

**适用性**：
- 不希望在抽象和实现之间有一个固定的绑定关系（如运行时实现可切换）
- 类的抽象及其实现都可通过生成子类扩充
- 对抽象的实现部分的修改应对客户不产生影响
- （C++）想对客户完全隐藏抽象的实现部分
- 有许多类要生成（"嵌套的泛化"）
- 想在多个对象间共享实现（如使用引用计数的 String 类）

**结构 / 参与者**：
- **Abstraction (Window)**：定义抽象类接口，维护指向 Implementor 的指针
- **RefinedAbstraction (IconWindow)**：扩充 Abstraction 定义的接口
- **Implementor (WindowImp)**：定义实现类接口，通常只提供基本操作
- **ConcreteImplementor (XWindowImp, PMWindowImp)**：实现 Implementor 接口

**效果**：
- 分离接口及其实现部分——运行时配置，降低编译依赖性
- 提高可扩充性——独立扩充 Abstraction 和 Implementor 层次
- 实现细节对客户透明——隐藏共享实现和引用计数机制

**关键实现要点**：
1. 仅有一个 Implementor 时无需抽象 Implementor 类——退化的 Bridge
2. 创建正确的 Implementor 对象：通过构造器参数、缺省+切换、或委托给 Factory 对象
3. 共享 Implementor 对象——Handle/Body 习惯用法，使用引用计数
4. 多重继承不能实现真正的 Bridge（依赖静态继承）

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 一个概念有两个独立变化的维度（如 Window + WindowImp、Shape + DrawingAPI） | **Bridge**——经典场景，两个维度独立演化 |
| 只有一个 Implementor 实现 | **退化 Bridge**：不需要抽象 Implementor 接口，直接在 Abstraction 中包含具体实现 |
| 需要在运行时切换实现（如切换数据库：MySQL → PostgreSQL） | Bridge + 构造器注入（或 DI 容器注入），运行时改变 Implementor 对象的引用 |
| "平台的抽象层"（如 JDBC 的 Driver 接口） | Bridge 是 JDBC、SLF4J 等日志门面的核心设计 |
| Implementor 对象需要被多个 Abstraction 共享（如 String 的引用计数实现） | Handle/Body 习惯用法——Bridge 的经典变体 |

> **实务忠告**：GoF 明确区分了 Bridge vs Adapter——"Bridge 在系统设计**开始时**使用，目的是分离接口和实现；Adapter 在系统设计**完成后**使用，目的是让已有接口协同工作。"如果你在设计阶段就问"这个维度会不会独立变化？"，就是在做 Bridge 思维。

**相关模式**：
- Abstract Factory 可用来创建和配置特定的 Bridge
- Adapter 帮助无关的类协同工作，在系统设计完成后使用；Bridge 在系统开始时使用

---

### 5.3 Composite (组合) — 对象结构型模式

**意图**：将对象组合成树形结构以表示"部分-整体"的层次结构。Composite 使得用户对单个对象和组合对象的使用具有一致性。

**动机**：
绘图编辑器中，用户可以组合多个简单组件形成较大组件。不使用 Composite 时需区别对待图元对象与容器对象。Composite 使用一个抽象类 Graphic，既代表图元又代表容器。子类 Line、Rectangle、Text 是叶结点（实现 Draw）；Picture 是 Composite，聚合 Graphic 对象，Draw 对每个子部件调用 Draw。Picture 可以递归组合其他 Picture。

**适用性**：
- 你想表示对象的部分-整体层次结构
- 你希望用户忽略组合对象与单个对象的不同，统一使用组合结构中的所有对象

**结构 / 参与者**：
- **Component (Graphic)**：为组合对象声明接口；实现共有接口的缺省行为；声明访问和管理子组件的接口
- **Leaf (Rectangle, Line, Text)**：在组合中表示叶结点对象，无子结点
- **Composite (Picture)**：定义有子部件的部件的行为；存储子部件；实现与子部件相关的操作
- **Client**：通过 Component 接口操纵组合部件

**效果**：
- 定义了包含基本对象和组合对象的类层次结构，支持递归
- 简化客户代码——一致地使用组合结构和单个对象
- 更容易增加新类型的组件
- 使设计更加一般化（可能难以限制组合中的组件类型）

**关键实现要点**：
1. 显式的父部件引用——简化遍历和管理，支持 Chain of Responsibility
2. 共享组件——当只有一个父部件时难以共享；Flyweight 可解决
3. 最大化 Component 接口——在 Leaf 和 Composite 之间提供尽可能多公共操作
4. 管理子部件操作声明位置：透明性（Component 中声明）vs 安全性（Composite 中声明）
5. 使用高速缓冲存储改善性能——缓存遍历/查找结果

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 文件系统、菜单导航、组织架构树 | **Composite**——"部分-整体"层次结构的天然表达 |
| 想让客户"不区分单个对象和组合对象" | Composite 的核心价值——透明性 |
| 只有一层嵌套（如文件夹>文件） | 可能**不需要抽象的 Component 类**，简单实现即可 |
| Composite 中需要限制子节点的类型 | 在 add/remove 操作中加入运行时类型检查 |
| 前端 UI 框架场景 | React/Vue 的组件树就是 Composite——父组件和子组件共享 render 接口 |
| 需要遍历 Composite 结构 | 结合 **Iterator** 提供统一遍历方式 |

> **实务忠告**：Composite 最大的陷阱是"让设计过于一般化"——透明性导致 Leaf 类被迫实现没有意义的 add/remove 方法。GoF 提供了两种选择：在 Component 中声明（透明但 Leaf 要多写空方法）vs 在 Composite 中声明（安全但丢失透明性）。**一般建议**：Component 中声明 + 提供默认抛异常的 add/remove，让 Leaf 不需要 override。

**相关模式**：
- 部件-父部件连接用于 Chain of Responsibility
- Decorator 经常与 Composite 一起使用
- Flyweight 共享组件但不能再引用父部件
- Iterator 可遍历 Composite
- Visitor 将分布在 Composite 和 Leaf 类中的操作局部化

---

### 5.4 Decorator (装饰) — 对象结构型模式

**别名**：包装器 (Wrapper)

**意图**：动态地给一个对象添加一些额外的职责。就增加功能来说，Decorator 模式相比生成子类更为灵活。

**动机**：
给用户界面组件添加特性（边框、滚动）。将组件嵌入另一个对象中（装饰），该装饰与组件接口一致，对客户透明。将客户请求转发给组件，在转发前后执行额外动作（如画边框）。透明性允许递归嵌套多个装饰，添加任意多功能。如：TextView + ScrollDecorator + BorderDecorator = 有边框和滚动条的文本显示。

**适用性**：
- 在不影响其他对象的情况下，以动态、透明的方式给单个对象添加职责
- 处理那些可以撤销的职责
- 当不能采用生成子类的方法进行扩充时（大量独立扩展导致子类爆炸，或类定义被隐藏）

**结构 / 参与者**：
- **Component (VisualComponent)**：定义一个对象接口，可以给这些对象动态添加职责
- **ConcreteComponent (TextView)**：定义一个可以添加职责的对象
- **Decorator**：维持指向 Component 对象的指针，定义与 Component 一致的接口
- **ConcreteDecorator (BorderDecorator, ScrollDecorator)**：向组件添加职责

**效果**：
- 比静态继承更灵活——运行时添加和分离职责，可重复添加特性
- 避免在层次结构高层的类有太多特征——"即用即付"方式添加职责
- Decorator 与它的 Component 不一样——对象标识不同，不应依赖对象标识
- 有许多小对象——难以学习和排错

**关键实现要点**：
1. 装饰对象接口必须与 Component 接口一致——所有 ConcreteDecorator 必须有公共父类
2. 仅需一个职责时可省略抽象 Decorator 类
3. 保持 Component 类简单——集中于定义接口而非存储数据
4. 改变对象外壳（Decorator）vs 改变对象内核（Strategy）——当 Component 很庞大时 Strategy 更好
5. Decorator 从外部改变组件，组件对装饰一无所知；Strategy 方式中组件知道可能的扩展

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 需要给对象动态添加/移除功能（如 UI 组件加边框、滚动条） | **Decorator**——最经典场景，比继承灵活得多 |
| 对象本身就很大、很复杂（如重量级的域对象） | 考虑用 **Strategy** 改变"内核"而非 Decorator 改变"外壳"——GoF 明确指出了这个选择 |
| 只有一个装饰层次（不需要递归嵌套） | 可省略抽象 Decorator 类，用一个直接包裹 Component 的类即可 |
| Java I/O 流：`new BufferedInputStream(new FileInputStream("a.txt"))` | Java I/O 库就是 Decorator 模式的教科书级应用 |
| Python/JS 场景 | Python: 装饰器语法 `@decorator` 与 GoF Decorator 是不同概念，注意区分。JS: 高阶函数和中间件模式天然支持装饰 |

> **实务忠告**：Decorator 和 Composite 经常**协同使用**。Decorator 可以看作"只有一个子组件的 Composite"。但 Decorator 的问题是"有许多小对象"——每个装饰层都是一个独立对象，调试和排错难度增加。**当你发现 Decorator 链超过 3 层时，重新审视是否应该用更简单的设计。**

**相关模式**：
- Adapter：Decorator 改变职责不改变接口；Adapter 给对象全新的接口
- Composite：Decorator 可视为退化的仅有一个组件的组合
- Strategy：Decorator 改变对象外表，Strategy 改变对象内核

---

### 5.5 Facade (外观) — 对象结构型模式

**意图**：为子系统中的一组接口提供一个一致的界面，Facade 模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。

**动机**：
编译子系统包含 Scanner、Parser、ProgramNode、BytecodeStream 等多个类。大多数编译器的用户不需要关心这些细节。引入 Compiler 类（Facade），定义编译器功能的统一接口。它不隐藏底层类，而是方便大多数程序员使用，同时对少数需要底层功能的人不隐藏这些功能。

**适用性**：
- 当你要为一个复杂子系统提供一个简单接口时
- 客户程序与抽象类的实现部分之间存在很大的依赖性时
- 需要构建层次结构的子系统时——定义每层的入口点

**结构 / 参与者**：
- **Facade (Compiler)**：知道哪些子系统类负责处理请求；将客户请求代理给适当的子系统对象
- **Subsystem classes (Scanner, Parser, ProgramNode 等)**：实现子系统功能；处理 Facade 指派的任务；没有 Facade 的相关信息

**效果**：
- 对客户屏蔽子系统组件，减少客户处理的对象数目
- 实现子系统与客户之间的松耦合关系
- 降低编译依赖性——子系统类改变时减少重编译工作
- 不限制使用子系统类——在易用性和通用性之间选择

**关键实现要点**：
1. 降低客户-子系统耦合度：用抽象类实现 Facade，具体子类对应不同子系统实现
2. 公共子系统类与私有子系统类：Facade 是公共接口的一部分但不是全部
3. Facade 可对参与者参数化以增加灵活性，但主要任务是为一般情况简化接口

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 复杂子系统（10+ 个类）需要给外部提供一个简单入口 | **Facade**——减少客户需要了解的对象数目 |
| 为分层架构的每一层定义入口 | 每层一个 Facade，层与层之间通过 Facade 通信（如 Service 层的 Facade） |
| 子系统本身的类也需要被高级用户直接访问 | Facade 不隐藏子系统类——它提供简化的入口，但同时也暴露完整能力 |
| 微服务/API 场景 | API Gateway / BFF（Backend for Frontend）就是 Facade 模式的架构级应用 |
| Facade 本身变得过于庞大（God Object） | **拆分**为多个子 Facade，每个负责子系统的一个方面 |

> **实务忠告**：Facade 是最"自由"的模式——它没有严格的类结构要求，核心就是"提供一个简单接口"。它和 Mediator 的区别常被混淆：**Facade 提供的是单向的简化接口**（客户→子系统），**Mediator 协调的是多向的交互**（同事之间）。大多数"Service"类本质上就是 Facade。

**相关模式**：
- Abstract Factory 可与 Facade 一起使用以提供子系统独立的方式创建子系统对象
- Mediator 与 Facade 相似但目的不同：Mediator 对同事之间的任意通信进行抽象；Facade 仅对子系统对象的接口进行抽象
- Facade 通常属于 Singleton 模式

---

### 5.6 Flyweight (享元) — 对象结构型模式

**意图**：运用共享技术有效地支持大量细粒度的对象。

**动机**：
文档编辑器用对象表示每个字符会耗费大量内存。Flyweight 是共享对象，可在多个场景中使用。关键概念：**内部状态**（存储于 flyweight 中，独立于场景，可共享）和**外部状态**（取决于场景，不可共享，由用户对象传递）。为每个字母创建一个 flyweight，存储字符代码（内部状态），位置和排版风格由外部提供。即使是大文档也只需约 100 个字符对象。

**适用性**（全部成立时使用）：
- 应用程序使用了大量对象
- 完全由于使用大量对象造成很大的存储开销
- 对象的大多数状态都可变为外部状态
- 如果删除对象的外部状态，可以用相对较少的共享对象取代很多组对象
- 应用程序不依赖于对象标识

**结构 / 参与者**：
- **Flyweight (Glyph)**：描述接口，接受并作用于外部状态
- **ConcreteFlyweight (Character)**：实现 Flyweight 接口，存储内部状态，必须是可共享的
- **UnsharedConcreteFlyweight (Row, Column)**：并非所有 Flyweight 子类都需要共享
- **FlyweightFactory**：创建并管理 flyweight 对象，确保合理共享
- **Client**：维持对 flyweight 的引用；计算或存储外部状态

**效果**：
- 传输、查找和/或计算外部状态会产生运行时开销，但空间节省抵消这些开销
- 存储节约由以下因素决定：共享带来的实例减少数、对象内部状态的平均数、外部状态是计算还是存储的
- 使用外部状态的计算时间换取存储空间

**关键实现要点**：
1. 删除外部状态：识别外部状态并将其从共享对象中删除
2. 管理共享对象：FlyweightFactory 使用关联存储查找，可能需引用计数和垃圾回收
3. 当 flyweight 数目固定且很小（如 ASCII 码）时，可永久保存，无需引用计数

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 系统有海量细粒度对象（10万+），且大部分状态可外部化 | **Flyweight**——时间换空间，空间节省通常远大于运行时开销 |
| 字符编辑器（每个字符一个对象） | 经典 Flyweight 场景：26个字母的 Flyweight 对象即可表示整篇文档 |
| 对象数量没有达到"海量"级别 | **不需要 Flyweight**。现代 JVM 的 GC 和内存管理已经足够高效，小规模不需要 |
| 连接池、线程池 | 是 Flyweight 的变体——共享"重型"对象而不是大量创建和销毁 |
| 内部/外部状态的划分很难 | **放弃 Flyweight**。这个划分是 Flyweight 正确性的前提，划分错了不如不用 |

> **实务忠告**：Flyweight 是"优化"模式，不是"设计"模式。GoF 明确说了：**"使用 Flyweight 模式时需要全面衡量，只有当以下所有条件都成立时才使用……"**（适用性列出了 5 个全部成立的前提）。现代硬件条件下，大多数应用不需要 Flyweight。不要为了"看起来高级"而去用它。

**相关模式**：
- 通常和 Composite 结合——用共享叶结点的有向无环图实现逻辑层次
- 最好用 Flyweight 实现 State 和 Strategy 对象

---

### 5.7 Proxy (代理) — 对象结构型模式

**别名**：Surrogate

**意图**：为其他对象提供一种代理以控制对这个对象的访问。

**动机**：
文档编辑器中，大型光栅图像创建开销很大，但打开文档时必须迅速。使用 ImageProxy 替代真正的 Image。只有当编辑器调用图像的 Draw 操作时，ImageProxy 才创建真正的图像。Proxy 事先存储图像尺寸，无需实例化即可响应格式化的尺寸请求。

**适用性**（常见情况）：
1. **远程代理**（Remote Proxy）：为一个对象在不同地址空间提供局部代表
2. **虚代理**（Virtual Proxy）：根据需要创建开销很大的对象
3. **保护代理**（Protection Proxy）：控制对原始对象的访问权限
4. **智能指引**（Smart Reference）：在访问对象时执行附加操作（引用计数、持久对象加载、锁定检查）

**结构 / 参与者**：
- **Proxy (ImageProxy)**：保存引用使代理可访问实体；提供与 Subject 相同的接口；控制对实体的存取，可能负责创建和删除
- **Subject (Graphic)**：定义 RealSubject 和 Proxy 的共用接口
- **RealSubject (Image)**：定义 Proxy 所代表的实体

**效果**：
- Remote Proxy 可隐藏对象存在于不同地址空间的事实
- Virtual Proxy 可进行最优化，如按需创建对象
- Protection Proxy 和 Smart Reference 允许访问时附加内务处理
- 可对用户隐藏 copy-on-write（写时复制）优化——拷贝延迟到对象被修改时

**关键实现要点**：
1. 重载 C++ 中的存取运算符 `->` 用于实现智能指针式代理
2. 使用 Smalltalk 中的 `doesNotUnderstand` 自动转发请求
3. Proxy 不一定需要知道实体的类型——可通过抽象接口处理所有实体

**推荐变体与实务指南**：

| 代理类型 | 场景 | 推荐做法 |
|---------|------|---------|
| **虚代理**（Virtual Proxy） | 延迟加载大对象（大图、大文件） | 最常用的代理类型。创建开销大且不一定使用时优先使用 |
| **远程代理**（Remote Proxy） | RPC、RMI、跨网络的对象访问 | 由 RPC 框架自动生成（gRPC stub、Feign client 等），通常不需要手写 |
| **保护代理**（Protection Proxy） | 权限控制、访问审计 | Spring Security 的 AOP 代理就是保护代理 |
| **智能指引**（Smart Reference） | 引用计数、资源自动释放、锁检查 | C++ `shared_ptr`、Java `try-with-resources` 本质是智能指引 |
| 缓存代理 | 将计算结果缓存，下次直接返回 | MyBatis 的二级缓存、Spring Cache 注解 |

> **实务忠告**：Proxy 和 Decorator 结构几乎相同，但**目的完全不同**。Proxy 控制访问（"能不能用？"），Decorator 增加功能（"怎么用更好？"）。如果你在犹豫用哪个，问自己：这个包装主要是**控制**行为（Proxy）还是**增强**行为（Decorator）。日常开发中虚代理和缓存代理是最常见的两种。

**相关模式**：
- Adapter：为适配对象提供不同接口；Proxy 提供相同的接口但控制访问
- Decorator：实现与 Proxy 类似，但目的不同——Decorator 添加功能，Proxy 控制访问

---

### 结构型模式讨论 (来自 §4.8)

**Adapter vs Bridge**：
- Adapter 解决两个已有接口不匹配问题，在类设计完后实施
- Bridge 分离抽象和实现以独立演化，在设计类之前实施
- Facade 定义新接口而非复用原有接口

**Composite vs Decorator vs Proxy**：
- Composite 和 Decorator 结构类似，都基于递归组合——但目的不同
- Composite 旨在构造统一处理多个相关对象的结构
- Decorator 旨在无需子类即可给对象添加职责
- Composite 和 Decorator 通常协同使用
- Proxy 类似 Decorator 但目的不同——提供替代者控制访问，不能动态添加/分离性质
- 所有三种模式可以结合使用（如 proxy-decorator、decorator-proxy）

---

## 六、行为型模式 (Behavioral Patterns) 详解

行为型模式涉及算法和对象间职责的分配，不仅描述对象或类的模式，还描述它们之间的通信模式。

---

### 6.1 Chain of Responsibility (职责链) — 对象行为型模式

**意图**：使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间的耦合关系。将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它为止。

**动机**：
图形用户界面中的上下文有关帮助机制。用户在界面任一部分点击获得帮助，从最特殊到最普遍的顺序组织帮助信息。提交帮助请求的对象（如按钮）不知道最终提供帮助的对象。解决方案：给多个对象处理请求的机会，请求沿对象链传递直至其中一个处理。隐式接收者概念。

**适用性**：
- 有多个对象可以处理一个请求，哪个对象处理该请求运行时自动确定
- 想在不明确指定接收者的情况下向多个对象中的一个提交请求
- 可处理一个请求的对象集合应被动态指定

**结构 / 参与者**：
- **Handler (HelpHandler)**：定义处理请求的接口；（可选）实现后继链
- **ConcreteHandler (PrintButton, PrintDialog)**：处理所负责的请求；可访问后继者；若不处理则转发
- **Client**：向链上的具体处理者提交请求

**效果**：
- 降低耦合度——对象仅需知道请求会被处理，不需要知道由谁处理
- 增强指派职责的灵活性——运行时动态增加/修改链
- 不保证被接受——请求可能到链末端都得不到处理

**关键实现要点**：
1. 实现后继者链：定义新链接或使用已有链接（如 Composite 中的父构件引用）
2. 连接后继者：Handler 维护后继，提供默认转发实现
3. 表示请求：硬编码操作调用（类型安全）、请求码（灵活但不安全）、请求对象（参数化封装）
4. 在 Smalltalk 中用 doesNotUnderstand 自动转发

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 请求需要依次尝试多个处理器（如异常处理、日志级别过滤） | **Chain of Responsibility**——解耦发送者和潜在的多个接收者 |
| 固定的处理器链在编译时就确定 | 在构造时配置链，运行时不变 |
| 处理器链需要运行时动态改变 | 提供 addHandler/removeHandler 方法 |
| 链的深度不超过 3 个处理器 | 考虑是否**可以不用链**——直接用 if-else 判断可能更清晰 |
| Java Web 场景 | Servlet Filter 链、Spring Security Filter Chain、Spring Interceptor 都是职责链 |

> **实务忠告**：职责链的隐患是"请求可能不被处理"——如果链上所有处理器都决定不处理，请求就丢失了。**务必在链的末端设置默认处理器**，或者在调用方处理超时/无响应的逻辑。

**相关模式**：
- 常与 Composite 一起使用——构件的父构件可作为其后继

---

### 6.2 Command (命令) — 对象行为型模式

**别名**：动作 (Action)，事务 (Transaction)

**意图**：将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化，对请求排队或记录请求日志，以及支持可撤销的操作。

**动机**：
用户界面工具箱中的按钮和菜单需要执行请求但不知道接收者和操作。解决方法：将请求变成 Command 对象。抽象 Command 类定义 Execute 操作接口。具体 Command 子类存储接收者引用，Execute 调用接收者的操作。支持 MacroCommand——执行一系列命令。解耦调用操作的对象和具有执行所需信息的对象。

**适用性**：
- 抽象出待执行的动作以参数化某对象（面向对象回调）
- 在不同时刻指定、排列和执行请求
- 支持取消操作（Unexecute）
- 支持修改日志（系统崩溃恢复）
- 用构建在原语操作上的高层操作构造系统（事务）

**结构 / 参与者**：
- **Command**：声明执行操作的接口
- **ConcreteCommand (PasteCommand, OpenCommand)**：将接收者绑定于动作；调用接收者操作实现 Execute
- **Client (Application)**：创建具体命令对象并设定接收者
- **Invoker (MenuItem)**：要求命令执行请求
- **Receiver (Document, Application)**：知道如何实施执行请求的操作

**效果**：
- 将调用操作的对象与知道如何实现该操作的对象解耦
- Command 是头等对象——可被操纵和扩展
- 可将多个命令装配成组合命令（MacroCommand 是 Composite 的实例）
- 增加新 Command 很容易，无需改变已有类

**关键实现要点**：
1. 命令对象的智能程度：仅确定接收者和动作 vs 自己实现所有功能（不需要额外接收者）
2. 支持撤销和重做：存储状态信息（接收者、操作参数、改变的值），使用历史列表
3. 避免撤销操作中的错误积累——可使用 Memento
4. 使用 C++ 模板：对不能撤销和不需要参数的命令，用类模板避免创建 Command 子类

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 需要 Undo/Redo 功能 | **Command + Memento**（Command 存储操作前状态）或 Command + 逆向操作 |
| 需要记录操作日志用于回放/审计 | Command 序列化 + 日志队列 |
| 简单的"点按钮执行动作" | 可能**不需要 Command**——回调函数/闭包更轻量。Command 的价值在于支持撤销/排队/日志 |
| 宏命令（一次执行多个命令） | Command + **Composite**（MacroCommand = Composite of Commands） |
| Java 场景 | `Runnable`、`Callable`、`ActionListener` 都是 Command 的变体 |

> **实务忠告**：Command 的核心价值不在"执行命令"（那只是一个回调），而在于"将请求变成一等对象"——这让请求可以被存储、排队、日志化、撤销。如果你只需要一个回调，用函数式接口（`Consumer<T>`, `Runnable`）就够了，不需要完整的 Command 模式。

**相关模式**：
- Composite 可用来实现宏命令
- Memento 可用来保持命令要撤销的状态
- 被放入历史列表前需被拷贝的 Command 起到 Prototype 的作用

---

### 6.3 Interpreter (解释器) — 类行为型模式

**意图**：给定一个语言，定义它的文法的一种表示，并定义一个解释器，这个解释器使用该表示来解释语言中的句子。

**动机**：
正则表达式匹配是常见问题。为每种模式构造特定算法不如使用通用解释器。解释器模式：为文法规则用类表示（如 RegularExpression 抽象类和 LiteralExpression、SequenceExpression 等子类）。每个正则表达式表示为抽象语法树。为每个子类定义 Interpret 操作，使用上下文参数传递当前状态。

**适用性**：
- 有一个语言需要解释执行，且可将句子表示为抽象语法树时
- 文法简单（复杂文法导致类层次庞大）
- 效率不是关键问题

**结构 / 参与者**：
- **AbstractExpression (RegularExpression)**：声明抽象的解释操作接口
- **TerminalExpression (LiteralExpression)**：实现与终结符相关的解释操作
- **NonterminalExpression (AlternationExpression, SequenceExpression, RepetitionExpression)**：为每条文法规则维护 AbstractExpression 实例变量；递归实现解释操作
- **Context**：包含解释器之外的全局信息
- **Client**：构建抽象语法树并调用解释操作

**效果**：
- 易于改变和扩展文法——使用继承
- 易于实现文法——类直接编写或生成
- 复杂文法难以维护——文法中每条规则至少一个类
- 增加新的解释表达式方式——如优美打印、类型检查；可考虑用 Visitor 避免修改文法类

**关键实现要点**：
1. 创建抽象语法树：不涉及语法分析，可用表驱动、递归下降或直接由 Client 提供
2. 定义解释操作：经常需要新解释器时，使用 Visitor 将解释放在独立访问者对象中
3. 与 Flyweight 共享终结符——终结结点不存储在语法树中位置信息

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 简单的 DSL、表达式求值器（如四则运算、逻辑表达式） | **Interpreter**——文法简单（< 10 条规则）时很好用 |
| 文法复杂或频繁变化 | **不要用 Interpreter**。考虑用 Parser Generator（ANTLR、JavaCC）或脚本引擎 |
| 效率是关键需求 | **不要用 Interpreter**。GoF 明确说"效率不是关键问题"时才用 |
| 需要多种解释方式（执行、打印、类型检查） | Interpreter + **Visitor**——将每种解释封装为 Visitor |
| 生产环境 | 极少直接使用 Interpreter。Hibernate HQL、Spring SpEL 等使用了解释器思想但实现了专用引擎 |

> **实务忠告**：Interpreter 是 GoF 23 种模式中**使用频率最低**的模式之一。GoF 明确指出："当文法非常复杂时……需要许多类；在这种情况下语法分析程序生成器是更好的选择。"大多数开发者在整个职业生涯中都不会直接用 Interpreter 模式。

**相关模式**：
- Composite：抽象语法树是 Composite 的实例
- Flyweight：共享抽象语法树中的终结符
- Iterator：解释器可用迭代器遍历结构
- Visitor：可用来在一个类中维护抽象语法树中各结点的行为

---

### 6.4 Iterator (迭代器) — 对象行为型模式

**别名**：游标 (Cursor)

**意图**：提供一种方法顺序访问一个聚合对象中的各个元素，而又不需要暴露该对象的内部表示。

**动机**：
列表应提供方法让别人访问其元素而不暴露内部结构，且可能需要不同遍历方式。迭代器模式：将对列表的访问和遍历分离到迭代器对象中。迭代器接口：First、Next、IsDone、CurrentItem。多态迭代：定义抽象 Iterator 和 AbstractList，具体迭代器子类与具体列表实现对应。使用 Factory Method（CreateIterator）创建合适迭代器。

**适用性**：
- 访问聚合对象的内容而无须暴露其内部表示
- 支持对聚合对象的多种遍历
- 为遍历不同的聚合结构提供统一接口（多态迭代）

**结构 / 参与者**：
- **Iterator**：定义访问和遍历元素的接口
- **ConcreteIterator**：实现迭代器接口，跟踪当前位置
- **Aggregate**：定义创建迭代器对象的接口
- **ConcreteAggregate**：实现创建迭代器的接口，返回 ConcreteIterator 适当实例

**效果**：
- 支持以不同的方式遍历聚合——只需替换迭代器实例
- 简化了聚合的接口——聚合本身不再需要遍历接口
- 在同一个聚合上可以有多个遍历——每个迭代器保持自己的状态

**关键实现要点**：
1. 谁控制迭代：外部迭代器（客户主动推进）vs 内部迭代器（迭代器控制，对每个元素施行操作）
2. 谁定义遍历算法：迭代器 vs 聚合（游标方式仅指示当前位置）
3. 迭代器健壮性：遍历时修改聚合可能导致遗漏或重复；健壮迭代器通过注册处理
4. C++ 中多态迭代器：使用 Proxy 作为中间代理保证自动删除（RAII）
5. 迭代器的特权访问：作为聚合的友元或使用 protected 操作
6. 组合对象的迭代：外部迭代器需存储 Composite 路径，内部迭代器更简单
7. 空迭代器（NullIterator）：边界条件处理，始终已完成遍历

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 需要多种遍历方式（正序、倒序、过滤、按条件跳过） | 多态 Iterator——每种遍历方式一个 Iterator 子类 |
| 简单的遍历，不需要隐藏内部结构 | **不需要 Iterator**。语言内置的 for-each 已经足够 |
| 在遍历过程中修改聚合体 | 需要**健壮迭代器**（Robust Iterator），使用注册/通知机制 |
| 遍历树形结构（如 Composite） | 使用内部迭代器更简单（迭代器控制遍历，回调处理每个元素） |
| Java 场景 | `Iterator` / `Iterable` 接口和 for-each 语法是 Iterator 模式的标准实现；Java 8+ 的 Stream 是更高级的替代 |

> **实务忠告**：Iterator 是已经被现代语言内置到语法层面的模式。如果语言提供了 for-each / for-in / Stream，直接使用它们。**只有在需要自定义遍历策略、或遍历自定义数据结构时才需要自己实现 Iterator。**

**相关模式**：
- Composite：迭代器常应用于递归结构
- Factory Method：多态迭代器靠 Factory Method 实例化适当子类
- Memento：常与迭代器一起使用，迭代器可使用 memento 捕获迭代状态

---

### 6.5 Mediator (中介者) — 对象行为型模式

**意图**：用一个中介对象来封装一系列的对象交互。中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。

**动机**：
图形用户界面中对话框的实现。窗口组件间存在依赖关系（如输入域为空时某按钮不可用）。不同对话框有不同依赖关系，无法直接复用窗口组件类。解决方法：将集体行为封装在单独的中介者对象中。中介者（如 FontDialogDirector）负责控制和协调一组对象间的交互，充当通信中转中心。对象仅知道中介者。

**适用性**：
- 一组对象以定义良好但复杂的方式通信，产生的相互依赖关系结构混乱且难以理解
- 一个对象引用其他很多对象并直接与这些对象通信，导致难以复用
- 想定制分布在多个类中的行为，而又不想生成太多子类

**结构 / 参与者**：
- **Mediator (DialogDirector)**：定义与各同事对象通信的接口
- **ConcreteMediator (FontDialogDirector)**：协调各同事对象实现协作行为；了解并维护各个同事
- **Colleague classes (ListBox, EntryField)**：每个同事类知道它的中介者对象；需要与其他同事通信时与中介者通信

**效果**：
- 减少了子类生成——改变行为只需生成 Mediator 子类，各 Colleague 可复用
- 将各 Colleague 解耦——可独立改变和复用
- 简化了对象协议——一对多代替多对多
- 对对象协作进行抽象——注意力从各自行为转移到交互
- 使控制集中化——中介者可能变得过于复杂

**关键实现要点**：
1. 当 Colleague 仅与一个 Mediator 工作时可不定义抽象 Mediator 类
2. Colleague-Mediator 通信：使用 Observer 模式（Mediator 作为 Observer）或特殊通知接口（Colleague 将自身作为参数传递）
3. 注意中介者复杂度——随着对话框复杂度增加，中介者可能变得难以维护

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| GUI 对话框中多个组件的联动（如筛选条件影响可选项） | **Mediator**——经典场景，把混乱的网状依赖集中管理 |
| 只有 2-3 个对象互相通信 | **不需要 Mediator**。直接引用即可，Mediator 的额外抽象不值得 |
| 一组对象的通信逻辑频繁变化 | Mediator 封装变化——修改交互逻辑只需改 ConcreteMediator |
| Colleague（同事对象）数量多、交互规则复杂 | **警惕 Mediator 本身变成 God Object**。如果中介者的代码超过 300 行，考虑拆分或引入多个子中介者 |
| 前端/移动端场景 | Redux Store、Vuex、BLoC 都是 Mediator 的现代变体——集中管理状态和交互逻辑 |

> **实务忠告**："使控制集中化——这可能会使中介者本身变得比它任何一个同事都复杂。它会变得难以维护。"（GoF 原文警告）Mediator 的代价是**将复杂性从分布式转移到集中式**。如果集中后的 Mediator 太复杂，说明可能需要多个 Mediator 各司其职。

**相关模式**：
- Facade 与中介者不同：Facade 对子系统进行抽象提供方便接口，协议是单向的；Mediator 提供协作行为，协议是多向的
- Colleague 可使用 Observer 与 Mediator 通信

---

### 6.6 Memento (备忘录) — 对象行为型模式

**别名**：Token

**意图**：在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。这样以后就可将该对象恢复到原先保存的状态。

**动机**：
图形编辑器中支持取消操作。ConstraintSolver 的公共接口不足以精确逆转它对其他对象的作用，且不能暴露内部结构给取消操作机制。Memonto 解决方法：ConstraintSolver（原发器）创建并返回存储当前状态的 Memento（SolverState）。Memonto 对其他对象"不可见"——只有原发器可以存取信息。取消操作时编辑器将 Memento 送回原发器恢复状态。

**适用性**：
- 必须保存一个对象在某个时刻的（部分）状态，以便以后恢复到先前状态
- 如果直接得到这些状态将暴露对象实现细节并破坏封装性

**结构 / 参与者**：
- **Memento (SolverState)**：存储原发器内部状态；有两个接口——窄接口（供管理者/Caretaker）和宽接口（供原发器/Originator）
- **Originator (ConstraintSolver)**：创建 Memento 记录当前内部状态；使用 Memento 恢复内部状态
- **Caretaker (undo mechanism)**：负责保存 Memento；不能对 Memento 内容进行操作或检查

**效果**：
- 保持封装边界——避免暴露只应由原发器管理却必须存储在原发器之外的信息
- 简化了原发器——由管理者负责管理请求的状态
- 使用备忘录可能代价很高——拷贝和存储大量信息
- 定义窄接口和宽接口——语言支持（C++ 用友元）
- 维护备忘录的潜在代价——管理者需删除维护的备忘录

**关键实现要点**：
1. 语言支持：C++ 中将 Originator 作为 Memento 的友元，宽接口为私有，窄接口为公共
2. 存储增量式改变——若创建和返回顺序可预测，可仅存储增量而非完整状态

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| Undo/Redo 功能，需要保存和恢复对象状态 | **Command + Memento**——Command 在执行前捕获 Memento，Undo 时从 Memento 恢复 |
| 保存的状态数据量很大 | 使用**增量式 Memento**——只存储变化的部分，而非完整状态快照 |
| 保存的状态很简单（几个字段） | 可能**不需要 Memento**——直接保存字段值的拷贝即可。Memento 的价值在于保护封装 |
| 需要支持多次 Undo | 使用 History 列表（Caretaker），每个 Command 执行前 Push Memento，Undo 时 Pop 并恢复 |
| C++ 场景 | 利用 `friend` 实现 Memento 的宽接口（给 Originator）和窄接口（给 Caretaker） |

> **实务忠告**：Memento 的隐藏成本是"保存状态可能代价高昂"——如果状态包含了大量数据结构的深拷贝，每次操作前的存储都会带来显著的时空开销。GoF 建议："如果创建备忘录的顺序是可以预测的，Memento 可以只存储增量改变。"

**相关模式**：
- Command：命令可使用备忘录维护可撤销操作的状态
- Iterator：备忘录可用于迭代（Dylan 的 Collection 状态对象）

---

### 6.7 Observer (观察者) — 对象行为型模式

**别名**：依赖 (Dependent)，发布-订阅 (Publish-Subscribe)

**意图**：定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。

**动机**：
图形用户界面中表格和柱状图使用不同表示描述同一应用数据。当用户改变表格信息时柱状图立即反映变化，反之亦然。Observer 模式：关键对象为目标（Subject）和观察者（Observer）。目标可有任意数目依赖它的观察者。目标状态改变时所有观察者得到通知，观察者查询目标使自身状态与目标同步。即发布-订阅机制。

**适用性**：
- 一个抽象模型有两个方面，其中一个依赖于另一方；封装在独立对象中以各自独立改变和复用
- 对一对象的改变需同时改变其他对象，而不知道具体有多少对象有待改变
- 一个对象必须通知其他对象，而又不能假定其他对象是谁

**结构 / 参与者**：
- **Subject**：知道它的观察者（任意多个）；提供注册和删除观察者的接口
- **Observer**：为需要获得通知的对象定义一个更新接口
- **ConcreteSubject**：将有关状态存入 ConcreteObserver；状态改变时向各观察者发出通知
- **ConcreteObserver**：维护指向 ConcreteSubject 的引用；存储应与目标一致的状态；实现 Update 接口

**效果**：
- 目标和观察者间抽象耦合——目标只知道有观察者，不知道具体类
- 支持广播通信——通知自动广播给所有登记对象，无需指定接收者
- 意外的更新——观察者不知道其他观察者的存在，可能引发级联更新；简单的更新协议不提供变化的具体细节

**关键实现要点**：
1. 创建目标到观察者的映射：显式保存 vs 关联查找（时间换空间）
2. 观察多个目标：扩展 Update 接口使观察者知道是哪个目标发送的通知
3. 谁触发更新：目标状态设定操作自动调用 Notify vs 客户负责在适当时机调用
4. 对已删除目标的悬挂引用：目标删除时通知观察者将引用复位
5. 在发出通知前确保目标状态自身一致——考虑使用 Template Method
6. 推模型（目标发送详细信息）vs 拉模型（目标仅发送最小通知，观察者显式查询）
7. 显式指定感兴趣改变——使用目标对象的"方面"（aspect）概念
8. 封装复杂更新语义——使用 ChangeManager（Mediator 的实例）

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 一对多的数据依赖（如数据模型改变 → 多个视图刷新） | **Observer**——最经典场景，数据模型（Subject）+ 视图（Observer） |
| 简单的通知，只有一个 Observer | 可能**不需要 Observer**——直接调用或回调更简单 |
| 推模型 vs 拉模型 | **推模型**：Subject 把数据推给 Observer（Observer 不用再查，但可能收到不需要的数据）；**拉模型**：Subject 只通知"变了"，Observer 自己拉取需要的数据（灵活但多了查询） |
| Java 8+ | 使用函数式接口替代 Observer 类：`Consumer<Event>`、`BiConsumer<>` 等，比 `java.util.Observer` 更灵活 |
| 事件总线/消息队列 | Guava EventBus、Spring ApplicationEvent、RabbitMQ/Kafka——Observer 的分布式变体 |

> **实务忠告**：Observer 最大的陷阱是"意外的更新"——一个 Observer 的 Update 触发另一个 Subject 的改变，引发级联更新。GoF 建议使用 **ChangeManager**（Mediator 的实例）来管理复杂的更新依赖关系。在简单场景下，确保 Update 方法中不修改 Subject 状态以避免无限循环。

**相关模式**：
- Mediator：通过 ChangeManager 封装复杂更新语义
- Singleton：ChangeManager 可使用 Singleton 模式

---

### 6.8 State (状态) — 对象行为型模式

**别名**：状态对象 (Objects for States)

**意图**：允许一个对象在其内部状态改变时改变它的行为。对象看起来似乎修改了它的类。

**动机**：
TCPConnection 对象根据当前状态（Established、Listening、Closed）对不同请求做出不同反应。State 模式：引入 TCPState 抽象类表示连接状态，各子类实现与特定状态相关的行为。TCPConnection 维护一个当前状态对象（TCPState 子类实例），将所有与状态相关的请求委托给这个状态对象。当连接状态改变时，TCPConnection 改变所使用的状态对象。

**适用性**：
- 一个对象的行为取决于它的状态，必须在运行时根据状态改变行为
- 一个操作中含有庞大的多分支条件语句，且这些分支依赖于该对象的状态

**结构 / 参与者**：
- **Context (TCPConnection)**：定义客户感兴趣的接口；维护一个 ConcreteState 实例定义当前状态
- **State (TCPState)**：定义接口以封装与 Context 特定状态相关的行为
- **ConcreteState subclasses (TCPEstablished, TCPListen, TCPClosed)**：每一子类实现与 Context 一个状态相关的行为

**效果**：
- 将与特定状态相关的行为局部化，将不同状态的行为分割开——比巨大的条件语句更好
- 使得状态转换显式化——为不同状态引入独立对象使转换更明确
- State 对象可被共享——如果无实例变量，各 Context 可共享一个 State 对象（适用 Flyweight）

**关键实现要点**：
1. 谁定义状态转换：Context 中固定准则 vs State 子类指定后继状态（更灵活但有依赖）
2. 基于表的替代方法：将输入映射到状态转换的表，规则性好但不够灵活
3. 创建和销毁 State 对象：按需创建（状态变化不频繁）vs 提前创建（状态变化频繁）
4. 动态继承：Self 等基于委托的语言直接支持（运行时改变委托目标）

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 对象的行为随内部状态改变，且状态变化频繁 | **State**——将每个状态的行为封装在独立的状态类中 |
| 只有 2-3 个简单状态，状态转换逻辑不复杂 | 可能**不需要 State**——用 if-else 或 switch 就够了。State 的额外类数量成本需要权衡 |
| 状态对象不需要实例变量（无状态） | 状态对象可以被**共享**（Flyweight + Singleton）——所有 Context 共用同一个 State 对象 |
| 状态转换由谁定义？ | Context 中定义（固定规则）更简单；State 子类中定义（每个状态知道自己的后继）更灵活 |
| React/Vue 等前端框架 | useState/useReducer、有限状态机库（XState）代替手动 State 实现 |

> **实务忠告**：State 和 Strategy 结构几乎相同，但意图不同。**State：状态自己知道何时切换以及切换到哪个状态**。**Strategy：策略不主动切换，由外部决定**。如果你看到 State 子类之间互相创建引用，那就是 State 的特征。

**相关模式**：
- Flyweight：解释了何时及怎样共享状态对象
- 状态对象通常是 Singleton

---

### 6.9 Strategy (策略) — 对象行为型模式

**别名**：政策 (Policy)

**意图**：定义一系列的算法，把它们一个个封装起来，并且使它们可相互替换。本模式使得算法可独立于使用它的客户而变化。

**动机**：
文本流分行有多种算法。将算法硬编进使用它们的类中会使客户复杂、难以维护。解决方法：定义类来封装不同换行算法（策略）。Composition 类维护一个 Compositor（策略）引用，将格式化职责转发给它。Compositor 子类：SimpleCompositor（简单策略）、TeXCompositor（全局优化）、ArrayCompositor（固定数目项）。

**适用性**：
- 许多相关类仅行为有异——用多个行为之一配置一个类
- 需要使用算法的不同变体
- 算法使用客户不应知道的数据——避免暴露复杂的算法相关数据结构
- 一个类定义多种行为，以多个条件语句形式出现

**结构 / 参与者**：
- **Strategy (Compositor)**：定义所有支持算法的公共接口
- **ConcreteStrategy (SimpleCompositor, TeXCompositor, ArrayCompositor)**：以 Strategy 接口实现具体算法
- **Context (Composition)**：用一个 ConcreteStrategy 对象配置；维护 Strategy 对象引用；可定义接口让 Strategy 访问数据

**效果**：
- 相关算法系列——继承析取公共功能
- 替代继承的方法——将行为从 Context 中分离，易于切换和理解
- 消除条件语句——将行为封装在独立的 Strategy 类中
- 实现的选择——根据不同时间/空间取舍选择策略
- 缺点：客户必须了解不同 Strategy；Strategy 和 Context 间的通信开销；增加了对象数目

**关键实现要点**：
1. 定义 Strategy 和 Context 接口：传送数据给 Strategy vs 将自身传递给 Strategy vs Strategy 存储 Context 引用
2. 将 Strategy 作为模板参数（C++）——编译时选择，运行时不可改变，更高效率
3. 使 Strategy 对象成为可选的——访问前检查是否存在，否则执行缺省行为
4. 共享的 Strategy 不应在各次调用之间维护状态——适用 Flyweight

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 需要在运行时切换算法/行为（如支付方式：微信/支付宝/银行卡） | **Strategy**——把算法封装成可替换的对象 |
| 大量 if-else 判断选择行为 | 用 Strategy 替代条件语句——每个分支变成一个 Strategy 类 |
| 只有 2 个策略，且未来不会增加 | 可能**不需要 Strategy**——一个 if-else 更简单直接 |
| 策略需要访问 Context 的内部数据 | 两种方式：Context 把数据传给 Strategy（耦合低），或 Strategy 持有 Context 引用（耦合高但灵活） |
| Java 8+ / Kotlin / TypeScript | 函数式接口（`Function<T,R>`, `Predicate<T>`）+ Lambda 表达式替代 Strategy 类层次 |

> **实务忠告**：Strategy 是最常用的行为型模式之一。GoF 指出主要缺点是"客户必须了解不同的 Strategy"——调用方需要知道有哪些策略以及何时用哪个。**用工厂模式（Factory Method）封装策略选择**可以消除这部分认知负担。另外，如果策略是无状态的，应作为 Flyweight 共享而非每次创建新对象。

**相关模式**：
- Flyweight：Strategy 对象经常是很好的轻量级对象

---

### 6.10 Template Method (模板方法) — 类行为型模式

**意图**：定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。Template Method 使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

**动机**：
Application 类的 OpenDocument 操作定义打开和读取文档的算法步骤：检查文档是否能打开、创建 Document 对象、加入文档集合、从文件读取。OpenDocument 是模板方法——用抽象操作定义算法，子类重定义这些操作提供具体行为。Application 子类定义 CanOpenDocument 和 DoCreateDocument，Document 子类定义 DoRead。

**适用性**：
- 一次性实现算法的不变部分，将可变行为留给子类实现
- 各子类中公共的行为应被提取并集中到公共父类中以避免代码重复
- 控制子类扩展——只在特定点调用钩子操作

**结构 / 参与者**：
- **AbstractClass (Application)**：定义抽象原语操作（子类重定义）；实现模板方法定义算法骨架（调用原语操作和其他操作）
- **ConcreteClass (MyApplication)**：实现原语操作以完成与特定子类相关的步骤

**效果**：
- 代码复用的基本技术——提取公共行为
- 导致反向控制结构——"好莱坞法则"（"别找我们，我们找你"）：父类调用子类操作
- 模板方法调用的操作类型：具体操作、具体 AbstractClass 操作、原语操作、Factory Method、钩子操作（缺省为空操作，子类可选扩展）
- 必须明确区分钩子操作（可重定义）和抽象操作（必须重定义）

**关键实现要点**：
1. C++ 访问控制：原语操作为保护成员；模板方法为非虚成员函数；必须重定义的原语操作为纯虚函数
2. 尽量减少原语操作——减少子类实现负担
3. 命名约定：给应被重定义的操作加前缀（如 MacApp 的 "Do-" 前缀）

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 算法骨架固定，但某些步骤需要子类定制 | **Template Method**——"好莱坞原则"：父类调子类，别反过来 |
| 需要运行时动态替换整个算法 | 用 **Strategy** 而不是 Template Method——Template Method 基于继承（静态），Strategy 基于组合（动态） |
| 原语操作过多（>5 个） | 考虑是否应该拆分成多个 Template Method 或改用 Strategy |
| 子类只想改变其中一个步骤 | 明确区分**钩子操作**（可选 override，默认空）和**抽象操作**（必须 override） |
| Java 场景 | Servlet `doGet/doPost`、Spring `JdbcTemplate`、JUnit `setUp/tearDown` 都是 Template Method |

> **实务忠告**：Template Method 的核心是"反向控制"——父类控制流程，子类填空。这是继承的**最合理**使用方式（也是 GoF 为数不多正面评价继承的场景）。它与 Strategy 的选择：**如果算法的"骨架"不变只是"步骤"变 → Template Method；如果整个"算法"都可以换 → Strategy。**

**相关模式**：
- Factory Method 常被模板方法调用
- Strategy：Template Method 使用继承改变算法的一部分；Strategy 使用委托改变整个算法

---

### 6.11 Visitor (访问者) — 对象行为型模式

**意图**：表示一个作用于某对象结构中的各元素的操作。它使你可以在不改变各元素的类的前提下定义作用于这些元素的新操作。

**动机**：
编译器在抽象语法树上需实施多种操作：类型检查、代码优化、流程分析、优美格式打印等。将所有这些操作分散到各种结点类中导致系统难以理解和维护。Visitor 解决方法：将每个类中相关的操作包装在独立 Visitor 对象中。遍历语法树时将 Visitor 传递给当前访问的元素。元素接受访问者时回调访问者的相应 Visit 操作。需要两个类层次：元素层次（Node）和访问者层次（NodeVisitor）。

**适用性**：
- 一个对象结构包含很多类对象，有不同的接口，想对它们实施一些依赖于其具体类的操作
- 需要对对象结构中的对象进行很多不同且不相关的操作，想避免这些操作"污染"对象的类
- 定义对象结构的类很少改变，但经常需要在此结构上定义新操作

**结构 / 参与者**：
- **Visitor (NodeVisitor)**：为 ConcreteElement 的每个类声明 Visit 操作
- **ConcreteVisitor (TypeCheckingVisitor)**：实现每个 Visit 操作，为算法提供上下文并累积结果
- **Element (Node)**：定义 Accept 操作，以访问者为参数
- **ConcreteElement (AssignmentNode, VariableRefNode)**：实现 Accept 操作
- **ObjectStructure (Program)**：能枚举元素；可提供高层接口允许访问者访问元素

**效果**：
- 易于增加新的操作——增加新 Visitor 子类即可
- 访问者集中相关操作而分离无关操作——简化元素类，算法数据结构隐藏在访问者中
- 增加新 ConcreteElement 类很困难——每个 Visitor 都要添加新抽象操作和实现
- 通过类层次进行访问——没有像迭代器那样要求所有元素有共同父类
- 累积状态——遍历结构中累积结果
- 破坏封装——常需提供访问元素内部状态的公共操作

**关键实现要点**：
1. 双分派（Double-Dispatch）技术：Accept 是 double-dispatch 操作——得到执行的操作取决于 Visitor 的类型和 Element 的类型
2. 谁负责遍历对象结构：对象结构中（每个元素的 Accept 遍历子构件）vs 访问者中 vs 独立迭代器对象
3. 在 C++ 中可用函数重载命名 Visit 操作，强调相同分析但不同参数

**推荐变体与实务指南**：

| 场景 | 推荐做法 |
|------|---------|
| 稳定的对象结构 + 频繁增加新操作（如 AST 的类型检查、代码格式化、代码分析） | **Visitor**——在不修改元素类的前提下增加新操作 |
| 元素类频繁变化（经常加新类型） | **不要用 Visitor**——每加一个新元素类，所有 Visitor 都要改 |
| 只有 1-2 个操作需要加到对象结构上 | 可能**不需要 Visitor**——直接把操作写在元素类里更简单 |
| 需要遍历对象结构 + 执行操作 | Visitor 内封装遍历逻辑 vs 对象结构自己遍历 + Accept |
| Java 场景 | ASM（字节码操作）、Eclipse JDT AST 的 ASTVisitor 都是 Visitor 的典型应用 |

> **实务忠告**：Visitor 是 GoF 23 种模式中**最复杂**的模式之一。它的核心权衡：**增加新操作很容易（新增一个 Visitor 子类），但增加新元素类很困难（所有 Visitor 都要改）**。GoF 的原话："当 ConcreteElement 类层次是稳定的，但你希望经常定义新的操作时，Visitor 非常有用。"反之，如果元素层次不稳定，请远离 Visitor。

**相关模式**：
- Composite：访问者可用于对 Composite 定义的对象结构进行操作
- Interpreter：访问者可用于解释

---

### 行为型模式讨论 (来自 §5.12)

**封装变化主题**：
- Strategy 封装一个算法
- State 封装与状态相关的行为
- Mediator 封装对象间的协议
- Iterator 封装访问和遍历聚合对象的方法

**对象作为参数**：
- Visitor 是多态 Accept 操作的参数
- Command 和 Memento 定义可作令牌传递的对象

**通信应被封装还是被分布**：
- Observer 分布通信——Observer 和 Subject 协作维护约束，粒细更易复用但通信流难理解
- Mediator 封装通信——集中管理约束，通信流更易理解

**对发送者和接收者解耦**：
- Command：Command 对象定义发送者和接收者绑定，支持解耦
- Observer：通过通知接口解耦，适用于数据依赖
- Mediator：通过中介者间接引用解耦，集中通信但类型安全性较低
- Chain of Responsibility：沿潜在接收者链传递请求解耦，灵活性最大但也不保证处理

---

## 七、设计模式之间的关系图

### 模式关系总结

| 模式 | 常与以下模式一起使用 | 可作为以下模式的替代 | 实现常使用 |
|---|---|---|---|
| Abstract Factory | Factory Method, Prototype | — | Singleton, Factory Method |
| Builder | Composite | Abstract Factory | — |
| Factory Method | Template Method | Prototype | — |
| Prototype | Composite, Decorator | Abstract Factory | Singleton |
| Singleton | — | — | — |
| Adapter | — | — | — |
| Bridge | Abstract Factory | Adapter | — |
| Composite | Iterator, Visitor, Chain of Responsibility | — | — |
| Decorator | Composite | — | — |
| Facade | Abstract Factory | Mediator | Singleton |
| Flyweight | Composite, State, Strategy | — | — |
| Proxy | — | Decorator | — |
| Chain of Responsibility | Composite | — | — |
| Command | Composite, Memento | — | Prototype |
| Interpreter | Composite, Flyweight, Visitor | — | — |
| Iterator | Composite, Memento | — | Factory Method |
| Mediator | Observer, Facade | — | — |
| Memento | Command, Iterator | — | — |
| Observer | Mediator, Singleton | — | — |
| State | Flyweight | — | Singleton |
| Strategy | Flyweight | Template Method | — |
| Template Method | Factory Method | Strategy | — |
| Visitor | Composite, Interpreter | — | — |

---

## 八、总结

GoF 23 个设计模式提供了面向对象设计中最经典的解决方案。记住以下关键原则：

1. **针对接口编程，而不是针对实现编程** —— 创建型模式帮助你做到这一点
2. **优先使用对象组合，而不是类继承** —— 结构型模式展示了如何做到这一点
3. **封装变化** —— 行为型模式的核心主题
4. **设计应支持变化** —— 每个模式都允许系统某个方面独立变化

在实际使用中，模式的选择取决于具体上下文：
- 设计初期通常以简单模式开始（Factory Method、Template Method、Composite）
- 需要更大灵活性时向更复杂的模式演化（Abstract Factory、Builder、Visitor）
- 多个模式经常协同工作，形成更大的设计整体
