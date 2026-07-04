# 面向对象基础：设计模式的先决知识

> 来源：刘韬《秒懂设计模式》第1章"初探"
> 定位：看不懂封装/继承/多态，就看不懂 23 种设计模式——先补课

---

## 开篇

设计模式（Design Patterns）是面向对象设计中的通用解决方案。但设计模式是建立在封装、继承、多态这三大支柱之上的。如果说不清什么是多态，那么 Decorator、Strategy、Observer 对你来说都是黑话。

这一章帮你用最短时间建立 OOP 的直觉。

---

## 一、封装（Encapsulation）：把复杂度藏起来

### 一句话定义

> 隐藏对象的内部状态和实现细节，只对外暴露必要的操作接口。

### 三层递进理解（从秒懂风格）

**第一层：可乐杯 + 吸管**

你喝可乐时，不需要知道杯子里可乐的化学成分、温度调节机制。你只需要一个**吸管**（接口）。吸管就是封装——它向你隐藏了可乐杯内部的复杂性。

**第二层：电脑机箱 + USB 接口**

你用电脑插 U 盘，不需要知道主板电路怎么传输数据。你只需要一个 **USB 接口**。机箱把主板、电源、硬盘全部封装在内部，只给你几个必要的接口：USB、HDMI、电源键。

**第三层：弹药 → 子弹 → 弹夹**

- 火药 + 弹头 + 底火 → 封装成**子弹**（你不需要操作火药）
- 多发子弹 → 封装成**弹夹**（你不需要逐发装填）
- 弹夹 + 枪 → 封装成**自动步枪**（扣扳机就行）

每一层封装都减少了上一层需要关心的细节。这就是**分层的封装**。

### 代码示例

```java
class BankAccount {
    private double balance;  // 内部状态：封装！外部不能直接访问

    public void deposit(double amount) {  // 暴露的操作接口
        if (amount > 0) {
            balance += amount;
        }
    }

    public void withdraw(double amount) {  // 暴露的操作接口
        if (amount > 0 && balance >= amount) {
            balance -= amount;
        }
    }

    public double getBalance() {  // 受控的访问
        return balance;
    }
}
```

直接暴露 `public double balance` 会出什么问题？任何人可以写 `account.balance = -100000`，银行业务全乱套。封装通过 `private` + 方法控制来防止非法操作。

### 封装与设计模式的关系

- 几乎所有设计模式都以封装为基础
- **Factory Method**：隐藏对象创建的细节
- **Facade**：隐藏子系统的复杂性
- **Memento**：保护原发器的内部状态不被外部访问

---

## 二、继承（Inheritance）：复用代码 + 建立"是"的关系

### 一句话定义

> 子类继承父类的属性和方法，可以在不修改父类的前提下扩展或重定义行为。
> **继承的核心是"is-a"关系：Dog is an Animal。**

### 代码示例

```java
class Dog {
    protected String name;  // protected：子类可见，外部不可见

    public Dog(String name) {
        this.name = name;
    }

    public void eat() {
        System.out.println(name + " 在吃饭");
    }

    protected void sleep() {  // protected：留给子类的"家族秘密"
        System.out.println(name + " 在睡觉");
    }
}

class Husky extends Dog {
    public Husky(String name) {
        super(name);  // 调用父类构造器
    }

    public void howl() {
        System.out.println(name + " 在狼嚎：嗷呜~~~");
    }

    @Override
    public void eat() {
        System.out.println(name + " 在狼吞虎咽！");  // 重写父类方法
    }
}
```

使用：
```java
Husky h = new Husky("二哈");
h.eat();   // "二哈 在狼吞虎咽！" —— 重写了
h.howl();  // "二哈 在狼嚎：嗷呜~~~" —— 子类新增的
h.sleep(); // "二哈 在睡觉" —— 继承自父类的 protected 方法
```

### 关键细节：可见性

| 修饰符 | 当前类 | 同包子类 | 不同包子类 | 所有类 |
|--------|--------|---------|-----------|--------|
| `private` | ✅ | ❌ | ❌ | ❌ |
| `default` | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

### 继承的陷阱：为什么 GoF 说"组合优于继承"

1. **白箱复用**：父类的内部细节对子类可见，破坏了封装
2. **编译时绑定**：继承关系在编译时确定，运行时无法改变
3. **脆弱基类**：父类的修改可能破坏所有子类
4. **子类爆炸**：要给 A 加 X 功能 -> `AWithX`；再加 Y -> `AWithXAndY`…

