# JavaScript

## hotch-potch
### 格式化
MM/DD/YYYY
```js
function formatDate(value) {
  const date = new Date(value);
  const year = date.getFullYear();
  const month = date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1;
  const d = date.getDate() < 10 ? '0' + date.getDate() : date.getDate();

  return month + '/' + d + '/' + year;
}
```
### 运算符
#### 指数运算符
```js
2 ** 2 // 4
2 ** 3 // 8
3 ** 2 ** 2 // 相当于 2 ** (3 ** 2) = 81

let a = 1.5;
a **= 2; // 等同于 a = a * a;

let b = 4;
b **= 3; // 等同于 b = b * b * b;
```
#### 逻辑赋值运算符
```js
function example(opts) {
  opts.foo ??= 'bar';
  opts.baz ??= 'qux';
}
```
上面示例中，参数对象opts如果不存在属性foo和属性baz，则为这两个属性设置默认值。

### 整数反转
```js
function revserInteger(num) {
  return parseInt((num).toString().split('').reverse().join('')) * Math.sign(num);
}
revserInteger(1234); // 4321
revserInteger(-100); // 1
```

### Limit concurrent requests to at most 3 at a time
```js
const requestPool = {
  MAX_PARALLEL_CALL: 3,
  callQueue: [], // 等待发起请求的集合
  activeCallCount: 0, // 正在请求中的数量
  addCall(call) { // 增加一个新的请求
    this.callQueue.push(call);
    this.updateCallQueue();
  },
  onDoneOrFail() {
    this.activeCallCount--;
    this.updateCallQueue();
  },
  updateCallQueue() {
    if (this.callQueue.length && this.activeCallCount <= this.MAX_PARALLEL_CALL) {
      let activeCall = this.callQueue.shift();
      this.activeCallCount++;
      fetch(activeCall.url)
        .then((response) => response.json())
        .then((data) => {
          this.onDoneOrFail();
          activeCall.callback(data);
        })
        .catch((error) => {
          this.onDoneOrFail();
          activeCall.callbback(error);
        });
    }
  }
};

function cb1(data) {
  console.log('cb1 done: ', data);
}

function cb2(data) {
  console.log('cb2 done: ', data);
}

function cb3(data) {
  console.log('cb3 done: ', data);
}

function cb4(data) {
  console.log('cb4 done: ', data);
}

requestPool.addCall({url: "https://example.com/request1", callback: cb1 });
requestPool.addCall({url: "https://example.com/request2", callback: cb2 });
requestPool.addCall({url: "https://example.com/request3", callback: cb3 });
requestPool.addCall({url: "https://example.com/request4", callback: cb4 });

```
### Check password strength
```js
function isStrongPassword(str) {
  // 长度在8～20之间
  if (str.length < 8 || str.length > 20) {
    return false;
  }
  // 至少包含一个字母
  if (!/(?=.*[A-Za-z])/.test(str)) {
    return false;
  }
  // 至少包含一个数字
  if (!/(?=.*[0-9])/.test(str)) {
    return false;
  }
  // 至少包含一个特殊字符（这里罗列出所有键盘上可见的特殊字符的英文和中文状态）
  if (!/(?=.*[`~!@#$%^&*()_\-+=<>?:"{}|,.\/;'\\[\]·~！@#￥%……&*（）——\-+={}|《》？：“”【】、；‘'，。、])/.test(str)) {
    return false;
  }
  
  return true;
}

console.log(isStrongPassword('123abc$￥'));
```
### Hoisting
Hoisting is JavaScript's default behavior of moving all variable and function declarations to the top of the current scope (to the top of the current script or the current function).
#### Variable hoisting
```js
console.log(counter); // undefined
var counter = 1;
```
The **let** keyword
```js
console.log(counter);
let counter = 1;
```
The JavaScript issues the following error:
```js
"ReferenceError: Cannot access 'counter' before initialization
```
If you access a variable that doesn’t exist:
```js
console.log(XXX);
let counter = 1;
```
Here is the error:
```js
"ReferenceError: XXX is not defined
```
#### Function hoisting
```js
let x = 20,
    y = 10;

let result = add(x,y);
console.log(result);

function add(a, b){
return a + b;
}
```
The above code is equivalent to the following:
```js
function add(a, b){
    return a + b;
}

let x = 20,
    y = 10;

let result = add(x,y);
console.log(result);
```
#### Function expressions
The following example changes the add from a regular function to a function expression:
```js
let x = 20,
    y = 10;

let result = add(x,y);
console.log(result);

var add = function(x, y) {
return x + y;
}
```
If you execute the code, the following error will occur:
```js
"TypeError: add is not a function
```
#### Arrow functions
```js
let x = 20,
    y = 10;

let result = add(x,y);
console.log(result);

var add = (x, y) => x + y;
```
The code also issues the same error as the function expression example because arrow functions are syntactic sugar for defining function expressions.
```js
"TypeError: add is not a function
```
### check if two urls belong the same domain
```js
function isSubdomain(url1, url2) {
  const protocols = [
    url1.slice(0,url1.indexOf(':')),
    url2.slice(0,url2.indexOf(':'))
  ];
  if (protocols[0] !== protocols[1]) {
    return false;
  }
  
  const domains = [
    url1.slice(url1.indexOf('//') + 2, url1.indexOf(':', 9) > -1 ? url1.indexOf(':', 9) : url1.indexOf('/', 9)),
    url2.slice(url2.indexOf('//') + 2, url2.indexOf(':', 9) > -1 ? url2.indexOf(':', 9) : url2.indexOf('/', 9))
  ]; 
  if (domains[0] !== domains[1]) {
    return false;
  }
  
  const ports = [
    url1.indexOf(':', 9) > -1 ? url1.slice(url1.indexOf(':', 9) + 1, url1.indexOf('/', 9)) :
      url1.startsWith('https') ? '443' : '80',
    url2.indexOf(':', 9) > -1 ? url2.slice(url2.indexOf(':', 9) + 1, url2.indexOf('/', 9)) :
      url2.startsWith('https') ? '443' : '80'
  ];
  if (ports[0] !== ports[1]) {
    return false;
  }
  
  return true;
}
```
### convert string to number
```js
parseInt('10.5px', 10); // 10
parseFloat('10.5px'); // 10.5
Number('10.5'); // 10.5
Number('10.5px'); // NaN
+'10.5' // 10.5
'10.5'*1 // 10.5
```

### swap two variables
```js
// solution 1
b = [a, a = b][0];

// solution 2: ES 6 destructuring assignment
[a, b] = [b, a]
```
### draw a circle on the page
```js
// img tag
  <img src="circle.png" width="100" height="100" />
  
// border-radius
#circle {
  background-color: #000;
  width: 100px;
  height: 100px;
  border-radius: 50%;
}

// clip-path
#circle {
  background-color: #000;
  width: 100px;
  height: 100px;
  clip-path: circle();
}

// html entity
<div id="circle">&#11044;</div>
#circle {
  font-size: 100px;
  line-height: 100px;
}

// pseduo element
#circle5::before {
  content: "";
  display: block;
  width: 100px;
  height: 100px;
  border-radius: 50px;
  background-color: #000;
}

// radial-gradient
#circle {
  background-image: radial-gradient(black 66.666%, transparent 33.333%);
  height: 100px;
  width: 100px;
}

// svg circle & path
<svg width="100" height="100">
  <circle cx="50" cy="50" r="50" />
</svg>
  
// canvas arc()
<canvas id="myCanvas" width="100" height="100"></canvas>
var c = document.getElementById('myCanvas');
var ctx = c.getContext('2d');
ctx.beginPath();
ctx.arc(50, 50, 50, 0, 2 * Math.PI);
ctx.fillStyle = '#000';
ctx.fill();

// pre tag
  <pre style="line-height:8px;">
     +++
    +++++
   +++++++
  +++++++++
 +++++++++++
 +++++++++++
 +++++++++++
  +++++++++
   +++++++
    +++++
     +++
  </pre>
```
## DOM
Output DOM tree
```js
function outputDOMTree(node, count) {
  document.write(`<p>` + `......`.repeat(count) + `${node.id ? node.id : node.nodeName}</p>`);
  Array.from(node.children).forEach(child => {
    outputDOMTree(child, count + 1);
  });
}

