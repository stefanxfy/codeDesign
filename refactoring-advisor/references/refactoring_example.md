# Movie Rental 重构示例

> 来源：Martin Fowler《重构：改善既有代码的设计》第1章
> 这是整本书的**教学基石**——通过一个完整例子，展示重构的节奏感和思考过程。

---

## 为什么这个示例重要

第1章的影片租赁示例传递了几个关键理念：

1. **重构是小步前进的**——每次只改一点，测试通过再继续
2. **重构的节奏感**——测试 → 小改 → 测试 → 小改
3. **提取函数 → 搬移函数 → 多态替代**的完整演进路径
4. **两顶帽子交替进行**——先重构（让添加 htmlStatement 更容易），再加功能

---

## 初始代码

一个影片租赁系统的计费功能。客户可以租影片，系统生成账单（文本格式）。

```pseudo
class Movie {
  title
  priceCode  // 0=普通片, 1=新片, 2=儿童片
}

class Rental {
  movie
  daysRented
}

class Customer {
  name
  rentals  // list of Rental
}
```

初始的 `statement()` 函数（在一个函数内完成了所有事情）：

```pseudo
function statement() {
  totalAmount = 0
  frequentRenterPoints = 0
  result = "Rental Record for " + name + "\n"

  for each rental in rentals {
    // 计算单笔租金
    thisAmount = 0
    if (rental.movie.priceCode == REGULAR) {
      thisAmount += 2
      if (rental.daysRented > 2)
        thisAmount += (rental.daysRented - 2) * 1.5
    } else if (rental.movie.priceCode == NEW_RELEASE) {
      thisAmount += rental.daysRented * 3
    } else if (rental.movie.priceCode == CHILDRENS) {
      thisAmount += 1.5
      if (rental.daysRented > 3)
        thisAmount += (rental.daysRented - 3) * 1.5
    }

    // 累加常客积分
    frequentRenterPoints += 1
    if (rental.movie.priceCode == NEW_RELEASE && rental.daysRented > 1)
      frequentRenterPoints += 1

    // 添加明细行
    result += "\t" + rental.movie.title + "\t" + thisAmount + "\n"
    totalAmount += thisAmount
  }

  // 添加总计
  result += "Amount owed is " + totalAmount + "\n"
  result += "You earned " + frequentRenterPoints + " frequent renter points"
  return result
}
```

**坏味道诊断**：
- 过长函数（Low: 需要注释才能理解每个段落）
- 分支惊悚现身（switch by priceCode 出现在"计算租金"和"计算积分"两处）
- 数据类（Customer 类只有数据，行为都在 statement() 里）

---

## 重构步骤 1：Extract Method — 提炼 amountFor

**动机**：计算单笔租金的代码块可以独立出来。

**做法**：
1. 将租金计算代码提炼为 `amountFor(rental)`
2. 原函数调用 `amountFor(rental)` 替代内联计算
3. 测试通过

```pseudo
// After Step 1
function statement() {
  totalAmount = 0
  frequentRenterPoints = 0
  result = "Rental Record for " + name + "\n"

  for each rental in rentals {
    thisAmount = amountFor(rental)  // ← 提炼后的函数
    // ... 其余不变
  }
  // ...
}

function amountFor(rental) {
  result = 0
  if (rental.movie.priceCode == REGULAR) {
    result += 2
    if (rental.daysRented > 2)
      result += (rental.daysRented - 2) * 1.5
  } else if (rental.movie.priceCode == NEW_RELEASE) {
    result += rental.daysRented * 3
  } else if (rental.movie.priceCode == CHILDRENS) {
    result += 1.5
    if (rental.daysRented > 3)
      result += (rental.daysRented - 3) * 1.5
  }
  return result
}
```

---

## 重构步骤 2：Move Method — 将 amountFor 搬移到 Rental 类

**动机**：`amountFor` 大量访问 `rental` 的数据，它应该属于 `Rental` 类（Feature Envy 坏味道）。

**做法**：
1. 在 `Rental` 类中创建 `getCharge()` 方法
2. 将 `amountFor` 的代码搬入 `Rental.getCharge()`
3. 修改 `statement()` 调用 `rental.getCharge()`
4. 测试通过
5. 删除 `amountFor`（或用委托保留一段时间）

```pseudo
// After Step 2
class Rental {
  // ...
  function getCharge() {
    result = 0
    if (movie.priceCode == REGULAR) { ... }
    else if (movie.priceCode == NEW_RELEASE) { ... }
    else if (movie.priceCode == CHILDRENS) { ... }
    return result
  }
}

function statement() {
  for each rental in rentals {
    thisAmount = rental.getCharge()  // ← 直接调用
    // ...
  }
}
```

---

## 重构步骤 3：Replace Temp with Query — 消除临时变量

**动机**：`thisAmount` 只被赋值一次，且 `getCharge()` 已提供相同功能。

**做法**：
1. 将 `thisAmount` 替换为 `rental.getCharge()` 直接调用
2. 测试通过

```pseudo
// After Step 3
function statement() {
  frequentRenterPoints = 0
  result = "Rental Record for " + name + "\n"

  for each rental in rentals {
    // thisAmount 已消除
    result += "\t" + rental.movie.title + "\t" + rental.getCharge() + "\n"
    totalAmount += rental.getCharge()  // ← 调用两次，可考虑内联或保留
  }
}
```

