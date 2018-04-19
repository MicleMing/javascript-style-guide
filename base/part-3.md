## 类和构造器
- 9.1 总是使用 `class`。避免直接操作 `prototype` 。
> 为什么? 因为 class 语法更为简洁更易读。
```javascript
// bad
function Queue(contents = []) {
  this.queue = [...contents];
}
Queue.prototype.pop = function () {
  const value = this.queue[0];
  this.queue.splice(0, 1);
  return value;
};

// good
class Queue {
  constructor(contents = []) {
    this.queue = [...contents];
  }
  pop() {
    const value = this.queue[0];
    this.queue.splice(0, 1);
    return value;
  }
}
```

- 9.2 使用 `extends` 继承。
> 为什么？因为 `extends` 是一个内建的原型继承方法并且不会破坏 `instanceof`。
```javascript
// bad
const inherits = require('inherits');
function PeekableQueue(contents) {
  Queue.apply(this, contents);
}
inherits(PeekableQueue, Queue);
PeekableQueue.prototype.peek = function () {
  return this.queue[0];
};

// good
class PeekableQueue extends Queue {
  peek() {
    return this.queue[0];
  }
}
```

- 9.3 方法可以返回 this 来帮助链式调用。
```javascript
// bad
Jedi.prototype.jump = function () {
  this.jumping = true;
  return true;
};

Jedi.prototype.setHeight = function (height) {
  this.height = height;
};

const luke = new Jedi();
luke.jump(); // => true
luke.setHeight(20); // => undefined

// good
class Jedi {
  jump() {
    this.jumping = true;
    return this;
  }

  setHeight(height) {
    this.height = height;
    return this;
  }
}

const luke = new Jedi();

luke.jump()
  .setHeight(20);
```

- 9.4 可以写一个自定义的 toString() 方法，但要确保它能正常运行并且不会引起副作用
```js
class Jedi {
  constructor(options = {}) {
    this.name = options.name || 'no name';
  }

  getName() {
    return this.name;
  }

  toString() {
    return `Jedi - ${this.getName()}`;
  }
}
```

- 9.5 在一个类中，如果没有显式的编写`constructor`，那么会有一个默认的`constructor`, 所以不需要写一个空的`constructor`或者去代理父类的`constructor`
```js
// bad
class Jedi {
  constructor() {}

  getName() {
    return this.name;
  }
}

// bad
class Rey extends Jedi {
  constructor(...args) {
    super(...args);
  }
}

// good
class Rey extends Jedi {
  constructor(...args) {
    super(...args);
    this.name = 'Rey';
  }
}
```

- 9.6 避免重复声明类成员.`eslint: no-dupe-class-members`
> 为什么？重复声明的类成员时，仅仅是最后一次的声明的成员会起效，前面声明的会被覆盖。- 重复声明类成员很可能是出现了一个bug
```js
// bad
class Foo {
  bar() { return 1; }
  bar() { return 2; }
}

// good
class Foo {
  bar() { return 1; }
}

// good
class Foo {
  bar() { return 2; }
}
```

## 模块
- 10.1 总是使用模组 (import/export) 而不是其他非标准模块系统。你可以编译为你喜欢的模块系统。
> 为什么？模块就是未来，让我们开始迈向未来吧。
```js
// bad
const AirbnbStyleGuide = require('./AirbnbStyleGuide');
module.exports = AirbnbStyleGuide.es6;

// ok
import AirbnbStyleGuide from './AirbnbStyleGuide';
export default AirbnbStyleGuide.es6;

// best
import { es6 } from './AirbnbStyleGuide';
export default es6;
```

- 10.2 不要使用通配符 import。
> 为什么？这样能确保你只有一个默认 `export`。
```js
// bad
import * as AirbnbStyleGuide from './AirbnbStyleGuide';

// good
import AirbnbStyleGuide from './AirbnbStyleGuide';
```

- 10.3 不要从 `import` 中直接 `export`。
> 为什么？虽然一行代码简洁明了，但让 `import` 和 `export` 各司其职让事情能保持一致。
```js
// bad
// filename es6.js
export { es6 as default } from './AirbnbStyleGuide';

// good
// filename es6.js
import { es6 } from './AirbnbStyleGuide';
export default es6;
```

- 10.4 只在同一个地方导入具有相同路径的模块。`eslint: no-duplicate-imports`
> 为什么？在不同的地方导入具有相同路径的模块会导致代码很难维护
```js
// bad
import foo from 'foo';
// … some other imports … //
import { named1, named2 } from 'foo';

// good
import foo, { named1, named2 } from 'foo';

// good
import foo, {
  named1,
  named2,
} from 'foo';
```

- 10.5 不要导出可变的绑定。`eslint: import/no-mutable-exports`
> 为什么？通常情况下是要避免导出一个可变的绑定，除非在特殊情况下,比如某些技术实现上需要这么做
```js
// bad
let foo = 3;
export { foo };

// good
const foo = 3;
export { foo };
```

- 10.6 在只有一个`export`的模块中，优先使用`default export`。` eslint: import/prefer-default-export`
> 为什么？鼓励更多的文件只导出一件东西，这样可读性和可维护性会更好
```js
// bad
export function foo() {}

// good
export default function foo() {}
```

- 10.7 把所有的`import`放到非`import`语句之上.`eslint: import/first`
> 为什么？因为`import`语句会被提升,所以把他们放到顶部以防止出现意外的行为.
```js
// bad
import foo from 'foo';
foo.init();

import bar from 'bar';

// good
import foo from 'foo';
import bar from 'bar';

foo.init();
```

- 10.8 当导出多个内容是，每个内容项都应该独立一行，就像数组和对象一样。
```js
// bad
import {longNameA, longNameB, longNameC, longNameD, longNameE} from 'path';

// good
import {
  longNameA,
  longNameB,
  longNameC,
  longNameD,
  longNameE,
} from 'path';
```

- 10.9 在模块导入声明中，不得使用`webpack loader`的语法。`eslint: import/no-webpack-loader-syntax`
> 在`import`中使用webpack的语法会使代码和模块加载器产生耦合，所以更好的方式是在`webpack.config.js`中使用`loader`的语法
```js
// bad
import fooSass from 'css!sass!foo.scss';
import barCss from 'style!css!bar.css';

// good
import fooSass from 'foo.scss';
import barCss from 'bar.css';
```