**什么时候用继承？**
- 存在真正的"is-a"关系（狗是动物）
- 子类和父类的行为差异主要在单个操作的扩展上（Template Method 场景）
- 不需要运行时动态改变行为

### 继承与设计模式的关系

- **Template Method**：最"合理"的继承用法——父类定义骨架，子类填空
- **Factory Method**：通过继承让子类决定创建什么
- **Adapter（类适配器）**：用继承来匹配接口
- **Decorator / Strategy / Bridge**：正是为了**替代**继承而生的模式

---

## 三、多态（Polymorphism）：同一个接口，不同的行为

### 一句话定义

> 同一个类型的引用可以指向不同的子类对象，调用同一个方法会产生不同的行为。

### 代码示例

```java
class Food {
    public void eat() {
        System.out.println("吃东西");
    }
}

class Tomato extends Food {
    @Override
    public void eat() {
        System.out.println("洗洗生吃番茄");
    }
}

class Apple extends Food {
    @Override
    public void eat() {
        System.out.println("削皮吃苹果");
    }
}

class Beef extends Food {
    @Override
    public void eat() {
        System.out.println("煮熟吃牛肉");
    }
}

// 多态的使用
Food[] lunch = { new Tomato(), new Apple(), new Beef() };
for (Food f : lunch) {
    f.eat();  // 同一个 f.eat()，不同对象执行不同的代码！
}
// 输出：
// 洗洗生吃番茄
// 削皮吃苹果
// 煮熟吃牛肉
```

### 关键认识

多态不神秘——它就是"**父类引用指向子类对象**" + "**方法调用在运行时动态绑定到实际对象的类型**"。

**违反直觉的关键点**：
```java
Food tomato = new Tomato();  // OK：Tomato is-a Food
// Tomato t = new Food();   // 编译错误！Food 不一定是 Tomato
```

### 多态与设计模式的关系

多态是以下模式的技术基础：

| 模式 | 多态怎么用的 |
|------|------------|
| **Strategy** | Context 持有 Strategy 接口引用，运行时指向不同算法子类 |
| **Observer** | Subject 持有 Observer 接口引用，通知所有具体观察者 |
| **State** | Context 持有 State 接口引用，改变状态 = 换一个子类对象 |
| **Command** | Invoker 持有 Command 接口引用，执行时多态调用 Execute |
| **Composite** | 叶结点和组合结点实现相同接口，透明操作 |
| **Decorator** | 装饰者和被装饰者实现相同接口，透明嵌套 |
| **Factory Method** | Creator 返回 Product 接口，客户只和 Product 多态交互 |

**一句话总结**：看懂这行代码就理解了多态——

```java
Animal a = new Dog();  // 左边是抽象类型，右边是具体类型
a.makeSound();          // 运行时才知道调用 Dog 的 makeSound
```

---

## 四、OOP 三大特性与设计原则的关系

```
封装（藏起来）
    ↓ 封装变化
继承（复用代码）
    ↓ 但要小心：组合优于继承
多态（统一接口）
    ↓ 针对接口编程
────────────────────
SOLID 原则（更高层的指导思想）
    ↓
23 种设计模式（具体的解决方案）
```

---

## 五、速查：这几个概念如果不清楚，先别碰的模式

| 你如果不理解... | 以下模式你会看不懂 |
|----------------|-------------------|
| **多态** | Strategy, Observer, State, Command, Composite, Decorator——几乎全部行为型模式 |
| **protected 和 private 的区别** | Template Method（原语操作用 protected）、Singleton（构造器用 private） |
| **接口 vs 抽象类** | Bridge（Abstraction 和 Implementor）、Adapter（类适配器 vs 对象适配器） |
| **组合 vs 继承** | Decorator vs 为什么不用继承、Strategy vs Template Method 的选择 |
| **静态方法 vs 实例方法** | Singleton 的 Instance 方法为什么是静态的、Factory Method 为什么不是 |

---

## 六、一句话测试

问自己一个问题，如果答不上来就先别学设计模式：

> 下面这行代码，`a` 的类型是什么？调用 `a.makeSound()` 执行的是谁的代码？
> ```java
> Animal a = new Dog();
> a.makeSound();
> ```

- `a` 的**编译时类型**：Animal
- `a` 的**运行时类型**：Dog
- `makeSound()` 执行的是：**Dog 的 makeSound()**

这就是多态。如果你理解了这一点，23 种设计模式的基础你就已经有了。
