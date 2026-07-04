# 重构手法目录

> 来源：Martin Fowler《重构：改善既有代码的设计》第6-12章
> 共 72 种重构手法，按类别分组。每种手法包含：定义、动机、做法（逐步步骤）、变换模式。
> 所有代码示例已从 Java 泛化为语言无关伪代码。

---

## 使用说明

重构手法不是"写新代码"的指南，而是"改旧代码"的操作手册。核心原则：

1. **小步前进**：每步只做一点修改，然后运行测试
2. **测试先行**：重构前必须有可靠的自动化测试
3. **两顶帽子**：重构时不加功能，加功能时不重构
4. **频率大于幅度**：宁可走 10 步小路，不走 1 步大跳

---

## 第6章：重新组织函数（9种）

函数是代码的基本构建块。几乎所有坏味道最终都指向函数层面的问题。

### 6.1 Extract Method（提炼函数）

**定义**：将一段代码组织进独立函数，用函数名解释其用途。

**动机**：最常用的重构手法。过长的函数、需要注释才能理解的代码都应提炼。短函数复用机会更大，高层函数读起来像注释。关键不在于函数长度，而在于函数名和函数体之间的语义距离。

**做法**：
1. 创建新函数，以"做什么"命名（不是"怎么做"）
2. 将待提炼代码复制到新函数
3. 检查局部变量和参数的引用
4. 如果有被修改的临时变量，考虑作为返回值
5. 如果多个变量被修改，可能需要先 Split Temporary Variable 或 Replace Temp with Query
6. 将原代码替换为对新函数的调用
7. 类型检查 + 测试

**变换模式**：
```
// Before
function printOwing() {
  printBanner();
  // print details
  console.log("name: " + name);
  console.log("amount: " + getOutstanding());
}

// After
function printOwing() {
  printBanner();
  printDetails(getOutstanding());
}
function printDetails(outstanding) {
  console.log("name: " + name);
  console.log("amount: " + outstanding);
}
```

---

### 6.2 Inline Method（内联函数）

**定义**：函数体与名称同样清晰时，在调用点插入函数体并移除该函数。

**动机**：非必要的间接层让人不舒服。也用于：先把组织不合理的函数全部内联，再重新提炼出合理的小函数。

**做法**：
1. 检查函数是否在子类型中被覆写（如果子类型覆写了这个函数，不要将此函数内联）
2. 找出所有调用点
3. 将每个调用替换为函数体
4. 类型检查 + 测试
5. 删除函数定义

> 如果遇到递归、多返回点、跨对象内联等复杂情况——就不应该用这个手法。

---

### 6.3 Inline Temp（内联临时变量）

**定义**：临时变量只被简单表达式赋值一次且妨碍其他重构，将所有引用替换为表达式本身。

**动机**：通常是 Replace Temp with Query 的一部分。单独使用的情况：临时变量被赋值为函数调用返回值且妨碍其他重构。

**做法**：
1. 确认变量只被赋值一次，表达式无副作用
2. 将所有引用替换为赋值表达式
3. 删除变量声明
4. 类型检查 + 测试

---

### 6.4 Replace Temp with Query（以查询取代临时变量）

**定义**：临时变量保存表达式结果 → 将表达式提炼为独立函数，用函数调用替换临时变量。

**动机**：临时变量只在所属函数内可见，驱使写出更长的函数。替换为查询后，同类的所有函数都能使用。通常是 Extract Method 的前置步骤。

**做法**：
1. 找出只赋值一次的临时变量
2. 将赋值右侧提炼为独立函数（设为私有）
3. 确保提炼函数无副作用
4. 用 Inline Temp 消除临时变量
5. 类型检查 + 测试

**变换模式**：
```
// Before
function getPrice() {
  let basePrice = quantity * itemPrice;
  if (basePrice > 1000) return basePrice * 0.95;
  return basePrice * 0.98;
}

// After
function getPrice() {
  if (basePrice() > 1000) return basePrice() * 0.95;
  return basePrice() * 0.98;
}
function basePrice() { return quantity * itemPrice; }
```

---

### 6.5 Introduce Explaining Variable（引入解释性变量）

**定义**：将复杂表达式（或其一部分）结果放入临时变量，用变量名解释表达式用途。

**动机**：条件逻辑中特别有用。当 Extract Method 因局部变量过多而困难时，用此手法替代。

**做法**：
1. 声明临时变量，将复杂表达式的一部分赋给它
2. 将表达式中对应部分替换为变量
3. 类型检查 + 测试
4. 重复处理其他部分

**变换模式**：
```
// Before
if ((platform.toUpperCase().indexOf("MAC") > -1) &&
    (browser.toUpperCase().indexOf("IE") > -1) &&
    wasInitialized() && resize > 0) { ... }

// After
const isMacOS = platform.toUpperCase().indexOf("MAC") > -1;
const isIEBrowser = browser.toUpperCase().indexOf("IE") > -1;
const wasResized = resize > 0;
if (isMacOS && isIEBrowser && wasInitialized() && wasResized) { ... }
```

---

### 6.6 Split Temporary Variable（分解临时变量）

**定义**：临时变量被赋值超过一次（非循环变量、非收集变量），为每次赋值创建独立变量。

