# SOLID 设计原则 + 迪米特法则

> 来源：结合 GoF《设计模式》与刘韬《秒懂设计模式》第25章"终道"
> 定位：设计模式的"内功心法"——先练招式（23种模式），再修内功（六大原则）

---

## 总览

| 原则 | 英文 | 一句话 | 反例（秒懂风格） |
|------|------|--------|------------------|
| **单一职责** | SRP | 一个类只干一件事 | 能打电话的鞋子 |
| **开闭原则** | OCP | 对扩展开放，对修改封闭 | 集成度太高的主板 |
| **里氏替换** | LSP | 子类可以替换父类而不出错 | 鸵鸟不能飞 |
| **接口隔离** | ISP | 接口要小而精，不要大而全 | 兔子不发声 |
| **依赖倒置** | DIP | 依赖抽象，不依赖具体 | 总经理→IT经理→程序员 |
| **迪米特法则** | LoD | 只和直接朋友说话 | 游戏机+手柄（黑盒子） |

---

## 一、单一职责原则（SRP: Single Responsibility Principle）

### 核心思想

> 一个类应该有且只有一个引起它变化的原因。
> 如果一个类承担了多项职责，那么任一项职责的变化都可能影响到其他职责。

### 反例："能打电话"的鞋子

一双鞋子的职责是穿在脚上走路。如果某天老板说"这双鞋再加个打电话的功能"，你在鞋底装了个通讯模块——当"走路"功能需要修改鞋底材质时，很可能影响到"打电话"的天线布局。

**一个模块的职责越多，它被复用的可能性越低。**

### 代码反例

```java
// 违反 SRP：灯泡类既负责发光，又负责报表统计
class Light {
    public void turnOn() { /* 发光 */ }
    public void turnOff() { /* 熄灭 */ }
    public void generateReport() { /* 统计开关次数，生成报表 */ }
    public void blink() { /* 闪烁 */ }
}
```

当产品经理说"把报表格式从 PDF 改成 Excel"时，你修改的是 Light 类——而这个类同时还在控制灯光。一旦改出 Bug，灯都不会亮了。

### 重构方案

```java
// 符合 SRP
class Light {  // 只有一个职责：控制灯光
    public void turnOn() { }
    public void turnOff() { }
    public void blink() { }
}

class LightReporter {  // 只有一个职责：统计报表
    private Light light;
    public void generateReport() { }
}
```

### 判断标准

问自己一个问题：**"这个类的用户是谁？他们为什么需要这个类发生变化？"** 如果答案是"多个不同角色的用户"，就说明职责太多了。

### 关联模式

- **Facade**：当子系统已经 SRP 良好但接口太多时，Facade 提供统一入口而不破坏SRP
- **Observer**：分离数据变化（Subject）和展现刷新（Observer）两种职责
- **Strategy**：将算法从使用算法的类中分离出来

---

## 二、开闭原则（OCP: Open-Closed Principle）

### 核心思想

> 软件实体（类、模块、函数等）应该对扩展开放，对修改封闭。
> 当需求变化时，应该通过新增代码来实现新功能，而不是修改已有的稳定代码。

### 反例：集成度太高的主板

一块把所有元器件都焊死的主板——想加个显卡？不行，换整块主板。想升级内存？不行，换整块主板。

好的设计应该像组装台式机：主板留下 PCIe 插槽、内存插槽、USB 接口——扩展时只需插入新组件，不用换主板。

### 代码反例

```java
// 违反 OCP：每加一种新图形就要改 GraphicEditor
class GraphicEditor {
    public void draw(Shape s) {
        if (s instanceof Circle) {
            drawCircle((Circle) s);
        } else if (s instanceof Rectangle) {
            drawRectangle((Rectangle) s);
        }
        // 加三角形 → 必须改这个类！
    }
}
```

### 重构方案（使用多态 + Strategy）

```java
interface Shape {
    void draw();
}

class Circle implements Shape { public void draw() { /* 画圆 */ } }
class Rectangle implements Shape { public void draw() { /* 画矩形 */ } }
// 加三角形：新建 Triangle implements Shape，GraphicEditor 不用改
```

### 关联模式

- **Strategy**：OCP 的最佳实践——通过封装变化算法来关闭修改、开放扩展
- **Decorator**：在不修改原有类的前提下动态添加功能
- **Template Method**：通过钩子操作给子类扩展点，父类本身保持对修改的封闭
- **Visitor**：在不修改元素类的前提下增加新操作

---

## 三、里氏替换原则（LSP: Liskov Substitution Principle）

### 核心思想