---

## 重构步骤 4：Extract Method — 提炼常客积分计算

**动机**：常客积分的计算逻辑也应该属于 `Rental`。

**做法**：
1. 在 `Rental` 中创建 `getFrequentRenterPoints()`
2. 修改 `statement()` 调用 `rental.getFrequentRenterPoints()`
3. 测试通过

```pseudo
// After Step 4
class Rental {
  function getCharge() { ... }
  function getFrequentRenterPoints() {
    if (movie.priceCode == NEW_RELEASE && daysRented > 1)
      return 2
    return 1
  }
}

function statement() {
  for each rental in rentals {
    result += "\t" + rental.movie.title + "\t" + rental.getCharge() + "\n"
    totalAmount += rental.getCharge()
    frequentRenterPoints += rental.getFrequentRenterPoints()
  }
}
```

---

## 重构步骤 5：添加新功能 — htmlStatement()

**动机**：现在需要生成 HTML 格式的账单。如果之前没有重构，这段代码会和 `statement()` 有大量重复。

**两顶帽子交替**：
- 先戴**重构帽**：让添加 htmlStatement 更容易（当前已完成）
- 再戴**功能帽**：添加 htmlStatement

```pseudo
function htmlStatement() {
  result = "<h1>Rental Record for " + name + "</h1>\n"
  for each rental in rentals {
    result += "<p>" + rental.movie.title + "\t" + rental.getCharge() + "</p>\n"
  }
  result += "<p>Amount owed is " + totalAmount + "</p>\n"
  result += "<p>You earned " + frequentRenterPoints + " points</p>\n"
  return result
}
```

**观察**：`htmlStatement()` 非常容易写，因为计费逻辑已在 `Rental` 类中。这就是重构的价值。

---

## 重构步骤 6：Replace Conditional with Polymorphism — 用多态替代 switch

**动机**：`getCharge()` 和 `getFrequentRenterPoints()` 中的 switch by priceCode 仍然存在。如果新增影片类型，需要修改这两处。

**做法**：
1. 将 `priceCode` 替换为 `Movie` 的子类型
2. 在子类型中覆写 `getCharge()` 和 `getFrequentRenterPoints()`

```pseudo
// After Step 6 — 引入多态
abstract class Movie {
  title
  abstract function getCharge(daysRented)
  abstract function getFrequentRenterPoints(daysRented)
}

class RegularMovie extends Movie {
  function getCharge(daysRented) {
    result = 2
    if (daysRented > 2) result += (daysRented - 2) * 1.5
    return result
  }
  function getFrequentRenterPoints(daysRented) { return 1 }
}

class NewReleaseMovie extends Movie {
  function getCharge(daysRented) { return daysRented * 3 }
  function getFrequentRenterPoints(daysRented) {
    return (daysRented > 1) ? 2 : 1
  }
}

class ChildrensMovie extends Movie {
  function getCharge(daysRented) {
    result = 1.5
    if (daysRented > 3) result += (daysRented - 3) * 1.5
    return result
  }
  function getFrequentRenterPoints(daysRented) { return 1 }
}
```

现在 `Rental.getCharge()` 变为：
```pseudo
class Rental {
  function getCharge() {
    return movie.getCharge(daysRented)  // ← 多态调用
  }
  function getFrequentRenterPoints() {
    return movie.getFrequentRenterPoints(daysRented)
  }
}
```

**switch 完全消除了**。新增影片类型只需添加 `Movie` 子类型，无需修改任何已有代码。

---

## 最终状态

| 重构前 | 重构后 |
|--------|--------|
| `statement()` 一个函数完成所有事 | `statement()` 只负责格式化输出 |
| 计费逻辑散落在函数中 | 计费逻辑在 `Movie` 子类型中 |
| 新增影片类型需改多处 | 新增子类型即可 |
| 添加 htmlStatement 会大量重复 | 添加 htmlStatement 非常简单 |

---

## 关键洞察

> "当你发现自己需要为程序添加一个特性，而代码结构使你无法很方便地达成目的，那就先重构那个程序，使特性的添加更容易，然后再添加特性。"

> "重构技术就是以微小的步伐修改程序。如果你犯下错误，很容易便可发现它。"

> "大多数重构都是这样：先对一个过于复杂的函数做些整理，再对刚被整理过的函数用 Move Method 搬移到更合适的类，如此反复。"

---

## 与本书其他部分的关联

- **第2章**：此示例展示了"两顶帽子"如何交替进行
- **第3章**：此示例中的代码展示了 Duplicated Code、Long Method、Feature Envy、Switch Statements 等坏味道
- **第6章**：Extract Method 和 Replace Temp with Query 在此示例中首次出现
- **第7章**：Move Method 是此示例的关键转折
- **第9章**：Replace Conditional with Polymorphism 是此示例的高潮
- **与 GoF Skill 的关系**：此示例最终引入了 **Polymorphism**（行为型模式的基础），当需要更精细的模式选择时（如 State vs Strategy），切换到 GoF Skill
