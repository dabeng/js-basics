# JavaScript
## hotch-potch
### swap two variables
```js
// solution 1
b = [a, a = b][0];

// solution 2: ES 6 destructuring assignment
[a, b] = [b, a]
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
#### Deep Copy
`let copy = JSON.parse(JSON.stringify(obj));`

This works for all kind of objects containing objects, arrays, strings, booleans and numbers.
Unfortunately, this method can't be used to copy user-defined object methods.
#### Deep Copy & Copying Object Methods
`let copy = Object.create(obj);`
#### Native Deep Copy
```js
function copy(obj) {
  let copy = {}; // objCopy will store a copy of the mainObj
  let key;

  for (key in obj) {
    copy[key] = obj[key]; // copies each property to the objCopy object
  }
  return copy;
}

const ref = 100;
const obj = { a: 1, b: ref };
const objCopy = copy(obj);
objCopy.b = 123;
document.write(`<p>${objCopy.b}</p>`); // 123
document.write(`<p>${obj.b}</p>`); // 100
```
## Async Programming
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
let uniqueArr = arr.reduce((pre, cur) => {
  if (!pre.includes(cur)) {
    return pre.concat(cur);
  } else {
    return pre;
  }
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
Math.max.apply(null,arr.join(',').split(','));
Math.min.apply(null,arr.join(',').split(','));
```
### create an array containing 1…N
```js
Array.from(Array(N), (v, i) => i + 1);
```

## String
### Count the occurrence of a substring in a string
```js
// split()
str.split(subStr).length - 1

// match()
(str.match(new RegExp(subStr, 'g')) || []).length
```
#### Get the position of nth occurrence of a substring in a string
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

// half-index switch for-loop
function reverse(str) {
  str = str.split('');
  var len = str.length;
  var halfIndex = Math.floor(len / 2) - 1;
  var tmp = '';
  for (var i = 0; i <= halfIndex; i++) {
    tmp = str[len - i - 1];
    str[len - i - 1] = str[i];
    str[i] = tmp;
  }
  return str.join('');
}
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
## jQuery Plugin
## ES6 Class
## React SFC
## Vuejs Single File component
## Make a slider
# Animation
### Move an object in a circular path
# Chart
# Testing
# Performance
# Security
# MVC & MVVM
# bundle & build