**动机**：承担多个责任的临时变量会让阅读者糊涂。每个变量应只承担一个责任。

**做法**：
1. 在声明和第一次赋值处改变量名
2. 将新变量声明为不可变
3. 修改到下次赋值前的所有引用
4. 在第二次赋值处重新声明原变量
5. 类型检查 + 测试
6. 逐次重复

---

### 6.7 Remove Assignments to Parameters（移除对参数的赋值）

**定义**：代码对参数赋值 → 用临时变量替代。

**动机**：对参数赋值降低代码清晰度，混用按值/按引用传递语义。

**做法**：
1. 创建临时变量，将参数值赋给它
2. 将对参数的赋值改为对临时变量的赋值
3. 将后续对参数的引用替换为临时变量
4. 类型检查 + 测试

**变换模式**：
```
// Before
function discount(inputVal, quantity, yearToDate) {
  if (inputVal > 50) inputVal -= 2;  // 对参数赋值
  ...
}

// After
function discount(inputVal, quantity, yearToDate) {
  let result = inputVal;  // 用临时变量
  if (inputVal > 50) result -= 2;
  ...
}
```

---

### 6.8 Replace Method with Method Object（以函数对象取代函数）

**定义**：大型函数因局部变量过多无法 Extract Method → 将函数放入独立对象，局部变量变成对象字段，然后分解。

**动机**：当局部变量泛滥成灾时，这是最后的杀手锏。所有局部变量变成字段后，可以任意分解而不需传递参数。

**做法**：
1. 创建新类，以函数用途命名
2. 为源对象、每个参数和临时变量建立字段
3. 创建构造函数接收所有参数
4. 创建 compute() 方法，将原函数代码复制进去
5. 原函数体替换为：创建新对象 + 调用 compute()
6. 类型检查 + 测试
7. 在 compute() 内自由使用 Extract Method

---

### 6.9 Substitute Algorithm（替换算法）

**定义**：用更清晰的算法替换现有算法。

**动机**：随着对问题理解的加深，往往发现更简单的方案。或库函数提供了重复功能。

**做法**：
1. 准备新算法，让它通过类型检查
2. 对现有测试运行新算法
3. 结果相同 → 完成
4. 结果不同 → 用旧算法作参照，逐个测试用例对比调试

---

## 第7章：在对象之间搬移特性（8种）

"决定把责任放在哪儿"是面向对象设计中最重要的事之一。

### 7.1 Move Method（搬移函数）

**定义**：函数与所驻类之外的类交流更多 → 在目标类建立新函数，旧函数变委托或删除。

**动机**：重构理论的支柱。类有太多行为或与另一个类高度耦合时使用。

**做法**：
1. 检查源类中被函数使用的特性，考虑一并搬移
2. 检查继承体系是否有其他声明
3. 在目标类声明函数
4. 复制代码，调整以适应新家
5. 决定如何从目标函数引用源对象（参数/字段/现有引用）
6. 将源函数改为委托函数
7. 类型检查 + 测试
8. 决定删除还是保留委托

---

### 7.2 Move Field（搬移字段）

**定义**：字段被所驻类之外的类更多使用 → 在目标类新建字段，修改所有引用。

**做法**：
1. 如果字段是公开的，先 Encapsulate Field
2. 在目标类建立字段和访问函数
3. 决定如何从源对象引用目标对象
4. 删除源字段
5. 将所有引用替换为对目标访问函数的调用
6. 类型检查 + 测试

---

### 7.3 Extract Class（提炼类）

**定义**：一个类做了两个类的事 → 建立新类，搬移相关字段和函数。

**动机**：类会随时间成长扩展，变得过分复杂。某些数据和函数总是一起出现、同时变化 → 应分离。

**做法**：
1. 决定如何分解责任
2. 建立新类
3. 建立从旧类访问新类的连接
4. 用 Move Field 逐个搬移字段
5. 用 Move Method 搬移函数（先低层后高层）
6. 每次搬移后类型检查 + 测试
7. 检查是否可改单向连接
8. 决定是否公开新类

---

### 7.4 Inline Class（将类内联化）

**定义**：类没做太多事 → 所有特性搬移到另一个类，移除原类。

**动机**：Extract Class 的逆操作。类经过重构后变得几乎为空时使用。

**做法**：
1. 在目标类声明源类的所有公开函数
2. 将源类所有字段搬移到目标类
3. 将源类所有函数搬移到目标类
4. 修改源类引用点指向目标类
5. 删除源类
6. 类型检查 + 测试

---

### 7.5 Hide Delegate（隐藏委托关系）

**定义**：客户通过委托类调用另一个对象 → 在服务类建立所需函数，隐藏委托。

**动机**：封装变化。客户不应知道服务类背后的委托关系。

**做法**：
1. 在服务类建立委托函数
2. 修改客户调用，改用服务类的新函数
3. 每修改一个引用点就测试
4. 完成后客户不再知道委托的存在

**变换模式**：
```
// Before
person.getDepartment().getManager()

// After
person.getManager()  // Person 内部委托给 Department
```

---

### 7.6 Remove Middle Man（移除中间人）

**定义**：类做过多简单委托 → 让客户直接调用受托类。