outputDOMTree(document.body, 0);
```
## Event Loop
## Closure
### “闭包”是什么？举个例子
闭包是在另一个函数（称为父函数）中定义的函数，并且可以访问在父函数作用域中声明和定义的变量。闭包可以访问三个作用域中的变量：1)在自己作用域中声明的变量；2)在父函数中声明的变量；3)在全局作用域中声明的变量。
```js
var globalVar = "abc";
// 自调用函数
(function outerFunction (outerArg) { // outerFunction 作用域开始
  // 在 outerFunction 函数作用域中声明的变量
  var outerFuncVar = 'x';    
  // 闭包自调用函数
  (function innerFunction (innerArg) { // innerFunction 作用域开始
    // 在 innerFunction 函数作用域中声明的变量
    var innerFuncVar = "y";
    console.log(         
      "outerArg = " + outerArg + "\n" +
      "outerFuncVar = " + outerFuncVar + "\n" +
      "innerArg = " + innerArg + "\n" +
      "innerFuncVar = " + innerFuncVar + "\n" +
      "globalVar = " + globalVar);
  // innerFunction 作用域结束
  })(5); // 将 5 作为参数
// outerFunction 作用域结束
})(7); // 将 7 作为参数
```
innerFunction 是在 outerFunction 中定义的闭包，可以访问在 outerFunction 作用域内声明和定义的所有变量。除此之外，闭包还可以访问在全局命名空间中声明的变量。上述代码的输出将是：
```js
outerArg = 7
outerFuncVar = x
innerArg = 5
innerFuncVar = y
globalVar = abc
```
### 编写一个可以执行如下操作的函数
```js
var addSix = createBase(6);
addSix(10); // 返回 16
addSix(21); // 返回 27
```
可以创建一个闭包来存放传递给函数 createBase 的值。被返回的内部函数是在外部函数中创建的，内部函数就成了一个闭包，它可以访问外部函数中的变量，在本例中是变量 baseNumber。
```js
function createBase(baseNumber) {
  return function(N) {
    // 我们在这里访问 baseNumber，即使它是在这个函数之外声明的。
    // JavaScript 中的闭包允许我们这么做。
    return baseNumber + N;
  }
}
```
When the like function finishes running, normally we would expect all of its variables to be garbage collected (removed from memory, which is an automatic process that the JS compiler does). We'd expect each likeCount to go away when the function is done, but they don't.Since the inner function instances are still alive (assigned to like), the closure is still preserving the likeCount variables.
```js
// To use a closure, define a function inside another function and expose it.
// To expose a function, return it or pass it to another function.
function handleLike(step) {
  let likeCount = 0;
  return function addLike() {
    likeCount += step;
    return likeCount;
  };
}

const like = handleLike(1); // create an instance of the inner function addLike
const doubleLike = handleLike(2);

like(); // 1
like(); // 2

doubleLike(); // 2 (likeCount is still being preserved!)
doubleLike(); // 4
```
## OOP
### Define Classes
```js
// ES5 version based on Point() constructor function
function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.getPosition = function () {
  return '(' + this.x + ', ' + this.y + ')';
};

var p1 = new Point(1, 2);

// ES6 version based on class keyword
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  getPosition() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
var p2 = new Point(3, 4);
```
### Inheritance
```js
// ES5 version
function Shape() {
  this.x = 0;
  this.y = 0;
}
Shape.prototype.move = function(x, y) {
  this.x += x;
  this.y += y;
  console.log('Shape moved.');
};

function Rectangle() {
  Shape.call(this); // call super constructor.
}
// subclass extends superclass
Rectangle.prototype = Object.create(Shape.prototype);
Rectangle.prototype.constructor = Rectangle;

var rect = new Rectangle();
rect.move(1, 1); // Outputs, 'Shape moved.'

//  version
class Animal { 
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(this.name + ' makes a noise.');
  }
}

class Dog extends Animal {
  constructor(name) {
    super(name); // call the super class constructor and pass in the name parameter
  }
  speak() {
    console.log(this.name + ' barks.');
  }
}

var d = new Dog('Mitzie');
d.speak(); // Mitzie barks.
```
### Multiple Inheritance
```js
// ES5 version
function MyClass() {
  SuperClass.call(this);
  OtherSuperClass.call(this);
}

MyClass.prototype = Object.create(SuperClass.prototype);
Object.assign(MyClass.prototype, OtherSuperClass.prototype);
MyClass.prototype.constructor = MyClass;

MyClass.prototype.myMethod = function() {
  // do a thing
};

//  version
class A {
  constructor(){
    console.log('A');
  }
}

const B = SuperClass => class extends SuperClass { 
  constructor(){
    super();
    console.log('B');
  }
}

const C = SuperClass => class extends SuperClass { 
 constructor(){
   super();
   console.log('C');
 }
}

class D extends C(B(A)){
  constructor(){
    super();
    console.log('D');
  }
}

const d = new D();

// output: ABCD
```
### Clone Objects
#### Shallow Copy
- `let copy = Object.assign({}, obj);`
- `let copy = { ...obj };`

Tips: this method can be used to copy methods
#### Deep Copy without methods
`let copy = JSON.parse(JSON.stringify(obj));`

This works for all kind of objects containing objects, arrays, strings, booleans and numbers.
Unfortunately, this method can't be used to copy user-defined object methods.
#### Deep Copy with Methods
```js
function clone(obj) {
  const temp = {};
  // ES5 equivalent: for (var key in o) {
   Object.keys(obj).forEach(key => {
    if (typeof obj[key] === 'object') {
      temp[key] = clone(obj[key]);
    } else {
      temp[key] = obj[key];
    }
   }
  );
  return temp;
}
```
## Async Programming
### Promise.all(), Promise.race(), Promise.allSettled(), Promise.any()
```js
const p = Promise.all([p1, p2, p3]);
```
只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。

```js
const p = Promise.race([p1, p2, p3]);
```
只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给p的回调函数。

```js
const promises = [
  fetch('/api-1'),
  fetch('/api-2'),
  fetch('/api-3'),
];

await Promise.allSettled(promises);
removeLoadingIndicator();
```
上面示例中，数组promises包含了三个请求，只有等到这三个请求都结束了（不管请求成功还是失败），removeLoadingIndicator()才会执行。

```js
Promise.any([
  fetch('https://v8.dev/').then(() => 'home'),
  fetch('https://v8.dev/blog').then(() => 'blog'),
  fetch('https://v8.dev/docs').then(() => 'docs')
]).then((first) => {  // 只要有一个 fetch() 请求成功
  console.log(first);
}).catch((error) => { // 所有三个 fetch() 全部请求失败
  console.log(error);
});
```
只要参数实例有一个变成fulfilled状态，包装实例就会变成fulfilled状态；如果所有参数实例都变成rejected状态，包装实例就会变成rejected状态。

Promise.any()跟Promise.race()方法很像，只有一点不同，就是Promise.any()不会因为某个 Promise 变成rejected状态而结束，必须等到所有参数 Promise 变成rejected状态才会结束。

### Callback Hell
```js
function runTasks() {
  setTimeout(() => {
    document.write('<p>Task 1</p>');
    setTimeout(() => {
      document.write('<p>Task 2</p>');
      setTimeout(() => {
        document.write('<p>Task 3</p>');
      }, 1000);
    }, 1000);
  }, 1000);
}

runTasks();

// solution 1:
function task1 () {
  setTimeout(() => {
    document.write('<p>Task 1</p>');
    task2();
   }, 1000);
}

function task2 () {
   setTimeout(() => {
    document.write('<p>Task 2</p>');
    task3();
   }, 1000);
}

function task3 () {
   setTimeout(() => {
    document.write('<p>Task 3</p>');
   }, 1000);
}

task1();

// solution 2: Chained Promises
function task1() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      document.write('<p>Task 1</p>');
      resolve();
    }, 1000);
  });
};

function task2() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      document.write('<p>Task 2</p>');
      resolve();
    }, 1000);
  });
};

function task3() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      document.write('<p>Task 3</p>');
      resolve();
    }, 1000);
  });
};

task1()
  .then(() => {
    return task2();
  })
  .then(() => {
    task3();
  });
  
// solution 3: async & await
function task1() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      document.write('<p>Task 1</p>');
      resolve();
    }, 1000);
  });
}

function task2() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      document.write('<p>Task 2</p>');
      resolve();
    }, 1000);
  });
}

function task3() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      document.write('<p>Task 3</p>');
      resolve();
    }, 1000);
  });
}

async function runTasks() {
  await task1();
  await task2();
  await task3();
}

runTasks();
```
## this
### call() vs apply() vs bind()
- call() invokes the function and allows you to pass in arguments one by one.
- apply() invokes the function and allows you to pass in arguments as an array.
- bind() returns a new function, allowing you to pass in a this array and any number of arguments.

**call()**
```js
var person1 = { name: '111' };
var person2 = { name: '222' };
function say(word) {
  document.write(`<p>${word} ${this.name}</p>`);
}
say.call(person1, 'Hello');
say.call(person2, 'Hello');
```
**apply()**
```js
var person3 = { name: '333' };
var person4 = { name: '444' };
function say(word) {
  document.write(`<p>${word} ${this.name}</p>`);
}
say.apply(person3, ['Hello']);
say.apply(person4, ['Hello']);
```
**bind()**
```js
var person5 = { name: '555' };
var person6 = { name: '666' };
function say(word) {
  document.write(`<p>${word} ${this.name}</p>`);
}
var say1 = say.bind(person5);
var say2 = say.bind(person6);
say1('Hello');
say2('Hello');
```
## Array
### 按照制定长度分割数组
给定数组和大小，将数组项拆分为具有给定大小的数组列表。
```js
function divideArray (arr, size) {
  const result = [];
  let index = 0;
   while(index < arr.length) {
     result.push(arr.slice(index, index + size));
     index += size;
   }
   return result;
}

