##### 1. 谈谈你对作用域链的理解(有赞一面)

> 小提示：同类型的问题还可以是原型链、继承、闭包等，这种概念性的问题你肯定不是一句两句能说清楚的，建议在理解之后自己尝试总结一下，如何把重要的知识点用简短的话语说明白。

了解作用域链之前我们要知道一下几个概念：

- 函数的生命周期
- 变量和函数的声明
- Activetion Object（AO）、Variable Object（VO）

函数的生命周期：

- 创建：JS解析引擎进行预解析，会将函数声明提前，同时将该函数放到全局作用域中或当前函数的上一级函数的局部作用域中。
- 执行：JS引擎会将当前函数的局部变量和内部函数进行声明提前，然后再执行业务代码，当函数执行完退出时，释放该函数的执行上下文，并注销该函数的局部变量。

变量和函数的声明：如果变量名和函数名声明时相同，函数优先声明。

Activetion Object（AO）、Variable Object（VO）：

- AO：Activetion Object（活动对象）
- VO：Variable Object（变量对象）

VO对应的是函数创建阶段，JS解析引擎进行预解析时，所有的变量和函数的声明，统称为Variable Object。该变量与执行上下文相关，知道自己的数据存储在哪里，并且知道如何访问。VO是一个与执行上下文相关的特殊对象，它存储着在上下文中声明的以下内容：

- 变量 (var, 变量声明);
- 函数声明 (FunctionDeclaration, 缩写为FD);
- 函数的形参

AO对应的是函数执行阶段，当函数被调用执行时，会建立一个执行上下文，该执行上下文包含了函数所需的所有变量，该变量共同组成了一个新的对象就是Activetion Object。该对象包含了：

- 函数的所有局部变量
- 函数的所有命名参数
- 函数的参数集合
- 函数的this指向

作用域链：

当代码在一个环境中创建时，会创建变量对象的一个作用域链（scope chain）来保证对执行环境有权访问的变量和函数。作用域第一个对象始终是当前执行代码所在环境的变量对象（VO）。如果是函数执行阶段，那么将其activation object（AO）作为作用域链第一个对象，第二个对象是上级函数的执行上下文AO，下一个对象依次类推。

在《JavaScript深入之变量对象》中讲到，当查找变量的时候，会先从当前上下文的变量对象中查找，如果没有找到，就会从父级(词法层面上的父级)执行上下文的变量对象中查找，一直找到全局上下文的变量对象，也就是全局对象。这样由多个执行上下文的变量对象构成的链表就叫做作用域链。



##### 2. 你知道`null`和`undefined`有什么区别么(有赞一面)

##### 3. 闭包有什么作用

###### 闭包定义：

当函数可以记住并访问所在的词法作用域，就产生了闭包。即使函数是在当前词法作用域之外执行。
闭包使得函数在外部调用时，可以继续访问定义时的词法作用域。
只要使用了回调函数，实际上就是在使用闭包！
—— 摘自《你所不知道的js》

简单来说就是：

- 父函数的返回值是一个子函数
- 子函数可以访问父函数中的作用域

###### 缺陷：

- 闭包引用了祖先函数的作用域，所以滥用闭包会有内存问题
- 让函数的变量都保存在内存中，内存消耗变大。使用不当会造成内存泄漏

###### 作用

- 封装块级作用域

```
// IIFE的目的是为了隔离作用域，防止污染全局命名空间。
(function() {
    //这里是块级作用域
})();
```

- 封装私有变量

- 实现模块化

##### 