**动机**：Hide Delegate 的逆操作。过度委托造成 Middle Man 坏味道。

**做法**：
1. 让客户直接获取受托对象
2. 删除委托函数
3. 修改调用点
4. 类型检查 + 测试

---

### 7.7 Introduce Foreign Method（引入外加函数）

**定义**：需要为服务类增加函数但无法修改 → 在客户类建立函数，第一参数传入服务类实例。

**动机**：库类功能不足时的轻量级扩展。

**变换模式**：
```
// Before — 需要计算"下一天开始"但 Date 类没有
const newStart = new Date(previousEnd.getFullYear(), previousEnd.getMonth(), previousEnd.getDate() + 1);

// After
function nextDay(date) {
  return new Date(date.getFullYear(), date.getMonth(), date.getDate() + 1);
}
const newStart = nextDay(previousEnd);
```

---

### 7.8 Introduce Local Extension（引入本地扩展）

**定义**：需要为服务类添加大量额外函数但无法修改 → 建立新类（子类或包装类）包含额外函数。

**动机**：外加函数不够用时，需要更系统的扩展方案。子类方式 vs 包装方式取决于场景。

---

## 第8章：重新组织数据（16种）

### 8.1 Self Encapsulate Field（自封装字段）

**定义**：在类内直接访问字段 → 改为通过访问函数访问。

**动机**：允许子类型覆写访问函数，或支持延迟初始化。

**争议性讨论**（原书重点讨论）：

| 派别 | 观点 | 代表 |
|------|------|------|
| **间接访问派** | 类内类外都通过访问函数访问字段，保持一致性 | Auer |
| **直接访问派** | 直接访问更清晰，只有在需要时才间接访问 | Beck |

**Fowler 的建议**（nuanced 观点）：
> "我先使用直接访问方式，直到这种方式给我带来麻烦为止。"

具体权衡：
- 如果子类型需要覆写访问函数 → 用自封装
- 如果需要延迟初始化 → 用自封装
- 如果字段访问很简单且不需要覆写 → 直接访问更清晰
- 如果团队规范要求一致性 → 遵循团队规范

**做法**：
1. 为字段建立访问函数（getter/setter）
2. 找到所有直接访问点
3. 逐一替换为对访问函数的调用
4. 类型检查 + 测试

> 注：现代 IDE 可以自动在直接访问和访问函数之间切换，降低了这个手法的必要性。

---

### 8.2 Replace Data Value with Object（以对象取代数据值）

**定义**：基本类型数据值 → 替换为对象。

**动机**：对抗 Primitive Obsession。如用 Money 类替代数字+币种。

**做法**：
1. 创建新类（值类），包含原始数据值
2. 将原字段类型改为新类
3. 修改所有引用点
4. 逐步添加行为到新类

---

### 8.3 Change Value to Reference（将值对象改为引用对象）

**定义**：多个相同值对象 → 改为单一引用对象。

**动机**：当需要共享同一实例时（如多个订单引用同一个客户对象）。

**做法**：使用注册表/工厂模式确保同值返回同一实例。

**变换模式**：
```
// Before — 每个订单各自创建客户值对象，数据重复
class Order {
  customer;  // 值对象，每份订单各有一份
  constructor(customerData) {
    this.customer = new Customer(customerData);  // 每次都 new
  }
}
// order1.customer 和 order2.customer 是不同实例，尽管数据相同

// After — 通过注册表共享同一引用对象
class CustomerRegistry {
  static instances = {};
  static getCustomer(id) {
    if (!this.instances[id]) {
      this.instances[id] = new Customer(id);
    }
    return this.instances[id];  // 同 ID 返回同一实例
  }
}
class Order {
  customer;  // 引用对象，指向共享实例
  constructor(customerId) {
    this.customer = CustomerRegistry.getCustomer(customerId);
  }
}
// order1.customer === order2.customer（同一实例，修改对全局可见）
```

---

### 8.4 Change Reference to Value（将引用对象改为值对象）

**定义**：引用对象 → 改为不可变值对象。

**动机**：当对象不需要共享身份，只需比较值时。值对象不可变、可复制、无副作用。

**变换模式**：
```
// Before — 订单共享可变的客户引用对象，修改一处影响所有引用者
class Order {
  customer;  // 引用对象，指向共享实例
  constructor(customer) {
    this.customer = customer;  // 同一实例被多个订单共享
  }
}
// 修改 order1.customer.name 会影响所有共享该客户的订单

// After — 每个订单持有不可变的客户值对象，按值比较
class Customer {  // 值对象：不可变 + 按值相等
  readonly id;
  readonly name;
  constructor(id, name) {
    this.id = id;
    this.name = name;
  }
  equals(other) {
    return this.id === other.id && this.name === other.name;
  }
}
class Order {
  customer;  // 值对象，每个订单各持一份副本
  constructor(customer) {
    this.customer = new Customer(customer.id, customer.name);  // 复制
  }
}
// 修改不影响其他订单，可通过 equals() 比较是否相同
```

---

### 8.5 Replace Array with Object（以对象取代数组）

**定义**：用数组存储不同含义的数据 → 替换为对象。

**变换模式**：
```
// Before
const row = [name, age, email];
console.log(row[0], row[1]);

// After
const person = { name, age, email };
console.log(person.name, person.age);
```