> 子类型必须能够替换掉它们的基类型。
> 换句话说：所有引用基类的地方，必须能透明地使用其子类的对象。

### 反例："鸵鸟不会飞"

```java
// 违反 LSP
class Bird {
    public void fly() { /* 飞翔 */ }
}

class Ostrich extends Bird {
    @Override
    public void fly() {
        throw new UnsupportedOperationException("我是鸵鸟，我不会飞！");
        // 调用者: fly() → 炸了！
    }
}
```

调用者写 `bird.fly()` 时期望所有鸟都能飞，但鸵鸟打破了这种契约。问题的根源不是鸵鸟（它确实不会飞），而是**抽象错了**——不是所有鸟都会飞。

### 重构方案

```java
// 符合 LSP：把 fly 从 Bird 中拆出去
class Bird {
    // 所有鸟的共性：有羽毛、会下蛋……
}

interface Flyable {
    void fly();
}

class Sparrow extends Bird implements Flyable {
    public void fly() { /* 麻雀飞 */ }
}

class Ostrich extends Bird {
    // 鸵鸟不会飞，不实现 Flyable
}
```

### 核心约束

1. **子类不能强化前置条件**（父类方法接受 int，子类不能要求只接受正数）
2. **子类不能弱化后置条件**（父类方法保证返回非 null，子类不能返回 null）
3. **子类不能改变父类的不变式**（父类保证 size >= 0，子类必须也保证）

### 关联模式

- **Strategy / State**：不通过继承而是通过组合来改变行为，天然避免 LSP 问题
- **Decorator**：透明围栏——装饰者和被装饰者具有相同接口，满足 LSP

---

## 四、接口隔离原则（ISP: Interface Segregation Principle）

### 核心思想

> 不应该强迫客户依赖于它们不用的方法。
> 接口应该小而专，而不是大而全的"上帝接口"。

### 反例："兔子不发声"

```java
// 违反 ISP
interface Animal {
    void move();   // 所有动物都要移动
    void makeSound();  // 但兔子不会叫！
}

class Rabbit implements Animal {
    public void move() { /* 跳跳跳 */ }
    public void makeSound() {
        // 兔子不发声，这个方法对我没用但还是得实现
        // 空方法是一个坏信号
    }
}
```

当你想换个新的 Rabbit 实现时，明明只关心 move，却被迫把 makeSound 也带上。

### 重构方案

```java
// 符合 ISP：把大接口拆成小接口
interface Movable {
    void move();
}

interface Audible {
    void makeSound();
}

class Rabbit implements Movable {
    public void move() { /* 跳跳跳 */ }
    // 兔子不需要实现 Audible
}

class Dog implements Movable, Audible {
    public void move() { /* 跑 */ }
    public void makeSound() { /* 汪汪 */ }
}
```

### ISP 与 SRP 的区别

| | 单一职责 SRP | 接口隔离 ISP |
|---|---|---|
| 关注对象 | **类** | **接口** |
| 关注问题 | 一个类做了太多不同的事 | 一个接口太大了 |
| 解决方法 | 把类拆成两个 | 把接口拆成两个 |

它们经常一起出现：一个违反 SRP 的类，往往也对外暴露了一个违反 ISP 的"大接口"。

### 关联模式

- **Adapter**：在不修改 Adaptee 的前提下，为不同客户提供专用的窄接口
- **Facade**：对子系统提供简化的接口，而不是暴露所有内部接口

---

## 五、依赖倒置原则（DIP: Dependency Inversion Principle）

### 核心思想

> 高层模块不应该依赖于低层模块。两者都应该依赖于抽象。
> 抽象不应该依赖于细节。细节应该依赖于抽象。

### 类比："总经理→IT部门经理→程序员"的组织架构

不好的组织架构：
> 总经理直接指挥每个程序员 → 总经理依赖"张三是写 Java 的"这个细节
> 一旦张三离职换了李四，总经理的工作模式也要变

好的组织架构：
> 总经理 → IT部门经理（抽象接口）→ 程序员（具体实现）
> 总经理只需要知道"我找 IT 部门经理"，不关心具体是谁在写代码

### 代码反例

```java
// 违反 DIP：高层模块直接依赖低层具体类
class GeneralManager {
    private JavaProgrammer zhangSan = new JavaProgrammer();  // 依赖具体类！
    
    public void buildSystem() {
        zhangSan.writeJavaCode();  // 张三离职？改代码！
    }
}
```

### 重构方案

