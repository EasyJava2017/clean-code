作者[ryanmcdermott](https://github.com/ryanmcdermott)根据Robert C. Martin[《代码整洁之道》](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)总结了适用于JavaScript的软件工程原则[《Clean Code JavaScript》](https://github.com/ryanmcdermott/clean-code-javascript)。本文是对其的翻译。

Original Repository: [ryanmcdermott/clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)

# 代码整洁之道

## 目录
  1. [简介](#简介)
  2. [变量](#变量)
  3. [函数](#函数)
  4. [对象和数据结构](#对象和数据结构)
  5. [类](#类)
  6. [健壮性](#健壮性)
  7. [可测试性](#可测试性)
  8. [并发性](#并发性)
  9. [容错性](#容错性)
  10. [格式化](#格式化)
  11. [注释](#注释)

## 简介

> 如下是一幅幽默的图片，该图片解释了代码review时每分钟的咒骂数是作为代码质量评测的唯一指标。

![Humorous image of software quality estimation as a count of how many expletives you shout when reading code](http://www.osnews.com/images/comics/wtfm.jpg)

这是根据Robert C. Martin[《代码整洁之道》](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)总结的适用于JavaScript的软件工程原则[《Clean Code JavaScript》](https://github.com/ryanmcdermott/clean-code-javascript)。但并不代表就必须遵循这些原则，这只是一份如何写出可读可复用和可重构的生产代码的指导而已。

并不是在此处提到的所有的准则都必须被严格遵守，相反有时尽量少遵守一些会更好。这仅仅是一些作者经过多年的编码总结整理出来的经验而已。

软件工程仅仅有50多年的历史，至今仍在不断前进发展。只有当软件架构极度成熟的时候，这些原则或许才能被当做唯一准则去遵循。但现在，尝试着将这些指导当做你或者你的团队代码质量考核的标准之一吧。

另外：了解了这些并不能立刻使你成为一个优秀的软件开发人员，以他们为准则工作也并不能确保你在编码的过程中不会犯错。千里之行始于足下。我们应时常和同行们进行codereview。不要惧怕迈出提升自己的第一步，现在就从养成良好编码习惯开始吧！

## **变量**

### 使用有意义且可读性好的变量命名

**Bad:**
```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');
```

**Good:**
```javascript
const currentDate = moment().format('YYYY/MM/DD');
```
**[⬆ 返回目录](#目录)**

使用ES6的const定义常量

反例中使用"var"定义的"常量"是可变的。

在声明一个常量时，该常量在整个程序中都应该是不可变的。

**Bad:**
```javascript
var FIRST_US_PRESIDENT = "George Washington";
```

**Good:**
```javascript
const FIRST_US_PRESIDENT = "George Washington";
```
**[⬆ 返回目录](#目录)**

### 对功能类似的变量名采用统一的命名风格

**Bad:**
```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Good:**
```javascript
getUser();
```
**[⬆ 返回目录](#目录)**

### 使用可检索的名字

我们需要阅读的代码远比我们要写的代码多得多。使代码拥有良好的可读性和可检索性非常重要。不按规则命名变量将是我们的代码晦涩难懂。类似[buddy.js](https://github.com/danielstjules/buddy.js) and[ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md)的工具可以帮助检测我们的命名是否规范。

**Bad:**
```javascript
// 谁知道86400 TM的到底是什么鬼？
setTimeout(blastOff, 86400000);

```

**Good:**
```javascript
// 用大写的const常量定义它
const MILLISECONDS_IN_A_DAY = 86400000;
setTimeout(blastOff, MILLISECONDS_IN_A_DAY);
```
**[⬆ 回到目录](#目录)**

### 使用解释性的变量命名

**Bad:**
```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(address.match(cityZipCodeRegex)[1], address.match(cityZipCodeRegex)[2]);
```

**Good:**
```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```
**[⬆ 回到目录](#目录)**

### 避免绕弯子

显示优于隐式。

**Bad:**
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((l) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // 等等，l是什么来着?
  dispatch(l);
});
```

**Good:**
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((location) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```
**[⬆ 回到目录](#目录)**

### 避免重复描述

如果类名或者对象名已经描述清楚了，就不要再在变量命名上重复。

**Bad:**
```javascript
const Car = {
  carMake: 'Honda',
  carModel: 'Accord',
  carColor: 'Blue'
};

function paintCar(car) {
  car.carColor = 'Red';
}
```

**Good:**
```javascript
const Car = {
  make: 'Honda',
  model: 'Accord',
  color: 'Blue'
};

function paintCar(car) {
  car.color = 'Red';
}
```
**[⬆ 回到目录](#目录)**

### 避免无意义的循环和条件判断

**Bad:**
```javascript
function createMicrobrewery(name) {
  const breweryName = name || 'Hipster Brew Co.';
  // ...
}
```

**Good:**
```javascript
function createMicrobrewery(breweryName = 'Hipster Brew Co.') {
  // ...
}
```

> 相反我个人在写代码过程中却比较喜欢用短路运算代替条件判断，因为显得代码简洁，比如ajax接口容错处理中用`if(result.error && (result.error.message || "系统异常!"))`，短路运算将多重判断简写为一行代码。
**[⬆ 回到目录](#目录)**

## **函数**

### 函数参数最多不超过两个

限制函数参数数量在测试过程中显得尤为重要。因为当函数参数个数超出3个以上时，参数异常组合将会倍增导致难于测试。

一到两个参数最为理想，三个应该尽量避免。

如果一个函数拥有超过两个参数那就意味着你想要做的有很多，这种情况下，将多个参数合成到一个对象里往往更为合适。

**Bad:**
```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}
```

**Good:**
```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
});
```
**[⬆ 回到目录](#目录)**

### 保持函数功能单一性

这是软件工程中是最为重要的一条准则。一个函数完成的功能越多越难于重构和测试。保持功能单一有助于代码重构并能保持代码更为整洁。

**Bad:**
```javascript
function emailClients(clients) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Good:**
```javascript
function emailClients(clients) {
  clients
    .filter(isClientActive)
    .forEach(email);
}

function isClientActive(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```
**[⬆ 回到目录](#目录)**

### 函数命名应该突显出它的功能

**Bad:**
```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// 很难从函数名称看出是什么被添加进Date
addToDate(date, 1);
```

**Good:**
```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```
**[⬆ 回到目录](#目录)**

### Functions should only be one level of abstraction
### 保持函数只有一层抽象

当在你的函数中有多于一个抽象级别时， 你的函数通常做了太多事情。 拆分函数将会提升重用性和测试性。

**Bad:**
```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach((token) => {
    // lex...
  });

  ast.forEach((node) => {
    // parse...
  });
}
```

**Good:**
```javascript
function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      tokens.push( /* ... */ );
    });
  });

  return tokens;
}

function lexer(tokens) {
  const ast = [];
  tokens.forEach((token) => {
    ast.push( /* ... */ );
  });

  return ast;
}

function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const ast = lexer(tokens);
  ast.forEach((node) => {
    // parse...
  });
}
```
**[⬆ 回到目录](#目录)**

### 避免冗余代码

竭尽全力避免冗余代码。 冗余代码是不好的， 因为它意味着当你需要修改一些逻辑时会有多个地方 需要修改。

想象一下你在经营一家餐馆， 你需要记录所有的库存西红柿， 洋葱， 大蒜， 各种香料等等。 如果你有多 个记录列表， 当你用西红柿做一道菜时你得更新多个列表。 如果你只有一个列表， 就只有一个地方需要更 新！

你有冗余代码通常是因为你有两个或多个稍微不同的东西， 它们共享大部分， 但是它们的不同之处迫使你使 用两个或更多独立的函数来处理大部分相同的东西。 移除冗余代码意味着创建一个可以处理这些不同之处的 抽象的函数/模块/类。

合理抽象很重要， 这是为什么要你遵循 Classes 那一章的 SOLID 的原因。 不好的抽象比冗余代码更差， 所以要谨慎行事。 既然已经这么说了， 如果你能够做出一个好的抽象， 才去做。 不要重复你自己， 否则你会发现当你要修改一个东西时时刻需要修改多个地方。

**Bad:**
```javascript
function showDeveloperList(developers) {
  developers.forEach((developer) => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach((manager) => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Good:**
```javascript
function showEmployeeList(employees) {
  employees.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    let portfolio = employee.getGithubLink();

    if (employee.type === 'manager') {
      portfolio = employee.getMBAProjects();
    }

    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```
**[⬆ 回到目录](#目录)**

### 使用 Object.assign 设置对象属性

**Bad:**
```javascript
const menuConfig = {
  title: null,
  body: 'Bar',
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || 'Foo';
  config.body = config.body || 'Bar';
  config.buttonText = config.buttonText || 'Baz';
  config.cancellable = config.cancellable === undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Good:**
```javascript
const menuConfig = {
  title: 'Order',
  // User did not include 'body' key
  buttonText: 'Send',
  cancellable: true
};

function createMenu(config) {
  config = Object.assign({
    title: 'Foo',
    body: 'Bar',
    buttonText: 'Baz',
    cancellable: true
  }, config);

  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```
**[⬆ 回到目录](#目录)**

### 不要用标志位作为函数参数

这通常意味着函数的功能的单一性已经被破坏。此时应考虑对函数进行再次划分。

**Bad:**
```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Good:**
```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```
**[⬆ 回到目录](#目录)**

### 避免副作用(part 1)

当函数产生了除了“接受一个值并返回一个结果”之外的行为时，称该函数产生了副作用。比如写文件、修改全局变量或将你的钱全转给了一个陌生人等。

程序在某些情况下确实需要副作用这一行为，如先前例子中的写文件。这时应该将这些功能集中在一起，不要用多个函数/类修改某个文件。用且只用一个service完成这一需求。

重点是避免这些常见的易犯的错误， 比如在对象之间共享状态而不使用任何结构， 使用任何地方都可以写入 的可变的数据类型， 没有集中化导致副作用。

**Bad:**
```javascript
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
let name = 'Ryan McDermott';

function splitIntoFirstAndLastName() {
  name = name.split(' ');
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Good:**
```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(' ');
}

const name = 'Ryan McDermott';
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```
**[⬆ 回到目录](#目录)**

### 避免副作用 (part 2)

**Bad:**
```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**Good:**
```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date : Date.now() }];
};
```

**[⬆ 回到目录](#目录)**

### 不要写全局函数

在JS中污染全局是一个非常不好的实践，这么做可能和其他库起冲突，且调用你的API的用户在实际环境中得到一个exception前对这一情况是一无所知的。

想象以下例子：如果你想扩展JS中的Array，为其添加一个diff函数显示两个数组间的差异，此时应如何去做？你可以将diff写入Array.prototype，但这么做会和其他有类似需求的库造成冲突。如果另一个库对diff的需求为比较一个数组中首尾元素间的差异呢？

使用ES6中的class对全局的Array做简单的扩展显然是一个更棒的选择。

**Bad:**
```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Good:**
```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```
**[⬆ 回到目录](#目录)**

### 函数编程优于指令式编程

JavaScript 不是 Haskell 那种方式的函数式语言， 但是它有它的函数式风格。 函数式语言更加简洁 并且更容易进行测试， 当你可以使用函数式编程风格时请尽情使用。
**Bad:**
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Good:**
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

const INITIAL_VALUE = 0;

const totalOutput = programmerOutput
  .map((programmer) => programmer.linesOfCode)
  .reduce((acc, linesOfCode) => acc + linesOfCode, INITIAL_VALUE);
```
**[⬆ 回到目录](#目录)**

### 封装条件语句

**Bad:**
```javascript
if (fsm.state === 'fetching' && isEmpty(listNode)) {
  // ...
}
```

**Good:**
```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === 'fetching' && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```
**[⬆ 回到目录](#目录)**

### 避免否定情况的判断

**Bad:**
```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Good:**
```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```
**[⬆ 回到目录](#目录)**

### 避免switch case

这一条至少目前来看，我是避免不了了。

**Bad:**
```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case '777':
        return this.getMaxAltitude() - this.getPassengerCount();
      case 'Air Force One':
        return this.getMaxAltitude();
      case 'Cessna':
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Good:**
```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```
**[⬆ 回到目录](#目录)**

### 避免类型检测 (part 1)

JavaScript 是无类型的， 这意味着你的函数能接受任何类型的参数。 但是有时又会被这种自由咬伤， 于是又尝试在你的函数中做类型检查。 有很多种方式来避免这个， 第一个要考虑的是一致的 API 。

**Bad:**
```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location('texas'));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location('texas'));
  }
}
```

**Good:**
```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location('texas'));
}
```
**[⬆ 回到目录](#目录)**

### 避免类型检测 (part 2)

如果你使用原始的字符串、 整数和数组， 并且你不能使用多态， 但是你依然感觉到有类型检查的需要， 你应该考虑使用 TypeScript 。 它是一个常规 JavaScript 的优秀的替代品， 因为它在标准的 JavaScript 语法之上为你提供静态类型。 对常规 JavaScript 做人工类型检查的问题是需要大量的冗词来仿造类型安 全而不缺失可读性。 保持你的 JavaScript 简洁， 编写良好的测试， 并有良好的代码审阅， 否则使用 TypeScript （就像我说的， 它是一个伟大的替代品）来完成这些。

**Bad:**
```javascript
function combine(val1, val2) {
  if (typeof val1 === 'number' && typeof val2 === 'number' ||
      typeof val1 === 'string' && typeof val2 === 'string') {
    return val1 + val2;
  }

  throw new Error('Must be of type String or Number');
}
```

**Good:**
```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```
**[⬆ 回到目录](#目录)**

### 不要过度优化

有些优化对于执行环境来说(浏览器等)根本不值一提，花时间在值得优化的地方。

**Bad:**
```javascript

// 这里使用变量len是因为在老式浏览器中，
// 直接使用正例中的方式会导致每次循环均重复计算list.length的值，
// 而在现代浏览器中会自动完成优化，这一行为是没有必要的
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Good:**
```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

不得不说我一直写上面Bad示例的代码而且以为很高明！

**[⬆ 回到目录](#目录)**

### 移除僵尸代码

僵死代码和冗余代码同样糟糕。 没有理由在代码库中保存它。 如果它不会被调用， 就删掉它。 当你需要 它时， 它依然保存在版本历史记录中。

**Bad:**
```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');

```

**Good:**
```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');
```
**[⬆ 回到目录](#目录)**

## **对象和数据结构**
### Use getters and setters

JavaScript 没有接口或类型， 所以坚持这个模式是非常困难的， 因为我们没有 public 和 private 关键字。 正因为如此， 使用 getters 和 setters 来访问对象上的数据比简单的在一个对象上查找属性 要好得多。 “为什么？” 你可能会问， 好吧， 原因请看下面的列表：

- 当你想在获取一个对象属性的背后做更多的事情时， 你不需要在代码库中查找和修改每一处访问；
- 使用 set 可以让添加验证变得容易；
- 封装内部实现；
- 使用 getting 和 setting 时， 容易添加日志和错误处理；
- 继承这个类， 你可以重写默认功能；
- 你可以延迟加载对象的属性， 比如说从服务器获取。

**Bad:**
```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0,
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Good:**
```javascript
function makeBankAccount() {
  // this one is private
  let balance = 0;

  // a "getter", made public via the returned object below
  function getBalance() {
    return balance;
  }

  // a "setter", made public via the returned object below
  function setBalance(amount) {
    // ... validate before updating the balance
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance,
  };
}

const account = makeBankAccount();
account.setBalance(100);
```
**[⬆ 回到目录](#目录)**


### 让对象拥有私有成员

可以通过闭包完成。

**Bad:**
```javascript

const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Good:**
```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    },
  };
}

const employee = makeEmployee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```
**[⬆ 回到目录](#目录)**


## **类**
### ES2015/ES6 类优先与 ES5 纯函数

很难为经典的 ES5 类创建可读的的继承、 构造和方法定义。 如果你需要继承（并且感到奇怪为啥你不需 要）， 则优先用 ES2015/ES6的类。 不过， 短小的函数优先于类， 知道你发现你需要更大并且更复杂的 对象。

**Bad:**
```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error('Instantiate Animal with `new`');
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error('Instantiate Mammal with `new`');
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error('Instantiate Human with `new`');
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Good:**
```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() { /* ... */ }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() { /* ... */ }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() { /* ... */ }
}
```
**[⬆ 回到目录](#目录)**


### 使用方法链

这个模式在 JavaScript 中是非常有用的， 并且你可以在许多类库比如 jQuery 和 Lodash 中见到。 它允许你的代码变得富有表现力， 并减少啰嗦。 因为这个原因， 我说， 使用方法链然后再看看你的代码 会变得多么简洁。 在你的类／方法中， 简单的在每个方法的最后返回 this ， 然后你就能把这个类的 其它方法链在一起。

**Bad:**
```javascript
class Car {
  constructor() {
    this.make = 'Honda';
    this.model = 'Accord';
    this.color = 'white';
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car();
car.setColor('pink');
car.setMake('Ford');
car.setModel('F-150');
car.save();
```

**Good:**
```javascript
class Car {
  constructor() {
    this.make = 'Honda';
    this.model = 'Accord';
    this.color = 'white';
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car()
  .setColor('pink')
  .setMake('Ford')
  .setModel('F-150')
  .save();
```
**[⬆ 回到目录](#目录)**

### 组合优先于继承

正如 [*设计模式四人帮*](https://en.wikipedia.org/wiki/Design_Patterns)所述， 如果可能， 你应该优先使用组合而不是继承。 有许多好的理由去使用继承， 也有许多好的理由去使用组合。这个格言 的重点是， 如果你本能的观点是继承， 那么请想一下组合能否更好的为你的问题建模。 很多情况下它真的 可以。

那么你也许会这样想， “我什么时候改使用继承？” 这取决于你手上的问题， 不过这儿有一个像样的列表说 明什么时候继承比组合更好用：
1. 你的继承表示"是一个"的关系而不是"有一个"的关系（人类->动物 vs 用户->用户详情）；
2. 你可以重用来自基类的代码（人可以像所有动物一样行动）；
3. 你想通过基类对子类进行全局的修改（改变所有动物行动时的热量消耗）；

**Bad:**
```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Good:**
```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```
**[⬆ 回到目录](#目录)**

## **健壮性**
### 单一职责原则 (SRP)

正如代码整洁之道所述， “永远不要有超过一个理由来修改一个类”。 给一个类塞满许多功能， 就像你在航 班上只能带一个行李箱一样，这样做的问题你的类不会有理想的内聚性，将会有太多的理由来对它进行修改。 最小化需要修改一个类的次数时很重要的， 因为如果一个类拥有太多的功能， 一旦你修改它的一小部分，将
会很难弄清楚会对代码库中的其它模块造成什么影响。

**Bad:**
```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Good:**
```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}


class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```
**[⬆ 回到目录](#目录)**

### 开闭原则  (OCP)

Bertrand Meyer 说过， “软件实体 (类， 模块， 函数等) 应该为扩展开放， 但是为修改关闭。” 这 是什么意思呢？ 这个原则基本上说明了你应该允许用户添加功能而不必修改现有的代码。

**Bad:**
```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'ajaxAdapter';
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'nodeAdapter';
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === 'ajaxAdapter') {
      return makeAjaxCall(url).then((response) => {
        // transform response and return
      });
    } else if (this.adapter.name === 'httpNodeAdapter') {
      return makeHttpCall(url).then((response) => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}
```

**Good:**
```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'ajaxAdapter';
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'nodeAdapter';
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then((response) => {
      // transform response and return
    });
  }
}
```
**[⬆ 回到目录](#目录)**

### Liskov替换原则 (LSP)

这是针对一个非常简单的里面的一个恐怖意图， 它的正式定义是： “如果 S 是 T 的一个子类型， 那么类 型为 T 的对象可以被类型为 S 的对象替换（例如， 类型为 S 的对象可作为类型为 T 的替代品）儿不需 要修改目标程序的期望性质 （正确性、 任务执行性等）。” 这甚至是个恐怖的定义。

最好的解释是， 如果你又一个基类和一个子类， 那个基类和字类可以互换而不会产生不正确的结果。 这可 能还有有些疑惑， 让我们来看一下这个经典的正方形与矩形的例子。 从数学上说，一个正方形是一个矩形， 但是你用 "is-a" 的关系用继承来实现， 你将很快遇到麻烦。

**Bad:**
```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach((rectangle) => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // BAD: Returns 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Good:**
```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach((shape) => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```
**[⬆ 回到目录](#目录)**

### 接口隔离原则 (ISP)

JavaScript 没有接口， 所以这个原则不想其它语言那么严格。 不过， 对于 JavaScript 这种缺少类 型的语言来说， 它依然是重要并且有意义的。

接口隔离原则说的是 “客户端不应该强制依赖他们不需要的接口。” 在 JavaScript 这种弱类型语言中， 接口是隐式的契约。

在 JavaScript 中能比较好的说明这个原则的是一个类需要一个巨大的配置对象。 不需要客户端去设置大 量的选项是有益的， 因为多数情况下他们不需要全部的设置。 让它们变成可选的有助于防止出现一个“胖接 口”。

**Bad:**
```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  animationModule() {} // Most of the time, we won't need to animate when traversing.
  // ...
});

```

**Good:**
```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  options: {
    animationModule() {}
  }
});
```
**[⬆ 回到目录](#目录)**

### 依赖反转原则 (DIP)

这个原则阐述了两个重要的事情： 

1. 高级模块不应该依赖于低级模块， 两者都应该依赖与抽象； 
2. 2. 抽象不应当依赖于具体实现， 具体实现应当依赖于抽象。

这个一开始会很难理解， 但是如果你使用过 Angular.js ， 你应该已经看到过通过依赖注入来实现的这 个原则， 虽然他们不是相同的概念， 依赖反转原则让高级模块远离低级模块的细节和创建， 可以通过 DI 来实现。 这样做的巨大益处是降低模块间的耦合。 耦合是一个非常糟糕的开发模式， 因为会导致代码难于 重构。

如上所述， JavaScript 没有接口， 所以被依赖的抽象是隐式契约。 也就是说， 一个对象/类的方法和 属性直接暴露给另外一个对象/类。 在下面的例子中， 任何一个 Request 模块的隐式契约 InventoryTracker 将有一个 requestItems 方法。

> 确实有点晦涩。

**Bad:**
```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // BAD: We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(['apples', 'bananas']);
inventoryTracker.requestItems();
```

**Good:**
```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ['WS'];
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
const inventoryTracker = new InventoryTracker(['apples', 'bananas'], new InventoryRequesterV2());
inventoryTracker.requestItems();
```
**[⬆ 回到目录](#目录)**

## **可测试性**
Testing is more important than shipping. If you have no tests or an
inadequate amount, then every time you ship code you won't be sure that you
didn't break anything. Deciding on what constitutes an adequate amount is up
to your team, but having 100% coverage (all statements and branches) is how
you achieve very high confidence and developer peace of mind. This means that
in addition to having a great testing framework, you also need to use a
[good coverage tool](http://gotwarlost.github.io/istanbul/).

There's no excuse to not write tests. There's [plenty of good JS test frameworks]
(http://jstherightway.org/#testing-tools), so find one that your team prefers.
When you find one that works for your team, then aim to always write tests
for every new feature/module you introduce. If your preferred method is
Test Driven Development (TDD), that is great, but the main point is to just
make sure you are reaching your coverage goals before launching any feature,
or refactoring an existing one.

### Single concept per test

**Bad:**
```javascript
import assert from 'assert';

describe('MakeMomentJSGreatAgain', () => {
  it('handles date boundaries', () => {
    let date;

    date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    assert.equal('1/31/2015', date);

    date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);

    date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```

**Good:**
```javascript
import assert from 'assert';

describe('MakeMomentJSGreatAgain', () => {
  it('handles 30-day months', () => {
    const date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    assert.equal('1/31/2015', date);
  });

  it('handles leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);
  });

  it('handles non-leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```
**[⬆ 回到目录](#目录)**

## **并发性**
### 使用 Promises而不是callback

回调不够简洁， 因为他们会产生过多的嵌套。 在 ES2015/ES6 中， Promises 已经是内置的全局类型 了，使用它们吧！

**Bad:**
```javascript
import { get } from 'request';
import { writeFile } from 'fs';

get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', (requestErr, response) => {
  if (requestErr) {
    console.error(requestErr);
  } else {
    writeFile('article.html', response.body, (writeErr) => {
      if (writeErr) {
        console.error(writeErr);
      } else {
        console.log('File written');
      }
    });
  }
});

```

**Good:**
```javascript
import { get } from 'request';
import { writeFile } from 'fs';

get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((response) => {
    return writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });

```
**[⬆ 回到目录](#目录)**

### Async/Await 比 Promises 更加简洁

Promises 是回调的一个非常简洁的替代品， 但是 ES2017/ES8 带来的 async 和 await 提供了一个 更加简洁的解决方案。 你需要的只是一个前缀为 async 关键字的函数， 接下来就可以不需要 then 函数链来编写逻辑了。 如果你能使用 ES2017/ES8 的高级功能的话， 今天就使用它吧！

**Bad:**
```javascript
import { get } from 'request-promise';
import { writeFile } from 'fs-promise';

get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((response) => {
    return writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });

```

**Good:**
```javascript
import { get } from 'request-promise';
import { writeFile } from 'fs-promise';

async function getCleanCodeArticle() {
  try {
    const response = await get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin');
    await writeFile('article.html', response);
    console.log('File written');
  } catch(err) {
    console.error(err);
  }
}
```
**[⬆ 回到目录](#目录)**


## **容错性**

抛出错误是一件好事情！ 他们意味着当你的程序有错时运行时可以成功确认， 并且通过停止执行当前堆栈 上的函数来让你知道， 结束当前进程（在 Node 中）， 在控制台中用一个堆栈跟踪提示你。

### 不要忽略捕捉错误

对捕捉到的错误不做任何处理不能给你修复错误或者响应错误的能力。 向控制台记录错误 (console.log) 也不怎么好， 因为往往会丢失在海量的控制台输出中。 如果你把任意一段代码用 try/catch 包装那就 意味着你想到这里可能会错， 因此你应该有个修复计划， 或者当错误发生时有一个代码路径。

**Bad:**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Good:**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### 不要忽略被拒绝的 promise

与你不应忽略来自 try/catch 的错误的原因相同。

**Bad:**
```javascript
getdata()
  .then((data) => {
    functionThatMightThrow(data);
  })
  .catch((error) => {
    console.log(error);
  });
```

**Good:**
```javascript
getdata()
  .then((data) => {
    functionThatMightThrow(data);
  })
  .catch((error) => {
    // One option (more noisy than console.log):
    console.error(error);
    // Another option:
    notifyUserOfError(error);
    // Another option:
    reportErrorToService(error);
    // OR do all three!
  });
```

**[⬆ 回到目录](#目录)**


## **格式化**

格式化是主观的。 就像其它规则一样， 没有必须让你遵守的硬性规则。 重点是不要因为格式去争论， 这 里有大量的工具来自动格式化， 使用其中的一个即可！因为做为工程师去争论格式化就是在浪费时间和金钱。

针对自动格式化工具不能涵盖的问题（缩进、 制表符还是空格、 双引号还是单引号等），这里有一些指南。

> 在IDE里找个格式化代码的插件，简单省事。

### 保持大小写一致

JavaScript 是无类型的， 所以大小写告诉你关于你的变量、 函数等的很多事情。 这些规则是主观的， 所以你的团队可以选择他们想要的。 重点是， 不管你们选择了什么， 要保持一致。

**Bad:**
```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const Artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Good:**
```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```
**[⬆ 回到目录](#目录)**


### 函数的调用方与被调用方应该靠近

如果一个函数调用另一个， 则在代码中这两个函数的竖直位置应该靠近。 理想情况下，保持被调用函数在被 调用函数的正上方。 我们倾向于从上到下阅读代码， 就像读一章报纸。 由于这个原因， 保持你的代码可 以按照这种方式阅读。

**Bad:**
```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers');
  }

  lookupManager() {
    return db.lookup(this.employee, 'manager');
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(user);
review.perfReview();
```

**Good:**
```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers');
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, 'manager');
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ 回到目录](#目录)**

## **注释**
### 仅仅对包含复杂业务逻辑的东西进行注释

注释是代码的解释， 不是要求。 多数情况下， 好的代码就是文档。

**Bad:**
```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = ((hash << 5) - hash) + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Good:**
```javascript

function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = ((hash << 5) - hash) + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}

```
**[⬆ 回到目录](#目录)**

### 不要在代码库中保存注释掉的代码

因为有版本控制， 把旧的代码留在历史记录即可。

**Bad:**
```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Good:**
```javascript
doStuff();
```
**[⬆ 回到目录](#目录)**

### 不要有日志式的评论

记住， 使用版本控制！ 不需要僵尸代码， 注释调的代码， 尤其是日志式的评论。 使用 git log 来 获取历史记录。

**Bad:**
```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Good:**
```javascript
function combine(a, b) {
  return a + b;
}
```
**[⬆ 回到目录](#目录)**

### 避免不必要的占位符

它们仅仅添加了干扰。 让函数和变量名称与合适的缩进和格式化为你的代码提供视觉结构。

**Bad:**
```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Good:**
```javascript
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

const actions = function() {
  // ...
};
```
**[⬆ 回到目录](#目录)**