---

### 8.6 Duplicate Observed Data（复制被监视数据）

**定义**：GUI 类中混入领域逻辑 → 将领域数据分离到独立领域对象，保持同步。

**动机**：将领域逻辑从 GUI 层分离，使其可独立测试。

**变换模式**：
```
// Before — GUI 类混入领域逻辑，无法脱离界面测试
class OrderWindow {
  textField_quantity;
  textField_price;
  label_total;

  // 领域逻辑混在 GUI 事件处理中
  onQuantityChanged() {
    const total = parseInt(this.textField_quantity) * this.price;
    this.label_total.setText(total);
    if (total > 1000) applyDiscount();  // 领域规则
  }
}

// After — 领域数据分离到独立对象，GUI 通过同步机制保持一致
class Order {  // 领域对象，可独立测试
  quantity;
  price;
  observers = [];
  setQuantity(q) {
    this.quantity = q;
    this.notifyObservers();  // 通知 GUI 同步
  }
  getTotal() { return this.quantity * this.price; }
  notifyObservers() {
    for (o of this.observers) o.update(this);
  }
}
class OrderWindow {  // GUI 仅负责显示和监听
  onQuantityChanged() {
    order.setQuantity(parseInt(this.textField_quantity));
  }
  update(order) {  // 同步：领域 → GUI
    this.label_total.setText(order.getTotal());
  }
}
// 领域逻辑可脱离 GUI 独立测试，GUI 通过观察者模式同步状态
```

---

### 8.7 Change Unidirectional Association to Bidirectional（单向关联改双向）

**定义**：两个类需互相引用 → 建立双向关联并维护一致性。

**变换模式**：
```
// Before — 单向关联：Order 引用 Customer，反向不可达
class Order {
  customer;  // 正向引用
  setCustomer(c) { this.customer = c; }
}
class Customer {
  // 无法知道哪些 Order 引用了自己
}

// After — 双向关联：双方互引，一致性由一方维护
class Order {
  customer;
  setCustomer(c) {
    if (this.customer !== null) {
      this.customer.removeOrder(this);  // 先从旧关联移除
    }
    this.customer = c;
    if (c !== null) c.addOrder(this);  // 维护反向引用
  }
}
class Customer {
  orders = [];  // 反向引用集合
  addOrder(o) { this.orders.push(o); }
  removeOrder(o) { this.orders.remove(o); }
}
// 添加引用时双向同步，移除时同样清理，保证一致性
```

---

### 8.8 Change Bidirectional Association to Unidirectional（双向关联改单向）

**定义**：不再需要双向引用 → 改为单向，减少耦合。

**动机**：双向关联增加维护成本。如果某方向不再使用，就删掉它。

**变换模式**：
```
// Before — 双向关联：双方互引，维护成本高
class Order {
  customer;
  setCustomer(c) {
    if (this.customer !== null) this.customer.removeOrder(this);
    this.customer = c;
    if (c !== null) c.addOrder(this);
  }
}
class Customer {
  orders = [];  // 反向引用，每次修改都要同步维护
  addOrder(o) { this.orders.push(o); }
  removeOrder(o) { this.orders.remove(o); }
  getOrders() { return this.orders; }  // 但实际已无人调用
}

// After — 简化为单向关联：仅保留正向引用
class Order {
  customer;  // 正向引用保留
  setCustomer(c) {
    this.customer = c;  // 无需维护反向引用
  }
}
class Customer {
  // orders 及相关方法已删除，耦合降低
}
// 移除了反向引用，一致性维护代码也随之消除
```

---

### 8.9 Replace Magic Number with Symbolic Constant（以字面常量取代魔法数）

**定义**：代码中硬编码的数值 → 替换为命名常量。

**变换模式**：
```
// Before
if (status == 5) { ... }

// After
const STATUS_COMPLETED = 5;
if (status == STATUS_COMPLETED) { ... }
```

---

### 8.10 Encapsulate Field（封装字段）

**定义**：公开字段 → 改为私有字段 + 访问函数。

**做法**：
1. 将字段设为私有/不可直接访问
2. 添加访问函数（getter/setter 或等效机制）
3. 修改所有直接引用为通过访问函数

> 不同语言的实现方式不同：
> - Java/C++：private 字段 + getter/setter 方法
> - Python：以单下划线 `_field` 表示"不建议直接访问"，或用 `@property`
> - JavaScript：以闭包或 `#field` 私有字段 + get/set 方法
> - Go：未导出字段 + getter/setter 方法

---

### 8.11 Encapsulate Collection（封装集合）

**定义**：返回集合的 getter → 返回不可变视图或副本，添加专用 add/remove 方法。

**动机**：直接返回集合让外部可以随意修改，破坏封装。集合的 getter 应返回只读副本，修改通过专用方法进行。

**变换模式**：
```
// Before
class Person {
  getCourses() { return this._courses; }  // 外部可直接修改
}

// After
class Person {
  getCourses() { return [...this._courses]; }  // 返回副本
  addCourse(course) { this._courses.push(course); }
  removeCourse(course) { ... }
}
```

---

### 8.12 Replace Record with Data Class（以数据类取代记录）

**定义**：传统记录/字典结构 → 替换为有类型的类。