```java
// 符合 DIP：都依赖抽象
interface Programmer {
    void writeCode();
}

class JavaProgrammer implements Programmer {
    public void writeCode() { /* 写 Java */ }
}

class GoProgrammer implements Programmer {
    public void writeCode() { /* 写 Go */ }
}

class ITManager {  // 抽象中间层
    private Programmer programmer;  // 依赖抽象，不依赖具体人
    public ITManager(Programmer p) { this.programmer = p; }
    public void deliver() { programmer.writeCode(); }
}

class GeneralManager {
    private ITManager itManager;  // 依赖抽象中间层
    public void buildSystem() { itManager.deliver(); }
}
```

### 与 GoF "针对接口编程"的区别

GoF 的"针对接口编程"是 DIP 的具体应用。DIP 更进一步：
- **谁拥有接口？** 接口应该属于高层模块（客户），而不是低层模块（实现者）
- **控制反转**：低层模块通过实现高层定义的接口来"被依赖"

### 关联模式

- **Abstract Factory** + **Factory Method**：创建层面的 DIP——客户依赖抽象工厂，不依赖具体产品类
- **Bridge**：将抽象和实现分离到两个独立层次，是 DIP 的架构级应用
- **Strategy**：让 Context 依赖 Strategy 接口而非具体算法

---

## 六、迪米特法则（LoD: Law of Demeter）

### 核心思想

> 一个对象应该对其他对象有尽可能少的了解。
> 又名"最少知识原则"：只和你的直接朋友说话，不要和"朋友的朋友"说话。

### 类比："游戏机+手柄"

游戏机是一个**黑盒子**（你不知道主板电路怎么工作），你通过**手柄/信号线**与它交互（你只知道那几个按钮）。

你不会拆开游戏机直接去按主板上的触点——那是"和陌生人说话"。

### 代码反例

```java
// 违反 LoD：链式调用穿透多层
class GameConsole {
    private CPU cpu;
    public CPU getCPU() { return cpu; }
}

class CPU {
    private Memory memory;
    public Memory getMemory() { return memory; }
}

// 调用者：直接访问了 3 层内部结构
int data = gameConsole.getCPU().getMemory().read(0x1000);
// gameConsole → CPU → Memory：太深的耦合链
```

如果哪天 GameConsole 换了新款 CPU 而 CPU 不再直接暴露 Memory，所有类似代码都要改。

### 重构方案

```java
// 符合 LoD：每个对象只暴露必要的"信号线"
class GameConsole {
    private CPU cpu;
    // 不暴露 CPU！而是暴露高层操作
    public int readMemory(int address) {
        return cpu.readMemory(address); // 内部转发
    }
}

// 调用者只和 GameConsole "说话"
int data = gameConsole.readMemory(0x1000);
```

### 四个"只和谁说话"

一个方法内部，只应该调用以下四类对象的方法：
1. **自身**（this）
2. **方法的参数对象**
3. **方法内部创建的对象**
4. **自身的成员对象**

不应该调用：通过方法返回值获得的"陌生对象"的方法。

### 关联模式

- **Facade**：提供一个"话筒"让客户只和 Facade 说话，不直接喊子系统内部
- **Mediator**：所有同事只和中介者说话，互相之间不直接对话
- **Observer**：Subject 只知道有 Observer 接口，不知道具体 Observer 类的细节

---

## 七大原则与 23 种模式的关系矩阵

| 原则 | 最直接体现的模式 |
|------|-----------------|
| SRP | Facade（子系统职责分离）、Observer（数据与展现分离） |
| OCP | Strategy（算法扩展）、Decorator（功能扩展）、Template Method（步骤扩展） |
| LSP | Decorator（透明围栏）、Strategy（接口契约一致） |
| ISP | Adapter（为不同客户提供专用接口）、Facade（简化暴露接口） |
| DIP | Abstract Factory（依赖抽象工厂）、Bridge（抽象与实现解耦） |
| LoD | Facade（单点通话）、Mediator（集中通信） |
| 组合优于继承 | Decorator、Strategy、Bridge、Composite |

---

## 实战速查：看到什么坏味道用什么原则

| 坏味道 | 违反的原则 | 优先检查 |
|--------|-----------|---------|
| 一个类的注释写着 "This class does A, B, and C" | SRP | 拆分类 |
| 每次加新功能都要改一堆现有类 | OCP | 引入接口/策略 |
| 子类的 override 方法抛出 UnsupportedOperationException | LSP | 重构继承层次 |
| 接口有 15 个方法，子类只实现了其中 3 个 | ISP | 拆分接口 |
| import 语句全是具体类名，没有接口/抽象类 | DIP | 引入抽象 |
| `a.getB().getC().doSomething()` | LoD | 增加委托方法 |
