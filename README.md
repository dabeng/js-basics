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

// ES6 version
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

// ES6 version
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

// ES6 repeat()
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
## jQuery Plugin
## ES6 Class
## React SFC
## Vuejs Single File component
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