**变换模式**：
```
// Before — 无类型的字典结构，字段名拼写错误不会被编译器捕获
record = {"name": "Alice", "age": 30, "email": "alice@example.com"}
function getName(record) { return record["name"]; }
// record["nme"] 不会报错，直到运行时才发现问题

// After — 有类型的数据类，字段名错误在编译期即可发现
class Person {
  name: string;
  age: number;
  email: string;
  constructor(name, age, email) {
    this.name = name;
    this.age = age;
    this.email = email;
  }
}
const person = new Person("Alice", 30, "alice@example.com");
// 类型安全，IDE 可自动补全，编译器可捕获拼写错误
```

---

### 8.13 Replace Type Code with Class（以类取代类型码）

**定义**：用数字/字符串表示类型 → 替换为类/枚举。

**动机**：类型码没有类型安全，容易传错值。

**变换模式**：
```
// Before
const ENGINEER = 0, SALESMAN = 1, MANAGER = 2;
class Employee { type; }

// After
class EmployeeType {
  static ENGINEER = new EmployeeType("engineer");
  static SALESMAN = new EmployeeType("salesman");
  static MANAGER = new EmployeeType("manager");
}
```

---

### 8.14 Replace Type Code with Subclasses（以子类取代类型码）

**定义**：类型码影响行为 → 用子类型替代，通过多态处理不同行为。

**动机**：当不同类型有不同的行为逻辑时，多态比 switch 更优雅。

---

### 8.15 Replace Type Code with State/Strategy（以 State/Strategy 取代类型码）

**定义**：类型码在运行时变化 → 用 State 或 Strategy 模式替代。

**动机**：当类型码在对象生命周期内会变化时，子类型方案不可行（不能改变对象的类型），需用组合模式。

> **与 GoF Skill 的交叉**：此手法直接引入 State 或 Strategy 模式，可切换到 gof-architecture-advisor 获取模式详细指导。

---

### 8.16 Replace Subclass with Fields（以字段取代子类）

**定义**：子类型之间唯一的差异只是返回常量值 → 用父类型的字段替代子类型。

**动机**：不需要为每个常量值创建子类型。简化继承体系。

**变换模式**：
```
// Before — 子类之间唯一的差异是返回不同的常量值
class Male extends Person {
  getCode() { return "M"; }
  isMale() { return true; }
}
class Female extends Person {
  getCode() { return "F"; }
  isMale() { return false; }
}
// 子类没有真正的行为差异，只为常量而存在

// After — 用字段替代子类，通过构造函数注入常量值
class Person {
  code;       // "M" 或 "F"
  isMaleFlag;
  constructor(code, isMaleFlag) {
    this.code = code;
    this.isMaleFlag = isMaleFlag;
  }
  getCode() { return this.code; }
  isMale() { return this.isMaleFlag; }
}
const male = new Person("M", true);
const female = new Person("F", false);
// 不再需要子类，常量值以参数形式传入
```

---

## 第9章：简化条件表达式（8种）

### 9.1 Decompose Conditional（分解条件表达式）

**定义**：复杂条件表达式 → 分解为命名良好的函数。

**变换模式**：
```
// Before
if (date.before(SUMMER_START) || date.after(SUMMER_END)) {
  charge = quantity * winterRate + winterServiceCharge;
} else {
  charge = quantity * summerRate;
}

// After
if (notSummer(date)) {
  charge = winterCharge(quantity);
} else {
  charge = summerCharge(quantity);
}
```

---

### 9.2 Consolidate Conditional Expression（合并条件表达式）

**定义**：多个条件返回同一结果 → 合并为单个条件，用 Extract Method 提炼。

---

### 9.3 Consolidate Duplicate Conditional Fragments（合并重复的条件片段）

**定义**：条件分支中有相同代码 → 提取到条件之外。

---

### 9.4 Remove Control Flag（移除控制标记）

**定义**：用布尔标志控制循环退出 → 用 break/return/异常替代。

---

### 9.5 Replace Nested Conditional with Guard Clauses（以卫语句取代嵌套条件）

**定义**：深层嵌套的条件 → 用提前返回（卫语句）展平。

**动机**：卫语句更清晰地表达"异常情况提前退出"的意图。

**变换模式**：
```
// Before
function getPayAmount() {
  let result;
  if (isDead) result = deadAmount();
  else {
    if (isSeparated) result = separatedAmount();
    else {
      if (isRetired) result = retiredAmount();
      else result = normalPayAmount();
    }
  }
  return result;
}

// After
function getPayAmount() {
  if (isDead) return deadAmount();
  if (isSeparated) return separatedAmount();
  if (isRetired) return retiredAmount();
  return normalPayAmount();
}
```

---

### 9.6 Replace Conditional with Polymorphism（以多态取代条件表达式）

**定义**：根据类型码的 switch/match → 将行为移到子类型的覆写方法中。

**动机**：当同一个 switch 出现在多处时，多态让新增类型只需添加子类型，无需修改已有代码。

**做法**：
1. 先用 Replace Type Code with Subclasses 或 State/Strategy
2. 将 switch 代码移到子类型
3. 每个子类型覆写对应方法
4. 删除条件逻辑

