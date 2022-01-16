# JS笔记

## JS 基础

### 外嵌JS代码的方式

~~~JavaScript
<script type="text/javascript" src="js/Hello.js"></script> src 属性标识js的文件的路径
~~~

js 如果引入了外部的文件了，就不能在编写代码了，即使编写了，浏览器也会忽略，如果，如果需要，可以新建一个js标签

### JS的数据类型

1. 基本数据类型

   * Boolean

   * String 

   * Number

     ```javascript
     console.log(typeof  Number.MAX_VALUE); // number  typeof查看类型
     var a = NaN;
     console.log(typeof  a); // NaN 也是一number类型
     ```

   * Null

     ~~~javascript
     var b = null;
     console.log(typeof b); // object 这里返回值不是null，Null类型只有一个值就是null，专门用来表示空对象
     ~~~

   * Undefined

     ~~~javascript
     var b;
     console.log(b); // undefined 如果定义一个变量，没有赋值，该变量就是undefined，该类型只有一个值 
     ~~~


2. 引用数据类型

   * Object

     ~~~javascript
     // 创建对象的几种方式
     var obj1 = new Object();
     obj1.name = "z3";
     obj1.age = 10;
     console.log(obj1); // {name: "z3", age: 10}
     
     var obj2 = {};
     obj2.name = "w5";
     obj2.age = 23;
     console.log(obj2); // {name: "w5", age: 23}
     
     var obj3 = {
         name : "l4",
         age : 20,
         pet : {
             name : "xiami",
             age : 2,
             gender : "female"
         }
     }
     console.log(obj3); // {name: "l4", age: 20, pet: {name: "xiami", age: 2, gender: "female"}}
     ~~~


### 类型转化

1. 将其他数据类型转为Number

   * 字符串转为数字，字符中有非数字的话，变为NaN

   * 如果字符串是空字符串，或者是全是空格的字符串，则转化为0

   * null 转为数字变为0；undefined 转为数字变为NaN

   * ~~~javascript
     var a = "123px";
     console.log(a); // 123px
     console.log(parseInt(a)); // 123 该函数从左向右开始读取数字，读到非整数，停止
     ~~~

   * ~~~javascript
     var res = true + "isea";
     console.log("res =" ,res); // res = trueisea 任何值和字符串做加法运算，都会先转化为字符串，然后在和字符串做拼接
     ~~~

   * ~~~javascript
     var res = true;
     console.log("res = ",res); // res = true
     res += 1;
     console.log("res = ",res); // res = 2 任何值做-，*，/ 运算的时候都自动转为Number
     ~~~

   * ```javascript
     {
         console.log("hello");
         alert("world");
         document.write("isea");
     }// {}代码块中的代码要么都被执行，要么都不执行
     ```

### 从键盘读值

```javascript
var num = prompt("please input a num:");
document.write("num = ", num);
```

## 函数

### 函数的创建

1. 使用`function`关键字来创建函数

   ~~~ javascript
   function fun1() {
      console.log("this is function1..");
   }
   
   fun1(); // this is function1..
   ~~~

2. 创建匿名函数并将匿名函数赋值给变量

   ```javascript
   var fun2 =  function () {
      console.log("this is function2..");
   }
   
   fun2(); // this is function2..
   ```

### 匿名函数

~~~javascript
var obj = {
    name : "xiaoli",
    age : 22,
    getInfo : function () {
        console.log(this.name);
        console.log(this.age);
    }
}
console.log(obj); // {name: "xiaoli", age: 22, getInfo: ƒ}
obj.getInfo(); // xiaoli \n 22
~~~

匿名函数作为参数

```javascript
var obj = {
    name : "xiaoli",
    age : 22
}
function getInfo(a) {
    console.log(a.name + " " + a.age);
}
var f = function (a) {
    console.log(a);
}
f(getInfo); // 将一个函数做为参数传入到另外一个函数中去
/*
    ƒ getInfo(a) {
         console.log(a.name + " " + a.age);
      }
*/
f(getInfo(obj)); // xiaoli 22 \n undefined 由于getInfo函数没有返回值，所以值就直接打印undefined
```

### 函数的调用

```javascript
var f1 = function () {
    var f2 = function () {
        console.log("i am f1..");
    }
    return f2;
}

var f3 = f1();
// 执行函数的方式
f3(); // i am f1..
f1()(); // i am f1..
```

### 立即执行函数

```JavaScript
// 立即执行函数
(function () {
    console.log("i am an instant function..");
})(); //i am an instant function..
```

### 方法

**如果一个函数作为一个对象的属性保存，那么该函数也叫做方法**