console.log(divideArray([1,2,3,4,5],2)); // [[1,2],[3,4],[5]]
```
#### 找出最长的不重复的子串
```js
function longestSubstrs(str) {
  let queue = []; // 在队列中攒可能的最长字串
  let result = []; // 保存最长字串的集合

  for (let i = 0; i < str.length; i++) {
    let index = queue.indexOf(str[i]);
    if (index > -1) { // 重复位置往前的字符串一律出队列
      queue.splice(0, index + 1);
    }
    // 新字符入队列
    queue.push(str[i]);
    // 起始的最长子串
    if (!result.length) {
      result.push(queue.join(''));
    }
    // 新的最长子串
    if (result.length && queue.length > result[0].length) {
      result = [queue.join('')];
    }
    // 又一个长度相同的字串
    if (result.length && queue.length === result[0].length && !result.includes(queue.join(''))) {
      result.push(queue.join(''));
    }
  }
  return result;
};

console.log(longestSubstrs('abcabcbb')); // ['abc', 'bca', 'cab']
```
### reduce() method
- 求数组项之和
```js
let arr1 = [1, 2, 3, 4, 5];
let sum = arr1.reduce((total, num) => {
   return total + num;
});
console.log(sum); // 15
```
- 求数组项最大值
```js
let arr = [2, 3, 1, 4, 5];
let max = arr.reduce((prev, cur) => {
  return Math.max(prev, cur);
});
console.log(max); // 5
```

- 数组去重
```js
let arr = [1, 2, 2, 3, 3, 4, 5];
let uniqueArr = arr.reduce((prev, cur) => {
  return prev.inclues(cur) ? prev : prev.concat(cur);
}, []);
console.log(uniqueArr); // 1,2,3,4,5
```

- 将二维数组转化为一维
```js
let arr = [[1, 2], [3, 4], [5, 6]];
let flatArr = arr.reduce((pre, cur) => {
  return pre.concat(cur);
}, []);
console.log(flatArr); // 1,2,3,4,5,6
```

- 求对象数组中某属性的和
```js
const result = [
  {
   subject: '语文',
   score: 90
  },
  {
   subject: '数学',
   score: 80
  },
  {
    subject: '英语',
    score: 70
   }
 ];
 const total = result.reduce((prev, cur) => {
   return  prev + cur.score; 
 }, 0);
console.log(total); // 240
```

### Clone Array
#### Shallow Copy
- `var copy = arr.slice();`
- `let copy = [...arr];`
#### Deep Copy
`let copy = JSON.parse(JSON.stringify(arr));`
### Empty an array
- solution 1
```js
A = [];
```
This code will set the variable A to a new empty array. This is perfect if you don't have references to the original array A anywhere else because this actually creates a brand new (empty) array. You should be careful with this method because if you have referenced this array from another variable or property, the original array will remain unchanged. Only use this if you only reference the array by its original variable A.

This is also the fastest solution.

This code sample shows the issue you can encounter when using this method:
```js
var arr1 = ['a','b','c','d','e','f'];
var arr2 = arr1;  // Reference arr1 by another variable 
arr1 = [];
console.log(arr2); // Output ['a','b','c','d','e','f']
```
- solution 2
```js
A.length = 0
```
This will clear the existing array by setting its length to 0. Some have argued that this may not work in all implementations of JavaScript, but it turns out that this is not the case. It also works when using "strict mode" in ECMAScript 5 because the length property of an array is a read/write property.

- solution 3
```js
A.splice(0,A.length)
```
Using .splice() will work perfectly, but since the .splice() function will return an array with all the removed items, it will actually return a copy of the original array. Benchmarks suggest that this has no effect on performance whatsoever.

- solution 4
```js
while(A.length > 0) {
    A.pop();
}
while(A.length > 0) {
    A.shift();
}
```
Performance

Of all the methods of clearing an existing array, methods 2 and 3 are very similar in performance and are a lot faster than method 4. See this [benchmark](http://jsben.ch/hyj65).
### Remove Duplicate Values
```js
// for loop
var uniqueArr = [];
for (var i = 0;i < srcArr.length; i++) {
  if(uniqueArr.indexOf(srcArr[i]) === -1) {
    uniqueArr.push(srcArr[i]);
  }
}

// Array.prototype.filter
var uniqueArr = srcArr.filter(function(elem, pos, arr) {
  return arr.indexOf(elem) === pos;
});

// Set
let arr1 = Array.from(new Set(arr));
let arr2 = [...new Set(arr)];
```

### Get max and min value
```js
Math.max.apply(null,arr);
Math.min.apply(null,arr);
```
### create an array containing 1…N
```js
Array.from(Array(N), (v, i) => i + 1);
```

## String
### 给定一个短语，每个首字母变为大写
```js
str.split(' ').map(word => word[0].toUpperCase() + word.slice(1)).join(' ');
```
### 给定一个短语，按照顺序反转每一个单词
```js
str.split(' ').map(word => word.split('').reverse().join('')).join(' ')
```
### 字符串重复n次
```js
// Array.prototype.join()
Array(n+1).join(str);

//  repeat()
str.repeat(n)
```
### 字符与ASCII码互转
```js
'abc'.charCodeAt(0); // 97
String.fromCharCode(97) // 'a'
```

### Count the occurrence of a substring in a string
```js
// split()
str.split(subStr).length - 1

// match()
(str.match(new RegExp(subStr, 'g')) || []).length
```
### 找出重复次数最多的字符
```js
function getMaxOccurrenceChar(str) {
  let maxChar = [''];
  let maxCount = 0;
  const unique = Array.from(new Set(str.split('')));
  for (let i=0;i < unique.length;i++) {
    let count = str.split(unique[i]).length - 1;
    if (count > maxCount) {
      maxChar = [unique[i]];
      maxCount = count;
    } else if (count === maxCount) {
      maxChar.push(unique[i]);
    }
  }
  return maxChar;
}

getMaxOccurrenceChar('abc awb bai '); // ['a','b', '']
```
### Get the position of nth occurrence of a substring in a string
```js
// split() & join()
str.split(subStr, n).join(subStr).length;

// indexof()
function getSubstrNth(str, subStr, n) {
  var index = -1;

  if (n === 1) {
    index = str.indexOf(subStr);
  } else if (n > 1) {
    var times = 1;
    index = str.indexOf(subStr);
    while (times < n && index !== -1) {
      index = str.indexOf(subStr, index + subStr.length);
      times++;
    }
  }

  return index;
}
```
### Reverse a string
```js
// decrementing for-loop with concatenation
var newStr = '';
for (var i = str.length - 1; i >= 0; i--) {
  newStr += str[i];
}

// In-built functions
str.split('').reverse().join('');

// recursion with substr() and charAt()
function reverseStr(str) {
  return (str === '') ? '' : reverse(str.substr(1)) + str.charAt(0);
}

// reduce()
str.split('').reduce((prev, cur) => cur + prev);
```
## Truthy & Falsy
The following values are always falsy:

- false
- 0 (zero)
- '' or "" (empty string)
- null
- undefined
- NaN (e.g. the result of 1/0)

Everything else is truthy. That includes:
- -1
- '0' (a string containing a single zero)
- 'false' (a string containing the text “false”)
- [] (an empty array)
- {} (an empty object)
- function(){} (an “empty” function)

## Random
### Get rondom string of given length
```js
function randomString(n) {
  let str = "abcdefghijklmnopqrstuvwxyz9876543210";
  let tmp = "",
    i = 0,
    len = str.length;
  for (i = 0; i < n; i++) {
    tmp += str.charAt(Math.floor(Math.random() * len));
  }
  return tmp;
}
```
### Get rondom number under given scope
```js
function getRadomNum(min,max){
  return Math.floor(Math.random() * (max - min + 1)) + min;
}
```
### Get rondom number from an array
```js
function getRadomFromArr(arr){
  return arr[Math.floor(Math.random()*arr.length)];
}
```
### Disturb sequence of an array
```js
var arr = [1,2,3,4,5,6,7,'a','dsfs',8,9,'v'];
arr.sort(function(){return Math.random() - 0.5});
```
## Data Types Checking
### String
```js
1 typeof value === 'string' || value instanceof String
2 typeof value === 'string' || value.constructor === String
3 /String/.test(Object.prototype.toString.call(value))
```
### Number
```js
1 typeof value === 'number' && isFinite(value)
2 value.constructor === Number && isFinite(value)
3 /Number/.test(Object.prototype.toString.call(value)) && isFinite(value)
```
### Boolean
```js
1 typeof value === 'boolean'
2 value.constructor === Boolean
3 /Boolean/.test(Object.prototype.toString.call(value))
```
### Date
```js
1 value instanceof Date
2 value.constructor === Date
```
### Array
```js
1 value instanceof Array
2 value.constructor === Array
3 value.isArray()
4 /Array/.test(Object.prototype.toString.call(value))
```
### Function
```js
1 typeof value === 'function'
2 value instanceof Function
3 value.constructor === Function
4 /Function/.test(Object.prototype.toString.call(value))
```
### Object
```js
1 value.constructor === Object
2 /Object/.test(Object.prototype.toString.call(value))
```
### RegExp
```js
1 value instanceof RegExp
2 value.constructor === RegExp
3 /RegExp/.test(Object.prototype.toString.call(value))
```
### Error
```js
1 value instanceof Error
2 value.constructor === Error
3 /Error/.test(Object.prototype.toString.call(value))
```
### Undefined
```js
1 value === undefined
2 typeof value === 'undefined'
3 /Undefined/.test(Object.prototype.toString.call(value))
```
### Null
```js
1 value === null
2 /Null/.test(Object.prototype.toString.call(value))
```
### Symbol
```js
1 typeof value === 'symbol'
2 value.constructor === Symbol
3 /Symbol/.test(Object.prototype.toString.call(value))
```
# Component
## Web Components
```js
class MyElement extends HTMLElement {
  constructor() {
    super();
    // element created
  }