> **与 GoF Skill 的交叉**：此手法是 State/Strategy 模式的直接应用。

---

### 9.7 Introduce Null Object（引入 Null 对象）

**定义**：到处检查 null → 用 Null 对象替代，它提供"什么都不做"的默认行为。

**变换模式**：
```
// Before
if (customer != null) plan = customer.getPlan();
else plan = BillingPlan.basic();

// After
// customer 可能是 RealCustomer 或 NullCustomer
plan = customer.getPlan();  // NullCustomer.getPlan() 返回 BillingPlan.basic()
```

---

### 9.8 Introduce Assertion（引入断言）

**定义**：代码假设某条件总为真 → 用断言显式表达。

**动机**：断言帮助交流假设，在运行时捕获意外。断言不应有副作用，生产环境可关闭。

---

## 第10章：简化函数调用（15种）

### 10.1 Rename Method（函数改名）

**定义**：函数名不能揭示其用途 → 改名。

> 好的命名是代码可读性的关键。"任何一个傻瓜都能写出计算机可以理解的代码。唯有写出人类容易理解的代码，才是优秀的程序员。"

### 10.2 Add Parameter（添加参数）
### 10.3 Remove Parameter（移除参数）

**定义**：函数需要/不需要某参数 → 添加/移除。

---

### 10.4 Separate Query from Modifier（将查询函数和修改函数分离）

**定义**：函数既查询又修改 → 拆分为两个函数。

**动机**：查询函数（获取值）应该没有副作用。将查询和修改分离后，可以自由调用查询。

---

### 10.5 Parameterize Method（令函数携带参数）

**定义**：多个函数做类似的事只是值不同 → 合并为一个带参数的函数。

---

### 10.6 Replace Parameter with Explicit Methods（以明确函数取代参数）

**定义**：参数值决定函数行为 → 用独立函数替代每个参数值。

**变换模式**：
```
// Before
function setValue(name, value) {
  if (name === "height") this._height = value;
  if (name === "width") this._width = value;
}

// After
function setHeight(value) { this._height = value; }
function setWidth(value) { this._width = value; }
```

---

### 10.7 Preserve Whole Object（保持对象完整）

**定义**：从同一对象取多个值传给函数 → 直接传整个对象。

**变换模式**：
```
// Before
const plan = calculatePlan(customer.getPlanRate(), customer.getPlanType());

// After
const plan = calculatePlan(customer);
```

> 例外：刻意避免与较大对象的依赖时，传值参数是合理的。

---

### 10.8 Replace Parameter with Methods（以函数取代参数）

**定义**：参数可由函数调用获取 → 让被调用方自己获取。

**做法**：当"已有对象"（可能是函数所属类的字段或另一个参数）可以提供该值时，移除参数。

---

### 10.9 Introduce Parameter Object（引入参数对象）

**定义**：多个参数总是一起出现 → 替换为一个参数对象。

**动机**：对抗 Long Parameter List 和 Data Clumps。参数对象还可以承载行为。

**变换模式**：
```
// Before
function createReport(startDate, endDate, startTime, endTime) { ... }

// After
function createReport(dateRange) { ... }
class DateRange { startDate, endDate, startTime, endTime }
```

---

### 10.10 Remove Setting Method（移除设值函数）

**定义**：字段在构造后不应改变 → 移除 setter，标记为不可变。

**动机**：不可变对象更容易理解、线程安全、无副作用。

---

### 10.11 Hide Method（隐藏函数）

**定义**：函数没有被外部调用 → 设为私有。

---

### 10.12 Replace Constructor with Factory Method（以工厂函数取代构造函数）

**定义**：创建对象时需要根据类型选择子类型 → 用工厂函数替代构造函数。

> **与 GoF Skill 的交叉**：直接引入 Factory Method 模式。

---

### 10.13 Encapsulate Downcast（封装向下转型）

**定义**：调用方需要向下转型 → 让被调用方返回正确类型。

**变换模式**：
```
// Before（调用方需要类型断言/转换）
const dog = animal.doSomething() as Dog;  // TypeScript: as / Kotlin: as / Java: (Dog)

// After（被调用方已返回正确类型）
const dog = animal.doSomething();  // 返回类型已是 Dog，无需转换
```

> 不同语言的类型转换语法：
> - TypeScript: `value as Type` 或 `<Type>value`
> - Java: `(Type)value`
> - C++: `dynamic_cast<Type>(value)`
> - Python: `isinstance(value, Type)` 检查 + 直接使用

---

### 10.14 Replace Error Code with Exception（以异常取代错误码）

**定义**：函数返回错误码 → 改为抛出异常。

**动机**：异常比错误码更清晰地分离正常逻辑和错误处理。

---

### 10.15 Replace Exception with Test（以测试取代异常）

**定义**：用异常处理常规逻辑 → 先检查条件再执行。

**动机**：异常应用于异常情况，不应用于常规流程控制。

**变换模式**：
```
// Before
try {
  return stack.pop();
} catch (EmptyStackException e) {
  return null;
}

// After
if (stack.isEmpty()) return null;
return stack.pop();
```

---

## 第11章：处理概括关系（12种）

### 11.1 Pull Up Field（字段上移）
### 11.2 Pull Up Method（函数上移）
### 11.3 Pull Up Constructor Body（构造函数本体上移）