```javascript
var obj = {
    name : "isea",
    sayName : function () {
      console.log("my name is : " + this.name); // my name is : isea
    }
}
obj.sayName();
```

## 作用域

JS中有一个全局对象window（浏览器窗口），在全局作用域中

* 创建的对象，会作为window的属性来保存
* 创建的函数，会作为window的方法来保存
* 使用`var`声明的变量，在所有的代码执行之前被声明，但是不会被赋值，等到具体的执行的时候才会被赋值
* 使用 ```function f_name() ```形式创建的函数，会在所有代码之前之前就被创建
* 使用 ``` var f_v = function() 创建的函数，只是变量会被提前声明，实际没有被赋值 ``` 

## this

**浏览器在每次调用函数的时候，都会向函数的内部传入一个固定的参数this，这个this指向一个对象，这个对象就是函数执行的上下文对象，根据调用函数的方式不同，this指向不同的对象**

* 以函数的形式调用的时候，this永远指向**window**
* 以方法的形式调用的时候，this指向调用该方法的对象

```javascript
var f = function () {
    console.log(this);
}

var obj = {
    name : "xiaomi",
    sayName : f
}
obj.sayName(); // {name: "xiaomi", sayName: ƒ}
f();// Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
```

* 当以构造函数的形式调用的时候，this就是创建的那个对象

  ```javascript
  function Person(name) {
      this.name = name;
      this.sayName = sayName;
  }
  function sayName() {
      console.log(this.name);
  }
  
  var p1 = new Person("z3");
  var p2 = new Person("l4");
  
  console.log(p1); // Person {name: "z3", sayName: ƒ}
  console.log(p2); // Person {name: "l4", sayName: ƒ}
  
  p1.sayName(); // z3
  p2.sayName(); // l4
  console.log(p1.sayName == p2.sayName); // true
  ```

## arguments

* 在函数执行的时候，除了隐含调用this之外，还会调用arguments参数，该参数封装实参，是一个类数组对象（不是数组，但是可以通过索引的方式来访问）

* arguments还有一个属性叫做callee，这个属性就是当前执行的函数对象

  ```javascript
  function fun() {
      console.log(arguments.length); // 获取实参的个数 3
      console.log(arguments.callee == fun); // true  调用fun的时候，当前执行的函数对象就是fun，因此为true。
  }
  
  fun(1,12,23);
  ```

## 构造函数

**构造函数就是一个普通的函数，不同的是构造函数习惯上首字母大写，普通函数直接调用，构造函数使用new关键字来调用。** 构造函数的执行流程如下：

* 只要调用了构造函数，立即会创建一个对象
* this指向构造函数创建出来的对象
* 将新建的对象作为返回值返回

```javascript
function Person(name) {
    this.name = name;
    this.sayName = function () {
        console.log(this.name);
    }
}
var p1 = new Person("z3");
var p2 = new Person("l4");

console.log(p1); // Person {name: "z3", sayName: ƒ}
console.log(p2); // Person {name: "l4", sayName: ƒ}
console.log(p1 instanceof Person); // true
```

## 事件

**事件**就是用户和浏览器之间的交互行为，我们可以在事件对应的属性中设置一些JS代码，这样当事件触发的时候，这些代码会被执行。

```html
<button id = "bit" >点我打印</button>
<script type="text/javascript">
    var but = document.getElementById("bit");
    but.onclick = function(){
        console.log("i am a button.."); 
    };
</script>
```

## call方法和apply方法

call方法和apply方法都是对象的方法，需要通过函数对象来调用，使用函数对象调用call方法或apply方法的时候，可以将一个对象作为参数传入到方法中，那么这个对象就会成为函数执行时的**```this```**

```javascript
function fun() {
    console.log(this.name);
}

var obj = {
    name : "isea"
}

fun.call(obj); // isea
fun.apply(obj); // isea
```

## 常用函数

### Date

```javascript
// 如果直接用构造函数创建一个Date对象，则会封装当前代码执行的时间
var date = new Date();
console.log(date);
// 注意创建固定日期的格式：
var time1 = new Date("06/08/19 0:53:22");
console.log(time1);
console.log(time1.getDate()); // getDate函数获当天时间对象是几日
console.log(time1.getDay()); //  getDay函数获取当前对象是周几 0 表示周日，1表示周一
console.log(time1.getMonth()); // getMonth 返回当前日期的月份，0表示的一月份。1表示的是2月份
console.log(time1.getFullYear()); // 获取年份
console.log(time1.getTime()); // 获取时间戳
```

### Math

Math ： Math和其他的对象不同，她不是一个构造函数，它属于一个工具类不需要创建对象，她里面封装了数学运算相关的方法和属性