  connectedCallback() {
    // browser calls this method when the element is added to the document
    // (can be called many times if an element is repeatedly added/removed)
  }

  disconnectedCallback() {
    // browser calls this method when the element is removed from the document
    // (can be called many times if an element is repeatedly added/removed)
  }

  static get observedAttributes() {
    return [/* array of attribute names to monitor for changes */];
  }

  attributeChangedCallback(name, oldValue, newValue) {
    // called when one of attributes listed above is modified
  }
}
```
# React
### What is Virtual DOM
As we all know, the browser DOM (Document Object Model) is a tree-like structure of different HTML elements. Virtual DOM is an in-memory representation of the real DOM. It is an abstraction created by React which is similar to the real DOM.
### Why does React use the virtual DOM
Updating and re-rendering the real DOM is slow and inefficient, especially if it gets updated frequently. So, instead of updating the real DOM directly, React updates the virtual DOM.
### How does React use the virtual DOM
The virtual DOM is then compared to the real DOM and once it identifies the differences, it only updates that part of the DOM, rather than rendering the entire DOM again. This process is known as diffing and reconciliation.
### What is JSX
JSX (JavaScript XML) is a syntax extension for JavaScript that allows you to write HTML-like code in the same file as the JavaScript code. This makes it very easy for your HTML to work with JavaScript.
### What is State
State is a React object that contains information about the component and determines how the component behaves. State can change any time based on user behavior. Any change in state causes the entire component to re-render.
### What are props
Props (short for properties) are a way to pass data from one component to another. They can be considered as arguments passed to components. Props are passed to a child component similar to HTML attributes.
### Controlled and Uncontrolled components
In controlled components, the values of form fields are set and updated ("controlled" by React state). 
```js
function ControlledComponent() {
  const [value, setValue] = useState('');

  const handleSubmit = (event) => {
    event.preventDefault();
    alert(`Value: ${value}`);
  };

  return (
    <form onSubmit={handleSubmit}>
        <input type="text" value={value} onChange={(e) => setValue(e.target.value)} />
        <button type="submit">Submit</button>
    </form>
  );
}
```
In uncontrolled components, the values of form fields are managed with refs.
```js
function UncontrolledComponent() {
  const inputRef = useRef(null);

  const handleSubmit = (event) => {
    event.preventDefault();
    alert(`Input Value: ${inputRef.current.value}`);
  };

  return (
    <form onSubmit={handleSubmit}>
        <input type="text" ref={inputRef} />
        <button type="submit">Submit</button>
    </form>
  );
}
```
### Custom Hooks
Custom hooks can share re-usable logic between multiple components.
You can export the fetching logic as a Hook to avoid duplicating code.
```js
function useFetch(url) {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch(url);
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }
        const result = await response.json();
        setData(result);
      } catch (error) {
        setError(error);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, error, loading };
}