**定义**：子类型中相同的字段/函数/构造代码 → 上移到父类型。

**动机**：消除重复。将共享行为提升到继承体系的公共点。

**变换模式**（11.3 Pull Up Constructor Body）：
```
// Before — 子类构造函数中存在重复的初始化代码
class Employee {
  name;
}
class Manager extends Employee {
  department;
  constructor(name, department) {
    this.name = name;        // 重复代码
    this.department = department;
  }
}
class Engineer extends Employee {
  level;
  constructor(name, level) {
    this.name = name;        // 重复代码
    this.level = level;
  }
}

// After — 公共构造代码上移到父类构造函数，子类通过 super 调用
class Employee {
  name;
  constructor(name) {
    this.name = name;        // 公共初始化代码上移
  }
}
class Manager extends Employee {
  department;
  constructor(name, department) {
    super(name);             // 调用父类构造函数
    this.department = department;
  }
}
class Engineer extends Employee {
  level;
  constructor(name, level) {
    super(name);             // 调用父类构造函数
    this.level = level;
  }
}
// 消除重复，新增子类时只需通过 super 传入公共参数
```

### 11.4 Push Down Method（函数下移）
### 11.5 Push Down Field（字段下移）

**定义**：父类型的字段/函数只被部分子类型使用 → 下移到需要它的子类型。

**动机**：父类型应只包含所有子类型共享的内容。

### 11.6 Extract Subclass（提炼子类）
### 11.7 Extract Superclass（提炼超类）
### 11.8 Extract Interface（提炼接口）

**定义**：类有部分职责可分离 → 提炼为子类/超类/接口。

**动机**：
- Extract Subclass：类的部分行为只对某些实例有意义
- Extract Superclass：两个类有相似特性 → 提取共同父类型
- Extract Interface：相同的接口被多个类使用 → 显式定义接口

### 11.9 Collapse Hierarchy（折叠继承体系）

**定义**：父类型和子类型已无实质差异 → 合并。

### 11.10 Form Template Method（塑造模板函数）

**定义**：子类型中有执行步骤相同但细节不同的函数 → 用 Template Method 模式上移到父类型。

> **与 GoF Skill 的交叉**：直接引入 Template Method 模式。

### 11.11 Replace Inheritance with Delegation（以委托取代继承）

**定义**：子类型只使用父类型的一小部分功能 → 改为委托。

**动机**：继承不应只是为了复用代码。如果不需要接口继承，委托更合适。

### 11.12 Replace Delegation with Inheritance（以继承取代委托）

**定义**：委托了大量行为且接口完全相同 → 改为继承。

---

## 第12章：大型重构（4种）

大型重构不是一步到位的，需要持续数周甚至数月的小步前进。

### 12.1 Tease Apart Inheritance（梳理并分解继承体系）

**定义**：一个继承体系同时处理两个不同维度的变化 → 分解为两个继承体系，用组合替代交叉继承。

**动机**：当继承体系沿两个不同方向变化时（如"形状" × "显示方式"），会出现子类型爆炸。

### 12.2 Convert Procedural Design to Objects（将过程化设计转化为对象设计）

**定义**：过程式代码（数据记录 + 操作函数）→ 转为面向对象设计。

**动机**：将数据和行为放在一起，让数据类拥有自己的行为。

**变换模式**：
```
// Before — 过程化设计：数据记录与操作函数分离
class AccountData {        // 被动的数据容器
  accountType;
  balance;
  interestRate;
}
class AccountCalculator {  // 逻辑集中在单独的工具类
  calculateInterest(account) {
    if (account.accountType === "premium") {
      return account.balance * account.interestRate * 1.5;
    }
    return account.balance * account.interestRate;
  }
  calculatePenalty(account, days) {
    return account.balance * 0.01 * days;
  }
}
// 数据类只是被动的数据容器，行为与数据分离

// After — 面向对象设计：数据与行为放在一起
class Account {
  accountType;
  balance;
  interestRate;
  calculateInterest() {           // 行为移入数据类
    if (this.accountType === "premium") {
      return this.balance * this.interestRate * 1.5;
    }
    return this.balance * this.interestRate;
  }
  calculatePenalty(days) {        // 行为移入数据类
    return this.balance * 0.01 * days;
  }
}
// Account 同时拥有数据和行为，AccountCalculator 已删除
// 可使用 Move Method 逐步搬移，每次搬移后测试
```

### 12.3 Separate Domain from Presentation（将领域和表述分离）

**定义**：领域逻辑混入 UI 代码 → 分离为独立的领域层。