![reflow](https://github.com/askwuxue/arithmetic-hand-code/blob/master/images/reflow.awebp)

##### 4. 浏览器垃圾回收机制

##### 5. 了解Event Loop么

> 小提示：这个题目问到的概率还是蛮大的，这里面试官询问了我浏览器端和Node端的Event Loop有什么不同点。如果想要知道更多浏览器端的Event Loop机制可以查看我之前写的文章[你真的理解$nextTick么/JS引擎线程和事件触发线程/事件循环机制](https://juejin.cn/post/6844903843197616136#heading-3)。

事件触发线程管理的任务队列是如何产生的呢？事实上这些任务就是从JS引擎线程本身产生的，主线程在运行时会产生执行栈，栈中的代码调用某些异步API时会在任务队列中添加事件，栈中的代码执行完毕后，就会读取任务队列中的事件，去执行事件对应的回调函数，如此循环往复，形成事件循环机制。JS中有两种任务类型：微任务（microtask）和宏任务（macrotask），在ES6中，microtask称为 jobs，macrotask称为 task：

- 宏任务： script （主代码块）、`setTimeout` 、`setInterval` 、`setImmediate` 、I/O 、UI rendering
- 微任务：`process.nextTick`（Nodejs） 、`Promise` 、`Object.observe` 、`MutationObserver`

Node.js中Event Loop和浏览器中Event Loop有什么区别

```
   ┌───────────────────────┐
┌─>│        timers         │<————— 执行 setTimeout()、setInterval() 的回调
│  └──────────┬────────────┘
|             |<-- 执行所有 Next Tick Queue 以及 MicroTask Queue 的回调
│  ┌──────────┴────────────┐
│  │     pending callbacks │<————— 执行由上一个 Tick 延迟下来的 I/O 回调（待完善，可忽略）
│  └──────────┬────────────┘
|             |<-- 执行所有 Next Tick Queue 以及 MicroTask Queue 的回调
│  ┌──────────┴────────────┐
│  │     idle, prepare     │<————— 内部调用（可忽略）
│  └──────────┬────────────┘     
|             |<-- 执行所有 Next Tick Queue 以及 MicroTask Queue 的回调
|             |                   ┌───────────────┐
│  ┌──────────┴────────────┐      │   incoming:   │ - (执行几乎所有的回调，除了 close callbacks、timers、setImmediate)
│  │         poll          │<─────┤  connections, │ 
│  └──────────┬────────────┘      │   data, etc.  │ 
│             |                   |               | 
|             |                   └───────────────┘
|             |<-- 执行所有 Next Tick Queue 以及 MicroTask Queue 的回调
|  ┌──────────┴────────────┐      
│  │        check          │<————— setImmediate() 的回调将会在这个阶段执行
│  └──────────┬────────────┘
|             |<-- 执行所有 Next Tick Queue 以及 MicroTask Queue 的回调
│  ┌──────────┴────────────┐
└──┤    close callbacks    │<————— socket.on('close', ...)
   └───────────────────────┘
复制代码
```

Node.js中宏任务分成了几种类型，并且放在了不同的task queue里。不同的task queue在执行顺序上也有区别，微任务放在了每个task queue的末尾：

- `setTimeout/setInterval` 属于 timers 类型；
- `setImmediate` 属于 check 类型；
- socket 的 close 事件属于 close callbacks 类型；
- 其他 MacroTask 都属于 poll 类型。
- `process.nextTick` 本质上属于 MicroTask，但是它先于所有其他 MicroTask 执行；
- 所有 MicroTask 的执行时机在不同类型的 MacroTask 切换后。
- idle/prepare 仅供内部调用，我们可以忽略。
- pending callbacks 不太常见，我们也可以忽略。

##### 6. `bind`的源码实现

> 小提示：这里我回答使用函数柯里化加上`apply`或者`call`可实现`bind`，面试官追问了一些具体的实现细节。

后来我自己粗糙的实现了一下，仅供参考：

```
Function.prototype.myCall = function (obj) {
  obj.fn = this
  let args = [...arguments].splice(1)
  let result = obj.fn(...args)
  delete obj.fn
  return result
}

Function.prototype.myApply = function (obj) {
  obj.fn = this
  let args = arguments[1]
  let result
  if (args) {
    result = obj.fn(...args)
  } else {
    result = obj.fn()
  }

  delete obj.fn

  return result
}

Function.prototype.myBind = function (obj) {
  let context = obj || window
  let _this = this
  let _args = [...arguments].splice(1)

  return function () {
    let args = arguments
    // 产生副作用
    // return obj.fn(..._args, ...args)
    return _this.apply(context, [..._args, ...args])
  }
}

function myFun (argumentA, argumentB) {
  console.log(this.value)
  console.log(argumentA)
  console.log(argumentB)
  return this.value
}

let obj = {
  value: 'ziyi2'
}
console.log(myFun.myCall(obj, 11, 22))
console.log(myFun.myApply(obj, [11, 22]))
console.log(myFun.myBind(obj, 33)(11, 22))
```

##### 7. 说说DOM事件流

##### 8. 在ES5中如何实现继承

> 小提示：这里我说了很多，从借用构造函数到组合继承到寄生组合继承，但面试官其实最想听到的是寄生组合继承。面试官还追问我具体要如何实现寄生组合继承。当然这里其实问的问题还可以很多，比如ES6的类继承和ES5中的继承有什么区别。

如果对于继承以及继承的区别不是很清楚的，可以随便看看我之前写的大笔记[js类和继承](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fziyi2%2Fjs%2Fblob%2Fmaster%2FJS%E7%B1%BB%E5%92%8C%E7%BB%A7%E6%89%BF.md)。

##### 9. 消抖和节流

> 小提示：面试官只是问了一下具体的使用场景，没有问实现原理。

###### 简单消抖

```
function debounce (fn, wait = 1000) {
  let timeOutId

  return function () {
    let context = this

    if (timeOutId) {
      clearTimeout(timeOutId)
    }

    timeOutId = setTimeout(() => {
      fn.apply(context, arguments)
    }, wait)
  }
}
复制代码
```

###### 带立即执行参数的消抖

```
function debounceImmediate (fn, wait = 1000, immediate) {
  let timeOutId, context, args

  const later = (immediate) => setTimeout(() => {
    if (!immediate) {
      fn.apply(context, args)
      timeOutId = context = args = null
    }
  }, wait)

  return function () {
    if (!timeOutId) {
      timeOutId = later(true)

      if (immediate) {
        fn.apply(this, arguments)
      }

      context = this
      args = arguments
    } else {
      clearTimeout(timeOutId)
      timeOutId = later(false)
    }
  }
}
复制代码
```

###### 节流

```
function throttle (fn, wait) {
  let timeoutId = null
  return function () {
    let context = this
    if (!timeoutId) {
      timeoutId = setTimeout(() => {
        fn.apply(context, arguments)
        timeoutId = null
      }, wait)
    }
  }
}
```

##### 10. 绝对定位

- 一旦给元素加上`absolute`或`float`就相当于给元素加上了`display:block`
- `absolute`元素覆盖正常文档流内元素（不用设z-index，自然覆盖）
- 可以减少重绘和回流的开销（如`absolute+ top:-9999em`，或`absolute + visibility:hidden`，将动画效果放到`absolute`元素中）

###### 属性介绍

- `static`，默认值。位置设置为static的元素，它始终会处于文档流给予的位置。
- `inherit`，规定应该从父元素继承 position 属性的值。但是任何的版本的 Internet Explorer （包括 IE8）都不支持属性值 “inherit”。
- `fixed`，生成绝对定位的元素。默认情况下，可定位于相对于浏览器窗口的指定坐标。元素的位置通过 “left”, “top”, “right” 以及 “bottom” 属性进行规定。不论窗口滚动与否，元素都会留在那个位置。但当祖先元素具有`transform`属性且不为none时，就会相对于祖先元素指定坐标，而不是浏览器窗口。
- `absolute`，生成绝对定位的元素，相对于距该元素最近的已定位的祖先元素进行定位。此元素的位置可通过 “left”、”top”、”right” 以及 “bottom” 属性来规定。
- `relative`，生成相对定位的元素，相对于该元素在文档中的初始位置进行定位。通过 “left”、”top”、”right” 以及 “bottom” 属性来设置此元素相对于自身位置的偏移。

浮动、绝对定位和固定定位会脱离文档流，相对定位不会脱离文档流，绝对定位相对于该元素最近的已定位的祖先元素，如果没有一个祖先元素设置定位，那么参照物是body层。

绝对定位相对于包含块的起始位置：

- 如果祖先元素是块级元素，包含块则设置为该元素的内边距边界。
- 如果祖先元素是行内元素，包含块则设置为该祖先元素的内容边界。

###### 问答：

- 定位的元素的起始位置为父包含块的内边距（不会在border里，除非使用负值，会在padding里）
- 定位的元素的margin还是能起作用的
- background属性是会显示在border里的
- z-index是有层叠层级的，需要考虑同一个层叠上下文的层叠优先级
- z-index是负值不会覆盖包含块的背景色（但是如果有内容，会被包含块的内容覆盖）
- z-index的值影响的元素是定位元素以及flex盒子
- 上面一个定位元素，下面一个正常流的元素，定位元素会覆盖在正常流元素之上，除非给z-index是负值
- 页面根元素html天生具有层叠上下文，称之为“根层叠上下文”

##### 11. 解释下面输出结果

```javascript
async function async1() {
  console.log('1')
  await async2()
  console.log('2')
}
async function async2() {
  console.log('12')
  await async3()
  console.log('3')
}
async function async3() {
  console.log('13')
}
console.log('4')
setTimeout(function() {
  console.log('5')
  new Promise(function(resolve) {
    console.log('6')
    resolve()
  }).then(function() {
    console.log('7')
  })
})

setTimeout(function() {
  console.log('8')
}, 0)
async1()
new Promise(function(resolve) {
  console.log('9')
  resolve()
}).then(function() {
  console.log('10')
})
console.log('11')
复制代码
```

##### 12. 手写promise.all

##### 13. 实现以下函数

```javascript
写一个加法函数sum，支持sum(1)(2)(3,4)(5,6,7....)  
console.log(sum(1,2,3)(4).run()) => 输出 10
console.log(sum(1,2,3)(4)()) => 输出 10
```

##### 14.Bigint和Number的区别

Number类型的数字**有精度限制**，数值的精度只能到 53 个二进制位（相当于 16 个十进制位, `正负9007199254740992`），大于这个范围的整数，就无法精确表示了。

Bigint**没有位数的限制，任何位数的整数都可以精确表示**。但是其只能用于表示整数，且为了与Number进行区分，BigInt 类型的数据必须添加后缀n。BigInt 可以使用负号（-），但是不能使用正号（+）。

另外number类型的数字和Bigint类型的数字**不能**混合计算。

##### 15. defer和async的区别

```
大家应该都知道在script标签内有这两个属性async和defer，例如<script src="./home.js" async defer></script>
```

**defer**：中文意思是延迟。用途是表示脚本会被延迟到整个页面都解析完毕后再运行。因此，在`<script>`元素中设置defer属性，相当于告诉浏览器立即下载，但延迟执行。
HTML5规范要求脚本按照它们出现的`先后顺序执行`，因此第一个延迟脚本会先于第二个延迟脚本执行,但执行脚本之间`存在依赖，需要有执行的先后顺序时`，就可以使用`defer`,延迟执行。我觉得把script脚本放在body底部和defer差不多。

**async**：中文意思是异步，这个属性与defer类似，都用于改变处理脚本的行为。同样与defer类似，async只适用于外部脚本文件，并告诉浏览器立即下载文件。当页面继续进行解析时，脚本将被执行。与defer不同的是，标记为async的脚本并不保证按照它们的先后顺序执行。
 指定async属性的目的是不让页面等待两个脚本下载和执行，从而`异步加载页面`其他内容,这使用于之间`互不依赖`的各脚本。

看到这里，就能知道其的一些作用了

当网页交给浏览器的HTML解析器转变成一系列的词语（Token）。解释器根据词语构建节点（Node），形成DOM树。因为JavaScript代码可能会修改DOM树的结构，所以节点是JavaScript代码的话，就需要停止当前DOM树的创建，直到JavaScript的资源加载并被JavaScript引擎执行后才继续DOM树的创建。
 这里就会产生**阻塞**，出现**白屏问题**(白屏问题优化有很多方面，这里就脚本阻塞这一小点)，我们就可以使用`async和defer`属性来解决JavaScript脚本阻塞问题。

当然最稳妥的办法还是把script标签放置在body的底部，没有兼容性问题，不会因此产生白屏问题，没有执行顺序问题。

最后总结：

- 如果 async="async"：脚本相对于页面的其余部分异步地执行（当页面继续进行解析时，脚本将被执行）
- 如果不使用 async 且 defer="defer"：脚本将在页面完成解析时执行
- 如果既不使用 async 也不使用 defer：在浏览器继续解析页面之前，立即读取并执行脚本

##### 16. async await对比promise的优缺点

###### **async/await优点**：

 a. 它做到了真正的串行的同步写法，代码阅读相对容易

b. 对于条件语句和其他流程语句比较友好，可以直接写到判断条件里面

```js
function a() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(222)
      }, 2222)
    })
  };
async function f() {
    try {
      if ( await a() === 222) {
        console.log('yes, it is!') // 会打印
      }
    } catch (err) {
      // ...
    }
  }
复制代码
```

c. 处理复杂流程时，在代码清晰度方面有优势

###### **async/await缺点**：

 a. 无法处理promise返回的reject对象，要借助try...catch...

b. 用 await 可能会导致性能问题，因为 await 会阻塞代码，也许之后的异步代码并不依赖于前者，但仍然需要等待前者完成，导致代码失去了并发性。

```js
//promise
Promise.all([ajax1(), ajax2()])
复制代码
```

c. try...catch...内部的变量无法传递给下一个try...catch...,Promise和then/catch内部定义的变量，能通过then链条的参数传递到下一个then/catch，但是async/await的try内部的变量，如果用let和const定义则无法传递到下一个try...catch...，只能在外层作用域先定义好。

**但async/await确确实实是解决了promise一些问题的。更加灵活的处理异步**

**promise的一些问题：**
 a. 一旦执行，无法中途取消，链式调用多个then中间不能随便跳出来

b. 错误无法在外部被捕捉到，只能在内部进行预判处理，如果不设置回调函数，Promise内部抛出的错误，不会反应到外部

c. Promise内部如何执行，监测起来很难，当处于pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）

##### 17. 手写深拷贝

```js
// 编写一个深度克隆函数，满足以下需求（此题考察面较广，注意细节）
function deepClone(obj) {}

// deepClone 函数测试效果
const objA = {
  name: 'jack',
  birthday: new Date(),
  pattern: /jack/g,
  body: document.body,
  others: [123,'coding', new Date(), /abc/gim,]
};

const objB = deepClone(objA);
console.log(objA === objB); // 打印 false
console.log(objA, objB); // 对象内容一样
复制代码
```

优化后的代码：

```js
// 深拷贝：对对象内部进行深拷贝，支持 Array、Date、RegExp、DOM
const deepCopy = (sourceObj) => {
  // 如果不是对象则退出（可停止递归）
  if(typeof sourceObj !== 'object') return;
  
  // 深拷贝初始值：对象/数组
  let newObj = (sourceObj instanceof Array) ? [] : {};

  // 使用 for-in 循环对象属性（包括原型链上的属性）
  for (let key in sourceObj) { 
    // 只访问对象自身属性
    if (sourceObj.hasOwnProperty(key)) {
      // 当前属性还未存在于新对象中时
      if(!(key in newObj)){
        if (sourceObj[key] instanceof Date) {
          // 判断日期类型
          newObj[key] = new Date(sourceObj[key].getTime());
        } else if (sourceObj[key] instanceof RegExp) {
          // 判断正则类型
          newObj[key] = new RegExp(sourceObj[key]);
        } else if ((typeof sourceObj[key] === 'object') && sourceObj[key].nodeType === 1 ) {
          // 判断 DOM 元素节点
          let domEle = document.getElementsByTagName(sourceObj[key].nodeName)[0];
          newObj[key] = domEle.cloneNode(true);
        } else {
          // 当元素属于对象（排除 Date、RegExp、DOM）类型时递归拷贝
          newObj[key] = (typeof sourceObj[key] === 'object') ? deepCopy(sourceObj[key]) : sourceObj[key];
        }
      }
    }
  }
  
  
  return newObj;
}

// deepCopy 函数测试效果
const objA = {
  name: 'jack',
  birthday: new Date(),
  pattern: /jack/g,
  body: document.body,
  others: [123,'coding', new Date(), /abc/gim,]
};

const objB = deepCopy(objA);
console.log(objA === objB); // false
console.log(objA.others === objB.others); // false
console.log(objA, objB); // 对象内容一样
```