export default useFetch;
```
Let's use this in a component:
```js
const UserList = () => {
  const { data, loading, error } = useFetch('https://jsonplaceholder.typicode.com/users');

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return (
    <ul>
      {data.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
};
```
### Higher Order Components
A higher order component (HOC) is a function that takes a component as an argument and returns a new component that wraps the original one. HOCs allow you to provide additional functionality to a component as well as re-use it across multiple components.
### Why not Use Index as Keys while Rendering Lists
Elements often get added or deleted in an array. The order of elements could get changed too. In these cases, value of key prop changes and may lead to unpredictable behavior.
### Difference Between Client-side and Server-side Rendering
Server Side Rendering (SSR): Web Page is generated and rendered on the server before sending to the client. Client receives complete web page from the server and displays it directly to the user.

Client Side Rendering (CSR): A basic HTML file is sent to the client, and then it renders dynamic content using JavaScript.
### How to handle the common lifecycle methods in React Functional Components
```js
const Component = () => {
  useEffect(() => {
    // componentDidMount
    console.log("Component mounted");

    return () => {
      // componentWillUnmount
      console.log("Component unmounted");
    };
  }, []); // empty dependency array implies this effect runs only once when the component mounts

  useEffect(
    () => {
      // componentDidUpdate
      console.log("Component updated");
    },
    [
      /* dependencies - changes to these values should trigger the function to re-run */
      /* NOTE: This function will run during mount too */
    ]
  );

  return <React.Fragment />;
};
```
### What is React Fiber
React Fiber is a concept of ReactJS that is used to render a system faster and smoother. It is an internal engine change aimed at making React faster & somewhat "smarter". The Fiber reconciler, which became the default reconciler for React 16 and above, is a complete rewrite of React’s reconciliation algorithm to solve some long-standing issues in React.

Because Fiber is asynchronous, React can:

- Pause, resume, and restart rendering work on components as new updates come in
- Reuse previously completed work and even abort it if not needed
- Split work into chunks and prioritize tasks based on importance
  
This change allows React to break away from the limits of the previous synchronous Stack Reconciler, where tasks couldn’t be interrupted. This change also allows React to fine-tune rendering components, ensuring that the most important updates happen as soon as possible.

In React, reconciliation is the core mechanism responsible for efficiently updating the UI in response to changes in a component's state or props. It determines the minimal set of operations needed to transform the actual DOM to match the desired state represented by the virtual DOM.
### SSG vs ISR vs SSR vs CSR - When to use which
1. Static Site Generation (SSG)
   
  SSG pre-renders all the pages of your website and serves them as per the client's requests. you should use it only when you have a website where the content rarely changes, like a product showcase website.
  
2. Server-side rendering (SSR)

  Server-side sends a fully rendered page to the client; the client's JavaScript bundle takes over the pages and allows the SPA framework to operate them. You should use it when you have a website with a lot of dynamic content that changes very frequently and heavily relies on SEO, like a stock ticker website.
  
3. Incremental Static Regeneration (ISR)

   ISR enables you to use static-generation on a per-page basis, without needing to rebuild the entire site. You should use it when you have a site where the content is dynamic but does NOT change frequently, like a blog site.
   
4. Client-Side Rendering (CSR)

   In CSR, the server-side sends a blank HTML page and a JavaScript bundle that handles all logic to the client-side. It is ideal for any site which CSR is ideal for any site which does not rely heavily on SEO.

# ES6
## Event Loop
Example 1: Basic Synchronous and Asynchronous Code
```js
console.log('A'); // Synchronous

setTimeout(() => {
  console.log('B'); // Macrotask
}, 0);

console.log('C'); // Synchronous
```
Output: A, C, B

Example 2: Microtasks with Promises
```js
console.log('A');

setTimeout(() => {
  console.log('B'); // Macrotask
}, 0);

Promise.resolve().then(() => {
  console.log('C'); // Microtask
});

console.log('D');
```
Output: A, D, C, B

Example 3: Nested Microtasks
```js
Promise.resolve().then(() => {
  console.log('A');
  Promise.resolve().then(() => console.log('B'));
});

console.log('C');
```
Output: C, A, B

Example 4: setTimeout vs setImmediate (Node.js only)
```js
setTimeout(() => console.log('A'), 0); // Macrotask
setImmediate(() => console.log('B')); // Macrotask in Node.js
```
Output (depends on Node.js version): Generally A, then B.

Example 5: Event Listener and Promises
```js
document.body.addEventListener('click', () => {
  console.log('Click Event'); // Macrotask
});

Promise.resolve().then(() => console.log('Promise Resolved')); // Microtask
console.log('End');
```
Output: End, Promise Resolved, Click Event

Example 6: Interleaving Promises and setTimeout
```js
setTimeout(() => console.log('A'), 0);
Promise.resolve().then(() => console.log('B'));
setTimeout(() => console.log('C'), 0);
Promise.resolve().then(() => console.log('D'));
```
Output: B, D, A, C

Example 7: Nested Promises with setTimeout
```js
console.log('A');

setTimeout(() => {
  console.log('B');
  Promise.resolve().then(() => {
    console.log('C');
  });
}, 0);

Promise.resolve().then(() => {
  console.log('D');
  setTimeout(() => {
    console.log('E');
  }, 0);
});

console.log('F');
```
Output:

1. A (Synchronous)
2. F (Synchronous)
3. D (Microtask from Promise)
4. B (Macrotask from setTimeout)
5. C (Microtask created within the setTimeout)
6. E (Macrotask from the inner setTimeout)

Example 8: Promise Chaining with setTimeout
```js
console.log('1');

setTimeout(() => {
  console.log('2');
  Promise.resolve().then(() => {
    console.log('3');
  }).then(() => {
    console.log('4');
  });
}, 0);

Promise.resolve().then(() => {
  console.log('5');
}).then(() => {
  console.log('6');
});

console.log('7');
```
Output:

1. 1 (Synchronous)
2. 7 (Synchronous)
3. 5 (Microtask from first Promise)
4. 6 (Chained Microtask)
5. 2 (Macrotask from setTimeout)
6. 3 (Microtask from Promise inside setTimeout)
7. 4 (Chained Microtask)

Example 9: Mixing Promise Resolution with Delays
```js
console.log('Start');

setTimeout(() => {
  console.log('Timeout 1');
}, 0);

Promise.resolve().then(() => {
  console.log('Promise 1');
  setTimeout(() => {
    console.log('Timeout 2');
  }, 0);
  return Promise.resolve();
}).then(() => {
  console.log('Promise 2');
});

console.log('End');
```
Output:

1. Start (Synchronous)
2. End (Synchronous)
3. Promise 1 (Microtask)
4. Promise 2 (Chained Microtask)
5. Timeout 1 (Macrotask from first setTimeout)
6. Timeout 2 (Macrotask from setTimeout inside Promise)

Example 10: Deeply Nested Promises in a Timer
```js
setTimeout(() => {
  console.log('Timer 1');
  Promise.resolve().then(() => {
    console.log('Microtask 1');
    Promise.resolve().then(() => {
      console.log('Microtask 2');
    });
  });
}, 0);

Promise.resolve().then(() => {
  console.log('Microtask 3');
});

console.log('Main Task');
```
Output:

1. Main Task (Synchronous)
2. Microtask 3 (Microtask from Promise)
3. Timer 1 (Macrotask from setTimeout)
4. Microtask 1 (Microtask within setTimeout)
5. Microtask 2 (Chained Microtask from Microtask 1)

Example 11: Combining Chained Promises and Timer Nesting
```js
console.log('Start');

setTimeout(() => {
  console.log('Timeout 1');
  Promise.resolve().then(() => {
    console.log('Promise 1');
  }).then(() => {
    console.log('Promise 2');
  });
}, 0);

Promise.resolve().then(() => {
  console.log('Promise 3');
  setTimeout(() => {
    console.log('Timeout 2');
  }, 0);
  return Promise.resolve();
}).then(() => {
  console.log('Promise 4');
});

console.log('End');
```
Output:

1. Start (Synchronous)
2. End (Synchronous)
3. Promise 3 (Microtask from first Promise)
4. Promise 4 (Chained Microtask)
5. Timeout 1 (Macrotask from first setTimeout)
6. Promise 1 (Microtask within Timeout 1)
7. Promise 2 (Chained Microtask within Timeout 1)
8. Timeout 2 (Macrotask from setTimeout inside Promise)

## Promise
### Promise 新建后就会立即执行
```js
let promise = new Promise(function(resolve, reject) {
  console.log('Promise');
  resolve();
});

promise.then(function() {
  console.log('resolved');
});

console.log('Hi!');

// Promise
// Hi!
// resolved
```
上面代码中，Promise 新建后立即执行，所以首先输出的是Promise。然后，then方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行，所以resolved最后输出。
### 调用resolve或reject并不会终结 Promise 的参数函数的执行
```js
new Promise((resolve, reject) => {
  resolve(1);
  console.log(2);
}).then(r => {
  console.log(r);
});
// 2
// 1
```
立即 resolved 的 Promise 是在本轮事件循环的末尾执行，总是晚于本轮循环的同步任务。
### Promise中的错误总是会被下一个catch语句捕获
```js
getJSON('/post/1.json').then(function(post) {
  return getJSON(post.commentURL);
}).then(function(comments) {
  // some code
}).catch(function(error) {
  // 处理前面三个Promise产生的错误
});
```
### Promise 会吃掉错误
Promise 内部的错误不会影响到 Promise 外部的代码(不会退出进程、终止脚本执行)
```js
const someAsyncThing = function() {
  return new Promise(function(resolve, reject) {
    // 下面一行会报错，因为x没有声明
    resolve(x + 2);
  });
};

someAsyncThing().then(function() {
  console.log('everything is great');
});

setTimeout(() => { console.log(123) }, 2000);
// Uncaught (in promise) ReferenceError: x is not defined
// 123
```
### 如果Promise 指定在下一轮“事件循环”再抛出错误
```js
const promise = new Promise(function (resolve, reject) {
  resolve('ok');
  setTimeout(function () { throw new Error('test') }, 0)
});
promise.then(function (value) { console.log(value) });
// ok
// Uncaught Error: test
```
上面代码中，Promise 指定在下一轮“事件循环”再抛出错误。到了那个时候，Promise 的运行已经结束了，所以这个错误是在 Promise 函数体外抛出的，会冒泡到最外层，成了未捕获的错误。
### Promise.allSettled()
```js
const resolved = Promise.resolve(42);
const rejected = Promise.reject(-1);

const allSettledPromise = Promise.allSettled([resolved, rejected]);

allSettledPromise.then(function (results) {
  console.log(results);
});
// [
//    { status: 'fulfilled', value: 42 },
//    { status: 'rejected', reason: -1 }
// ]
```
上面代码中，Promise.allSettled()的返回值allSettledPromise，状态只可能变成fulfilled。它的回调函数接收到的参数是数组results。该数组的每个成员都是一个对象，对应传入Promise.allSettled()的数组里面的两个 Promise 对象。
### Promise.resolve()
Promise.resolve()方法允许调用时不带参数，直接返回一个resolved状态的 Promise 对象。
```js
setTimeout(function () {
  console.log('three');
}, 0);

Promise.resolve().then(function () {
  console.log('two');
});

console.log('one');

// one
// two
// three
```
上面代码中，setTimeout(fn, 0)在下一轮“事件循环”开始时执行，Promise.resolve()在本轮“事件循环”结束时执行，console.log('one')则是立即执行，因此最先输出。
### Promise.try()
实际开发中，经常遇到一种情况：不知道或者不想区分，函数f是同步函数还是异步操作，但是想用 Promise 来处理它。

那么有没有一种方法，让同步函数同步执行，异步函数异步执行，并且让它们具有统一的 API 呢？

```js
const f = () => console.log('now');
Promise.try(f);
console.log('next');
// now
// next
```
```js
Promise.try(() => database.users.get({id: userId}))
  .then(...)
  .catch(...)
```
事实上，Promise.try就是模拟try代码块，就像promise.catch模拟的是catch代码块。
## Generator
## Decorator
### Explain the difference between `var`, `let`, and `const`
- Thevarkeyword is function-scoped and can be redeclared, which can lead to unexpected behavior. 
- In contrast,letandconstare block-scoped;letallows reassignment whileconstdoes not.

### what are arrow functions? How are they different from regular functions ?
Arrow functions provide a concise syntax for writing functions
```js
const sum = (a, b) => a + b;
```
Differences:

- They have an implicit `return` if the function body is a single expression(without `{}`).
- They do not have their own `this`, so the `this` keyword refers to the surrounding context.
- They cannot be used as constructors (cannot be used with `new`).
- They do not have `arguments` objects (use rest parameters instead)

### What is a template literal
A template literal is a way to include variables and expressions in strings
```js
const greeting = `Hello, ${name}!`;
```
### What are default parameters
```js
function greet(name = 'Guest') {
return `Hello, ${name}!`; 
}
```
### what is destructuring assignment
```js
const [a, b] = [1, 2]; // a = 1, b = 2
const {name, age} = {name: 'John', age: 30}; // name = 'John', age = 30
```
### What are Rest Parameters
Rest parameters allow you to represent an indefinite number of arguments as an array.
```js
function sum(...numbers) {
return numbers.reduce((prev, curr) => prev + curr, 0);
}
console.log(sum(1, 2, 3, 4)); // 10
```
### What is `super` keyword
Thesuperkeyword in ES6 classes is used to call the constructor of the parent class
```js
class Child extends Parent {
constructor() {
super();
}
}
```
### What are modules
ES6 modules allow for splitting code into reusable pieces that can be imported and exported.
```js
// module.js
export const PI = 3.14;
export function calculateArea(radius) {
return PI * radius * radius;
}

// main.js
import { PI, calculateArea } from './module.js';
console.log(calculateArea(5)); // 78.5
```
### What are Promises
Promises are used for handling asynchronous operations. 

A Promise can be in one of three states:
1. Pending: Initial state, neither fulfilled nor rejected.
2. Fulfilled: The operation completed successfully.
3. Rejected: The operation failed.
```js
const myPromise = new Promise((resolve, reject) => {
setTimeout(() => resolve('Success!'), 1000);
});
myPromise.then(result => console.log(result)); // Success! after 1 second
```
### What are `Set` and `Map` in ES6
Set is a collection of unique values and Map is a collection of key-value pairs
```js
const mySet = new Set([1, 2, 3]);
const myMap = new Map([['key1', 'value1'], ['key2', 'value2']]);
```
### 使用for...of 遍历对象
```js
const obj = {
name: '豆包'，
age: 18,
job； ‘助手’
}
```
- 遍历对象的属性名
```js
for (const key of Object.keys(obj)) {
console.log(key);
}
```
- 遍历对象的属性值
```js
for (const value of Object.values(obj)) {
console.log(value);
}
```
- 遍历对象的属性名和值
```js
for (const [key, value] of Object.entries(obj)) {
console.log(`${key}: ${value}`);
}
```
### What is the spread operator
The spread operator is used to expand an array or object into individualelements or properties.
```js
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5]; // [1, 2, 3, 4, 5]

const obj1 = { a: 1, b: 2 };
const obj2 = { ...obj1, c: 3 }; // { a: 1, b: 2, c: 3 }
```
# TypeScript
### Utility types
The utility types are extremely useful when you need to generate a new type that is similar to an already existing type with minor alterations.

- Pick: Pick the necessary properties from anobject type
- Omit: Pick all the properties from anobject type, omitting the selected keys
- Partial: Make all properties of anobject typeoptional
- Required: Make all properties of anobject typerequired
```js
interface User {
  name: string;
  age?: number;
  email: string;
}

type PartialUser = Partial<User>;
type PickUser = Pick<User, "name" | "age">;
type OmitUser = Omit<User, "age">;
type RequiredUser = Required<User>;

// PartialUser is equivalent to:
// interface PartialUser {
//   name?: string;
//   age?: number;
//   email?: string;
// }

// PickUser is equivalent to:
// interface PickUser {
//   name: string;
//   age?: number;
// }

// OmitUser is equivalent to:
// interface OmitUser {
//   name: string;
//   email: string;
// }

// RequiredUser is equivalent to:
// interface RequiredUser {
//   name: string;
//   age: number;
//   email: string;
// }
```
### Explain how the arrays work in TypeScript
Here is the syntax to declare and initialize an array in TypeScript.
```js
let values: number[] = [15, 20, 25, 30];
let values: Array<number> = [15, 20, 25, 30];
```
### What is any type, and when to use it
There are times when you want to store a value in a variable but don’t know the type of that variable in advance. For example, the value is coming from an API call or the user input. The ‘any’ type allows you to assign a value of any type to the variable of type any.
```js
// json may come from a third-party API
const employeeData: string = `{"name": "John Doe", "salary": 60000}`;

// parse JSON to build employee object
const employee: any = JSON.parse(employeeData);
```
TypeScript assumes a variable is of typeanywhen you don’t explicitly provide the type, and the compiler cannot infer the type from the surrounding context. 
### What is void, and when to use the void type
The void indicates the absence of type on a variable. It acts as the opposite type to any. It is especially useful in functions that don’t return a value.
```js
function notify(): void {
alert("The user has been notified.");
}
````
If a variable is of type void, you can only assign the null or undefined values to that variable
### What is an unknown type
The unknown type is the type-safe counterpart of any type. You can assign anything to the unknown, but the unknown isn’t assignable to anything but itself and any, without performing a type assertion of a control-flow-based narrowing. You cannot perform any operations on a variable of an unknown type without first asserting or narrowing it to a more specific type
```js
let foo: unknown = "Akshay";
let bar: string = foo; // Type 'unknown' is not assignable to type 'string'.(2322)
```
You can narrow down a variable of an unknown type to something specific by doing typeof checks or comparison checks or using type guards. For example, we can get rid of the above error by
```js
let foo: unknown = "Akshay";
let bar: string = foo as string;
```
### Explain the arrow function syntax in TypeScript
```js
function add(x: number, y: number): number {
let sum = x + y;
return sum;
}
```
Using arrow functions syntax, the same function can be defined as:
```js
let add = (x: number, y: number): number => x + y;
```
### Provide the syntax of a function with the type annotations
Here’s the TypeScript syntax to create and call a function
```js
function greet(name: string): string {
return `Hello, ${name}`;
}

let greeting = greet("Anders");
console.log(greeting); // "Hello, Anders"
```
### How to create objects in TypeScript
```js
let pt: { x: number; y: number } = {
x: 10,
y: 20
};
```
### How to specify optional properties in TypeScript
```js
let pt: { x: number; y: number; z?: number } = {
x: 10,
y: 20
};
```
In the example above, because the property ‘z’ is marked as optional, the compiler won’t complain if we don’t provide it during the initialization.
### Explain the concept of null and its use in TypeScript
In programming, a null value indicates an absence of value.
```js
function greet(name: string | null) {
if (name === null) {
console.log("Name is not provided");
} else {
console.log("Good morning, " + name.toUpperCase());
}
}

var foo = null;
greet(foo); // "Name is not provided"

foo = "Anders";
greet(foo); // "Good morning, ANDERS"
```
### What is undefined in TypeScript
When a variable is declared without initialization, it’s assigned the undefined value.
``js
console.log(null == null); // true
console.log(undefined == undefined); // true, loose equality
console.log(null == undefined); // true, with type-conversion

console.log(null === undefined); // false, without type-conversion, strict equality
console.log(0 == undefined); // false
console.log('' == undefined); // false
console.log(false == undefined); // false
```
### Explain how enums work in TypeScript
```js
enum Team {
Alpha,
Beta,
Gamma,
Delta
}
let t: Team = Team.Delta;
```
By default, the enums start the numbering at 0. You can override the default numbering by explicitly assigning the values to its members.
```js
enum Author {
Anders = "Anders",
Hejlsberg = "Hejlsberg"
};
```
### What is the typeof operator? How is it used in TypeScript?
Similar to JavaScript, the typeof operator in TypeScript returns the type of the operand as a string
```js
console.log(typeof 10); // "number"

console.log(typeof 'foo'); // "string"

console.log(typeof false); // "boolean"

console.log(typeof bar); // "undefined"
```
In TypeScript, you can use the typeof operator in a type context to refer to the type of a property or a variable.
```js
let greeting = "hello";
let typeOfGreeting: typeof greeting; // similar to let typeOfGreeting: string
```
### What are the rest parameters and arguments in TypeScript
A rest parameter allows a function to accept an indefinite number of arguments as an array. It is denoted by the ‘…’ syntax and indicates that the function can accept one or more arguments.
```js
function add(...values: number[]) {
let sum = 0;
values.forEach(val => sum += val);
return sum;
}
const sum = add(5, 10, 15, 20);
console.log(sum); // 50
```
In contrast, the rest arguments allow a function caller to provide a variable number of arguments from an array.
```js
const first = [1, 2, 3];
const second = [4, 5, 6];

first.push(...second);
console.log(first); // [1, 2, 3, 4, 5, 6]
```
### What is parameter destructuring
Parameter destructing allows a function to unpack the object provided as an argument into one or more local variables. 
```js
function multiply({ a, b, c }: { a: number; b: number; c: number }) {
console.log(a * b * c);
}

multiply({ a: 1, b: 2, c: 3 });

You can simplify the above code by using an interface or a named type, as follows:
type ABC = { a: number; b: number; c: number };

function multiply({ a, b, c }: ABC) {
console.log(a * b * c);
}

multiply({ a: 1, b: 2, c: 3 });
```
### Explain the TypeScript class syntax
```js
class Employee {
name: string;
salary: number;

constructor(name: string, salary: number) {
this.name = name;
this.salary = salary;
}
promote() : void {
this.salary += 10000;
}
}
/ Create a new employee
let john = new Employee("John", 60000);

console.log(john.salary); // 60000
john.promote();
console.log(john.salary); // 70000
```
### Provide the syntax for optional parameters in TypeScript
```js
function greet(name: string, greeting?: string) {
if (!greeting)
greeting = "Hello";

console.log(`${greeting}, ${name}`);
}

greet("John", "Hi"); // Hi, John
greet("Mary", "Hola"); // Hola, Mary
greet("Jane"); // Hello, Jane
```
### What is the purpose of the tsconfig.json file?
A tsconfig.json file in a directory marks that directory as the root of a TypeScript project. It provides the compiler options to compile the project.
Here is a sample tsconfig.json file:
```js
{
"compilerOptions": {
"module": "system",
"noImplicitAny": true,
"removeComments": true,
"outFile": "../../built/local/tsc.js",
"sourceMap": true
},
"include": ["src/**/*"],
"exclude": ["node_modules", "**/*.spec.ts"]
}
```
### What are type assertions in TypeScript?
Sometimes, you as a programmer might know more about the type of a variable than TypeScript can infer. Usually, this happens when you know the type of an object is more specific than its current type. In such cases, you can tell the TypeScript compiler not to infer the type of the variable by using type assertions.

TypeScript provides two forms to assert the types.

1. as syntax:
```js
let value: unknown = "Foo";
let len: number = (value as string).length;
```
2. <> syntax:
```js
let value: unknown = "Foo";
let len: number = (<string>value).length;
```
Explain the tuple types in TypeScript.
Tuples are a special type in TypeScript. They are similar to arrays with a fixed number of elements with a known type. However, the types need not be the same.
```js
// Declare a tuple type and initialize it
let values: [string, number] = ["Foo", 15];

// Type 'boolean' is not assignable to type 'string'.(2322)
// Type 'string' is not assignable to type 'number'.(2322)
let wrongValues: [string, number] = [true, "hello"]; // Error
```
Since TypeScript 3.0, a tuple can specify one or more optional types using the ? as shown below.
```js
let values: [string, number, boolean?] = ["Foo", 15];
```
### What are type aliases? How do you create one?
Type aliases give a new, meaningful name for a type. They don’t create new types but create new names that refer to that type.
For example, you can alias a union type to avoid typing all the types everywhere that value is being used.
```js
type alphanumeric = string | number;
let value: alphanumeric = "";
value = 10;
```
### What are intersection types?
Intersection types let you combine the members of two or more types by using the ‘&’ operator. This allows you to combine existing types to get a single type with all the features you need.
The following example creates a new type Supervisor that has the members of types Employee and Manager.
```js
interface Employee {
work: () => string;
}

interface Manager {
manage: () => string;
}

type Supervisor = Employee & Manager;

// john can both work and manage
let john: Supervisor;
```
### What are union types
A union type is a special construct in TypeScript that indicates that a value can be one of several types. A vertical bar (|) separates these types.
Consider the following example where the variable value belongs to a union type consisting of strings and numbers. The value is initialized to string “Foo”. Because it can only be a string or a number, we can change it to a number later, and the TypeScript compiler doesn’t complain. 
```js
let value: string | number = "Foo";
value = 10; // Okay
```
However, if we try to set the value to a type not included in the union types, we get the following error. 
```js
value = true; // Type 'boolean' is not assignable to type 'string | number'.(2322)
``` 
Union types allow you to create new types out of existing types. This removes a lot of boilerplate code as you don’t have to create new classes and type hierarchies
### How to make object properties immutable
You can mark object properties as immutable by using the readonly keyword before the property name. For example:
```js
interface Coordinate {
readonly x: number;
readonly y: number;
}
```
When you mark a property as readonly, it can only be set when you initialize the object. Once the object is created, you cannot change it. 
```js
let c: Coordinate = { x: 5, y: 15 };
c.x = 20; // Cannot assign to 'x' because it is a read-only property.(2540)
```
### Explain the purpose of the ‘in’ operator.
The in operator is used to find if a property is in the specified object. It returns true if the property belongs to the object. Otherwise, it returns false.
```js
const car = { make: 'Hyundai', model: 'Elantra', year: 2017 };
console.log('model' in car); // true
console.log('test' in car); // false
```
### What are conditional types? How do you create them?
A conditional type allows you to dynamically select one of two possible types based on a condition. The condition is expressed as a type relationship test.
```js
C extends B ? TypeX : TypeY
```
Here, if type C extends B, the value of the above type is TypeX. Otherwise, it is TypeY.
### 26. Provide the TypeScript syntax to create function overloads
```js
function reverse(str:string):string;
function reverse(arr:any[]):any[];
function reverse(
  stringOrArray:string|any[]
):string|any[] {
  if (typeof stringOrArray === 'string')
    return stringOrArray.split('').reverse().join('');
  else
    return stringOrArray.slice().reverse();
}
```
### What is meant by type inference
TypeScript can infer the type of a variable when you don’t provide an explicit type. This is known as type inference. This is usually done when the variables or parameters are initialized during the declaration.

For example, TypeScript knows that the variable foo is a string, even though we don’t mention string as a type.
```js
let foo = "this is a string";
console.log(typeof foo);  // "string"
```
### What are interfaces
They act as a contract in your code by defining the properties and methods that an object must implement.
```js
interface Employee {
name: string;
salary: number;
}

function process(employee: Employee) {
console.log(`${employee.name}'s salary = ${employee.salary}`);
}

let john: Employee = {
name: "John Doe",
salary: 150000
}

process(john);  // "John Doe's salary = 150000"
```
###  What are generics
Here's an example of a generic function that returns whatever is passed into it:
```js
function identity<T>(arg: T): T {
  return arg;
}

console.log(identity("myString"));  // Output: myString
```
In this example, T acts as a placeholder for any type. The type is typically inferred automatically by TypeScript, but you can specify the exact T when calling the function.
### Optional Chaining
Optional chaining is another feature supported by TypeScript. It allows you to access deeply nested properties without worrying if an intermediate property is null or undefined.

For example:
```js
type User = {
  name: string;
  address?: {
	street?: string;
	city?: string;
  }
}

const user: User = {
  name: 'John Doe',
};

console.log(user.address?.city);  // Outputs: undefined
```
### what type compatibility
If a type Y has at least the same members as type X, then type Y is said to be compatible with type X.
```js
interface Named {
  name: string;
}

class Person {
  name: string;
}

let p: Named;
p = new Person();  // OK, because 'Person' has a 'name' member
```
### what are type aliases
Type aliases allow you to create new names for types. They’re kind of similar to interfaces, but they can also name primitives, unions, tuples, and any other types that you'd otherwise have to write by hand.

Once you've defined a type alias, you can use it in places where you would use any other type:
```js
type Point = {
  x: number;
  y: number;
};

function drawPoint(p: Point) {
  console.log(`The point is at position ${p.x}, ${p.y}`);
}

drawPoint({ x: 10, y: 20 }); // Outputs: "The point is at position 10, 20"
### what is the difference between type and interface
TypeScript allows interface declarations with the same name to be merged. It's a key advantage of interfaces; but, you can't do this with type aliases.
```js
interface User {
  name: string;
}

interface User {
  age: number;
}

let user: User = {
  name: 'John Doe',
  age: 30,
};
```
type alias supports union (|), intersection (&), typeof, etc, which allows you to create more complex types.
```js
type User = {
  name: string;
} & {
  age: number;
};

let user: User = {
  name: 'John Doe',
  age: 30,
};
```

If you want to create complex type combinations, you'd use type. If you're describing object shapes, interface is usually the better choice.
```js
type User = {
  name: string;
} & {
  age: number;
};

let user: User = {
  name: 'John Doe',
  age: 30,
};
```
###  read-only arrays and objects
However, once defined as ReadonlyArray, you can't alter the array. This means that any attempt to mutate the array such as push, pop, or assignment will result in a compile-time error:
```js
const arr: ReadonlyArray<number> = [1, 2, 3, 4, 5];

arr.push(6);  // Error: Property 'push' does not exist on type 'readonly number[]'.
arr[0] = 10;  // Error: Index signature in type 'readonly number[]' only permits reading.
```
Similarly, you can make the properties of an object read-only using the Readonly<T> generic utility type where T is the type of the object:
```js
type Point = {
  x: number;
  y: number;
};

const point: Readonly<Point> = {
  x: 10,
  y: 20
};
```
Any attempt to change the properties of point will result in a compile-time error:
```js
point.x = 30; // Error: Cannot assign to 'x' because it is a read-only property.
```
# Redux
### Why zustand over redux?
- It's a more light-weight solution. In javascript world, less code is the truth.
- Equally powerful but easier to use.
- Use hooks to consume state. This will bring programmers a better development experience.
## Make a slider
# Animation
## requestAnimationFrame
## Move an object in a circular path
# HTTP
## HTTP codes
- 200 – OK – the successful request. If the customer has requested any data, then these data are in the header and/or body of the message.
- 201 – OK – a new resource was created as a result of the successful execution of the request.
- 204 – OK – the resource was successfully deleted.
- 304 – Not Modified – the client can use the data from the cache.
- 400 – Bad Request – the request is invalid or could not be processed.
- 401 – Unauthorized – the request requires user authentication.
- 403 – Forbidden – the server understood the request, but refuses to handle it or access is denied.
- 404 – Not found – the resource was not found.
- 500 – Internal Server Error – API developers should try to avoid such errors. These errors must be caught in the global catch-block and be logged, but they should not be returned in the response.

## RESTful Design
[RESTful架构及实践修炼宝典](https://cloud.tencent.com/developer/article/1543848)
# MVC & MVVM
# Performance
## React.js Performance Best Practices
1. **remove unnecessary http requests**
2. **remove unnecessary rerender**
3. **compress all assets**
4. **Web Workers**  use web workders to perform heavy computation, this will reduce the burden on the main thread
5. **use useCallback() hook to memoize a function, use useMemo() hook to memoize the function result.**
6. **List Virtualization** Instead of rendering all the items at once, we only render the items that are currently visible on the screen
7. **Lazy Loading Images** Instead of loading images at page load time, we load them when they enter the viewport.
8. **code splitting** Instead of loading the entire bundle, a small chunk will be loaded when a user visits a particular page or triggers a specific action

# Security
## Best Practices for Developing Secure Web Applications
1. **Use secure coding practices** Follow secure coding practices to avoid common vulnerabilities such as cross-site scripting (XSS) and SQL injection.
2. **Implement proper authentication and authorization** Implement proper authentication and authorization mechanisms to ensure that only authorized users can access sensitive information.
3. **Validate and sanitize user input data** Validate and sanitize user input data to prevent malicious code injection and other attacks.
4. **Use HTTPS protocol to secure communication** Use HTTPS protocol to encrypt communication between your app and server to protect sensitive data from eavesdropping.
5. **Keep your React.js version up to date** Keep your React.js version up to date to take advantage of security patches and bug fixes.
6. **Use reliable third-party libraries** Use reliable and trusted third-party libraries to avoid introducing security vulnerabilities into your app.
7. **Secure your backend APIs** Secure your backend APIs to prevent unauthorized access and protect sensitive data.

# Test
## spy vs stub vs mock
1. Spies are used to get information about function calls. For example, a spy can tell us how many times a function was called, what arguments each call had, what values were returned, what errors were thrown, etc.

2. Stubs are like spies, except in that they replace the target function. They can also contain custom behavior, such as returning values or throwing exceptions. They can even automatically call any callback functions provided as parameters.Stubs have a few common uses:
- You can use them to replace problematic pieces of code
- You can use them to trigger code paths that wouldn’t otherwise trigger – such as error handling
- You can use them to help test asynchronous code more easily

3. mocks can be used to replace whole objects and alter their behavior similar to stubbing functions.They are primarily useful if you need to stub more than one function from a single object. If you only need to replace a single function, a stub is easier to use.
# bundle & build
# Browser
## Chrome DevTools
**search by filename**
cmd + O

**search in all files**
cmd + option + f

search 
## 浏览器渲染页面的基本流程
1. 解析HTML文件，创建DOM树--自上而下，遇到任何样式（link、style）与脚本（script）都会阻塞（外部样式不阻塞后续外部脚本的加载）。
2. 解析CSS。
3. 将CSS与DOM合并，构建渲染树（Render Tree）
4. 布局和绘制，重绘（repaint）和重排（reflow）

## cookies sessionStorage和localstorage区别
1. 存储大小：cookie数据大小不能超过4k。sessionStorage和localStorage 虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大。
2. 有效期：cookie设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭；sessionStorage数据在当前浏览器窗口关闭后自动删除；localStorage存储持久数据，浏览器关闭后数据不丢失除非主动删除数据。
3. 数据与服务器之间的交互方式：cookie的数据会自动的传递到服务器，服务器端也可以写cookie到客户端；sessionStorage和localStorage不会自动把数据发给服务器，仅在本地保存。
4. 有效期：cookie设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭；sessionStorage数据在当前浏览器窗口关闭后自动删除；localStorage存储持久数据，浏览器关闭后数据不丢失除非主动删除数据。

# NPM
## Semantic Versioning
### ~version "Approximately equivalent to version"
Tilde Ranges
- ~1.2.3 := >=1.2.3 <1.(2+1).0 := >=1.2.3 <1.3.0-0
- ~1.2 := >=1.2.0 <1.(2+1).0 := >=1.2.0 <1.3.0-0 (Same as 1.2.x)
- ~1 := >=1.0.0 <(1+1).0.0 := >=1.0.0 <2.0.0-0 (Same as 1.x)
- ~0.2.3 := >=0.2.3 <0.(2+1).0 := >=0.2.3 <0.3.0-0
- ~0.2 := >=0.2.0 <0.(2+1).0 := >=0.2.0 <0.3.0-0 (Same as 0.2.x)
- ~0 := >=0.0.0 <(0+1).0.0 := >=0.0.0 <1.0.0-0 (Same as 0.x)

### ^version "Compatible with version"
Caret Ranges
- ^1.2.3 := >=1.2.3 <2.0.0-0
- ^0.2.3 := >=0.2.3 <0.3.0-0
- ^0.0.3 := >=0.0.3 <0.0.4-0

# 综合题
1. 一个交互元素众多的表单，甚至表单的若干部分是由不同团队独立开发的，那么如何在用户体验，性能，安全，维护与升级等方面进行考量与改进
2. 如何记录和管理用户在站点使用过程中的所有bug
3. 网页中涵盖哪些尺寸单位，如何使用
4. 网页中的交互元素有哪些，如何使用
5. 基于token的权限认证与授权，实现机制的基本说明
6. 在页面上显示一个圆形，有哪些方案
7. 把一个div从页面左侧移动到页面右侧，有哪些方案
## 8. 为用户做了一个Demo程序，但又不想直接暴露本方的源码给客户，怎么处理？ ##
这里涉及到的是这些方面：Minification, Uglification(Obfuscation), Compression

```js
npm install -g javascript-obfuscator
   
javascript-obfuscator scripts.js --output s.js --compact true --string-array true --string-array-encoding base64 --rename-properties true
```   
通过上面的命令，最终输出的s.js文件，是被缩减，混淆处理后的文件，无法阅读出原有代码逻辑，尤其是string literals（包括属性名在内）也被替换成string array，对外不可见。
   
## 9. 浏览器端生成token ##
```js
<script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.2.0/crypto-js.min.js"></script>

function base64url(source) {
  let encodedSource = CryptoJS.enc.Base64.stringify(source);
  encodedSource = encodedSource.replace(/=+$/, '');
  encodedSource = encodedSource.replace(/\+/g, '-');
  encodedSource = encodedSource.replace(/\//g, '_');
  return encodedSource;
}

function jwtSign(payload, secretOrPrivateKey) {
  const header = { 'alg': 'HS256', 'typ': 'JWT' };
  const stringifiedHeader = CryptoJS.enc.Utf8.parse(JSON.stringify(header));
  const encodedHeader = base64url(stringifiedHeader);
  const stringifiedPayload = CryptoJS.enc.Utf8.parse(JSON.stringify(payload));
  const encodedPayload = base64url(stringifiedPayload);
  const encodedSignature = base64url(CryptoJS.HmacSHA256(encodedHeader + "." + encodedPayload, secretOrPrivateKey));

  return encodedHeader + "." + encodedPayload + "." + encodedSignature;
}

const token = jwtSign({ 'field1': 'test1', 'fileId2': 'test2'}, 'test key');
console.log(token);
```
## 10. 使用原生js访问和解析Server-sent events事件流 ##
```js
function sseevent(message) {
  let type = 'message', start = 0;
  if (message.startsWith('event: ')) {
    start = message.indexOf('\n');
    type = message.slice(7, start);
  }
  start = message.indexOf(': ', start) + 2;
  let data = message.slice(start, message.length);

  return new MessageEvent(type, { data: data })
}

function XhrSource(url, opts) {
  const eventTarget = new EventTarget();
  const xhr = new XMLHttpRequest();

  xhr.open(opts.method || 'GET', url, true);
  for (var k in opts.headers) {
    xhr.setRequestHeader(k, opts.headers[k]);
  }

  var ongoing = false, start = 0;
  xhr.onprogress = function () {
    if (!ongoing) {
      // onloadstart is sync with `xhr.send`, listeners don't have a chance
      ongoing = true;
      eventTarget.dispatchEvent(new Event('open', {
        status: xhr.status,
        headers: xhr.getAllResponseHeaders(),
        url: xhr.responseUrl,
      }));
    }

    var i, chunk;
    while ((i = xhr.responseText.indexOf('\n\n', start)) >= 0) {
      chunk = xhr.responseText.slice(start, i);
      start = i + 2;
      if (chunk.length) {
        eventTarget.dispatchEvent(sseevent(chunk));
      }
    }
  }

  xhr.onloadend = _ => {
    eventTarget.dispatchEvent(new CloseEvent('close'))
  }

  xhr.timeout = opts.timeout;
  xhr.ontimeout = _ => {
    eventTarget.dispatchEvent(new CloseEvent('error', { reason: 'Network request timed out' }));
  }
  xhr.onerror = _ => {
    eventTarget.dispatchEvent(new CloseEvent('error', { reason: xhr.responseText || 'Network request failed' }));
  }
  xhr.onabort = _ => {
    eventTarget.dispatchEvent(new CloseEvent('error', { reason: 'Network request aborted' }));
  }

  eventTarget.close = _ => {
    xhr.abort();
  }

  xhr.send(opts.body);
  return eventTarget;
}

const xs = XhrSource(remoteURL, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Authorization: 'Bearer ...'
    },
    body: JSON.stringify(question)
  });

// 监听各个关心的event
xs.addEventListener('answer', e => {
  const msg = JSON.parse(e.data);
    if (msg.choices[0].delta.content) {
      //  逐字输出事件流返回的内容
      $('.tab-pane:not(.is-hidden) .chat-window .ai-response:last')[0].textContent += `${msg.choices[0].delta.content}`;
    }
});
```
## 11. 检查聊天窗口的滚动条是否到底，如果没有，使其滚动到底以显示聊天窗口中的最新消息 ##
```js
function chatWindowScrollToBottom() {
  // 使滚动条滚到底，显示全部回复，方便用户浏览
  const chatWindow = $('.tab-pane:not(.is-hidden) .chat-window')[0];
  if (chatWindow.scrollHeight - chatWindow.offsetHeight - chatWindow.scrollTop > 1) {
    $(chatWindow).scrollTop(chatWindow.scrollHeight);
  }
}
```