**变换模式**：
```
// Before — 领域逻辑混入 UI 代码，无法独立测试
class OrderPage {
  render(order) {
    let total = order.quantity * order.unitPrice;
    if (total > 1000) total *= 0.9;     // 领域折扣规则混在 UI 中
    if (order.isVip) total -= 50;       // 领域 VIP 规则混在 UI 中
    document.getElementById("total").innerText = "¥" + total.toFixed(2);
  }
  onSave(order) {
    let total = order.quantity * order.unitPrice;
    if (total > 1000) total *= 0.9;     // 同样的领域逻辑重复出现
    if (order.isVip) total -= 50;
    saveToDatabase(order, total);
  }
}
// 业务规则散落在 UI 代码中，无法脱离界面测试，且多处重复

// After — 领域逻辑分离到独立的领域层
class Order {                           // 领域层：纯逻辑，无 UI 依赖
  quantity;
  unitPrice;
  isVip;
  getTotal() {
    let total = this.quantity * this.unitPrice;
    if (total > 1000) total *= 0.9;
    if (this.isVip) total -= 50;
    return total;
  }
}
class OrderPage {                       // 表述层：仅负责显示和转发
  render(order) {
    document.getElementById("total").innerText = "¥" + order.getTotal().toFixed(2);
  }
  onSave(order) {
    saveToDatabase(order, order.getTotal());
  }
}
// 领域逻辑可在 Order 中独立测试，表述层不再包含业务规则
```

> **与 Clean Architecture Skill 的交叉**：这是整洁架构"同心圆模型"中 Adapter 与 Use Case 分离的具体操作。

### 12.4 Extract Hierarchy（提炼继承体系）

**定义**：一个类承担了多种条件行为 → 分解为继承体系。

**做法**：
1. 先用 Replace Conditional with Polymorphism 处理每个条件
2. 逐步提炼出子类型
3. 这是"渐进式"重构——不要试图一次到位

**变换模式**：
```
// Before — 一个类通过 switch 处理多种条件行为
class ShippingCostCalculator {
  calculate(package, type) {
    switch (type) {
      case "air":
        return package.weight * 10 + package.distance * 0.5;
      case "ground":
        return package.weight * 2 + package.distance * 0.1;
      case "sea":
        return package.weight * 1 + package.distance * 0.05;
      default:
        throw new Error("未知运输类型");
    }
  }
}
// 所有运输方式的逻辑挤在一个类中，新增类型需修改 switch

// After — 条件行为提炼为继承体系，每个子类对应一种运输方式
abstract class ShippingCostCalculator {
  abstract calculate(package);
}
class AirShippingCalculator extends ShippingCostCalculator {
  calculate(package) {
    return package.weight * 10 + package.distance * 0.5;
  }
}
class GroundShippingCalculator extends ShippingCostCalculator {
  calculate(package) {
    return package.weight * 2 + package.distance * 0.1;
  }
}
class SeaShippingCalculator extends ShippingCostCalculator {
  calculate(package) {
    return package.weight * 1 + package.distance * 0.05;
  }
}
// 新增运输类型只需添加新子类，无需修改已有代码（开闭原则）
// 渐进式：每次只提炼一个 case 为子类，测试通过后再提炼下一个
```

---

## 重构手法选择决策树

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
│   ├── 【例外】Strategy/Visitor 模式故意将行为与数据分离 → 这是对抗 Divergent Change 的手段，不是坏味道
│   └── （一般情况）→ Move Method → Replace Type Code with State/Strategy → Replace Conditional with Polymorphism
├── 数据和操作分离
│   ├── 【例外】Strategy/Visitor 模式 → 可接受（见上）
│   └── （一般情况）→ Move Method（搬行为到数据类）
├── 委托问题（平衡决策）
│   ├── 委托不足（消息链） → Hide Delegate
│   ├── 过度委托（中间人） → Remove Middle Man / Inline Method
│   └── 【平衡原则】Hide Delegate 和 Remove Middle Man 是相反操作，根据实际需要平衡：
│       ├── 如果委托函数不多（1-2个）→ Remove Middle Man，直接访问
│       ├── 如果委托函数多但稳定 → 保留 Hide Delegate，维持封装
│       └── 如果委托函数多且变化 → 考虑 Replace Delegation with Inheritance
├── 基本类型偏执
│   └── Replace Data Value with Object / Replace Type Code with Class
├── 条件逻辑复杂
│   ├── 单一函数内 → Decompose Conditional
│   ├── 嵌套过深 → Replace Nested Conditional with Guard Clauses
│   └── 按类型分支 → Replace Conditional with Polymorphism
├── 继承体系不合理
│   ├── 共性在子类 → Pull Up Method/Field
│   ├── 特性仅部分子类需要 → Push Down Method/Field
│   ├── 继承滥用（子类型不需要大部分功能） → Replace Inheritance with Delegation
│   └── 两个维度交叉 → Tease Apart Inheritance
└── 字段/数据问题
    ├── 相同数据项总是一起出现 → Extract Class（数据泥团）
    ├── 基本类型表示概念 → Replace Data Value with Object
    ├── 类型码影响行为 → Replace Type Code with Subclasses/State-Strategy
    └── 字段只在特定场景有效 → Extract Class（暂时字段）
```

### 决策树使用说明

1. **从最具体的症状开始**——如果代码有多个坏味道，先处理阻碍你前进的那个
2. **例外情况优先判断**——看到 Strategy/Visitor 模式时，先判断是故意设计还是坏味道
3. **平衡决策需要审时度势**——Hide Delegate 和 Remove Middle Man 之间没有绝对对错，取决于系统的变化模式
4. **决策树不是算法**——它是指南，不是规则。相信你的嗅觉，结合上下文判断
5. **重构是迭代的**——第一次重构可能只改善一部分，后续迭代继续改善