```javascript
console.log(Math.PI);
console.log(Math.ceil(1.4)); // ceil向上取整
console.log(Math.floor(1.9)); // floor 想下取整
console.log(Math.round(1.5)); // 四舍五入
console.log(Math.random()) // random()产生[0,1)前闭后开的小数
console.log(Math.max(1, 3, 89, 100, 2)); // min 求最小值
```

### toString方法

当我们直接在控制台打印一个对象的时候，实际上控制台打印的是这个对象toString()方法的返回值。

## 垃圾回收

```javascript
/**
 * 当一个对象没有任何一个变量或者属性引用的时候，此时该对象在堆内存中就是垃圾。
 * 该部分内存的回收是由浏览器引擎来进行回收。不需要手动回收。
 *
 */
var obj = new Object();
obj = null; // 我们如果想让一个对象被回收，只需要将该对象赋值为null就OK了。
```

## 数组

数组也是**对象**，数组使用索引来操作属性。

```javascript
var arr = ["xiaomi","apple","huawei","zhongxin","vivo"];
// 这个方法接收一个函数作为参数，该函数接受每一次遍历到的元素，作为自己的参数，这个函数接收三个参数
arr.forEach(function (value,index,obj) {
    console.log(value + ":" + index);
});
```

数组中的方法：

```javascript
var arr = [1,0,2,3,5];
console.log(arr); // (5) [1, 0, 2, 3, 5]

var res = arr.sort();
console.log(res);// (5) [0, 1, 2, 3, 5]

var re = arr.sort(function (a, b) {return b - a });
console.log(re); // (5) [5, 3, 2, 1, 0]
```

## 自动装箱

```javascript
var s1 = "hello";
var s2 = "hello";
var s3 = new String("hello");
var s4 = new String("hello");
console.log(s1 == s2); // true  都是基本数据类型
console.log(s1 == s3); // true  将对象地址直接指向了基本数据类型所在的值
console.log(s3 == s4); // false 两个都是对象
```

当我们使用基本数据类型的值调用其属性和方法的时候，浏览器会使用包装类将其包装成对象，然后在调用其属性和方法调用完成之后，在转成基本数据类型。

## DOM 

### onload

为window绑定一个onload事件的作用是，所有的dom都加载完毕了之后，再执行js代码

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script type="text/javascript">
        window.onload = function (ev) { // 此时如果不使用window.onload 的话，会报错
            var but = document.getElementById("but");
            but.onclick = function (ev) {
                alert("hello..");
            };
        };
    </script>
</head>
<body>
    <button id = "but" >点我</button>
</body>
</html>
```

## 原型

我们创建的每一个函数，浏览器都会给该函数添加一个属性，这个属性对应着一个对象，这个对象就是原型对象。

当函数以构造函数调用时，它创建的对象中都有一个隐含的属性，该属性指向构造函数的原型对象，可以通过```__proto__```来访问。

原型对象是所有的对象共有的内容，我们可以将所有的对象共有的内容放置到原型对象中。

```javascript
function Person(name,age) {
    this.name = name;
    this.age= age;
};

Person.prototype.sayName = function () { // 相当于在构造函数中添加了sayName函数
    console.log("hello " + this.name);
};

var p1 = new Person("ise",54);
var p2 = new Person("xiao",33);
console.log(p1);
/**
 Person {name: "ise", age: 54}
 age: 54
 name: "ise"
 __proto__:
 sayName: ƒ ()
 constructor: ƒ Person(name,age)
 __proto__: Object
 */

console.log(p2);
/**
     Person {name: "xiao", age: 33}
     age: 33
     name: "xiao"
     __proto__: Object
 */


p1.sayName(); // hello ise
p2.sayName(); // hello xiao
```



```javascript
function MyClass() {
}

MyClass.prototype.name = "我是原型中的属性";
var mc = new MyClass();
console.log(mc);
/**
     MyClass {}
     __proto__:
     name: "我是原型中的属性"
     constructor: ƒ MyClass()
     __proto__: Object
*/

console.log("name" in mc);  // true 检查MyClass中是否有name属性，


console.log(mc.hasOwnProperty("name"));  // false 检查对象自身中是否有这个属性
/**
 *  原型对象也有原型，当我们使用一个对象的属性或者是方法的时候，会现在自身的对象中寻找，
 *      如果自身有，则直接使用
 *      如果没有则去原型中寻找，如果原型中有，则使用
 *      如果原型中没有，则去原型的原型中寻找。直到找到Object的原型，
 *      Object对象没有原型 ，如果在Object对象中也没有找到，则返回undefined
 */
console.log(mc.__proto__.__proto__.hasOwnProperty("hasOwnProperty")); // true
```

