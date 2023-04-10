## 显示与输出

JavaScript 可以通过不同的方式来输出数据：

~~~js
window.alert() // 弹出警告框。
document.write() // 将内容写到 HTML 文档中
// 如果在文档已完成加载后执行 document.write，整个 HTML 页面将被覆盖。
innerHTM // 写入到 HTML 元素
console.log() // 写入到浏览器的控制台
~~~



## 同步与异步

js是单线程的 ,不能同时进行多个任务和流程

Js是单线程，但浏览器内部并不是单线程，I/O、定时器、事件监听等都是浏览器的其他线程完成的

同步：

- 上一件事情没有完成，继续处理上一件事情，只有上一件事情完成了，才会做下一件事情
- 对js来说，同步就是在浏览器执行js代码的时候，将所有同步的代码放到一个执行栈（先进后出）当中

异步：

- 规划要做一件事情，但是不是当前立马去执行这件事情，需要等一定的时间，跳过执行，继续执行下面的操作
- 遇到异步代码就把异步代码放到任务队列（先进先出）中，不进入主线程，只有引擎认为某个异步任务可以执行了（比如 Ajax 操作从服务器得到了结果），该任务（采用回调函数的形式）才会进入主线程执行。也就是说，异步任务不具有“堵塞”效应。
- 一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列" ，主线程从”任务队列”中读取事件，这个过程是循环不断的，所以整个的这种运行机制又称为Event Loop（事件循环）。
- js异步编程的四种情况
  - 定时器
  - 所有的事件绑定都是异步编程的
  - Ajax读取数据都是异步编程的，我们一般设置为异步编程
  - 回调函数都是异步编程的

~~~js
function log1 () { console.log(1) } ;
function log2 () { console.log(2) } ;
function log3 () { console.log(3) } ;

log1() ;
setTimeout(log1,4000) ;
setInterval(log2,1000) ;
setTimeout(log3,2000)
console.log(4)

// 1 ,2 -->主线程执行
// setTimeout(log1,4000) 开始计时，进入任务队列
// setInterval(log2,1000) 开始计时，进入任务队列
// setTimeout(log3,2000) 开始计时，进入任务队列
// 2 --> 1s
// 2,3 --2s
// 2 --> 3s
// 1,2 -->4s
// 2..... --> time++

~~~



### 阻塞/非阻塞

阻塞: 就是程序处于等待过程中, 必须等待当前程序执行完毕, 后面程序才能执行，如果前程序执行时间过长（或死循环），就会造成浏览器假死状态（无响应

非阻塞: 程序不必等待前一个程序执行完, 就能执行, 这正是Js想达到的目的。



## 基础语法

变量可以通过变量名访问。在指令式语言中，变量通常是可变的。字面量是一个恒定的值。

JavaScript语言有多种类型的运算符：

- 赋值，算术和位运算符：= + - * /
- 条件，比较及逻辑运算符：== != < >



### 创建变量/常量

创建新变量：var ，let

区别：

- 使用var声明的变量，其**作用域为该语句所在的函数内**，且存在变量提升现象(变量可以先使用再声明),在块中重新声明变量也会重新声明块外的变量
- 使用let声明的变量，其**作用域为该语句所在的代码块内**，不存在变量提升；

~~~js
function () {
    for(let i = 0 ; i<2; i++) {
         console.log(i) // 0,1
    }
    for(var v = 0 ; i<2; i++) ;
     console.log(i) // ReferenceError: i is not defined
     console.log(v) // 2
    
}
var carname="hello";
var carname;
 console.log(carname) // hello


~~~



- 在 for循环中 let 声明的 变量， 只在for循环中有效（作用域为该语句所在的代码块）

- 在函数外声明的变量作用域是全局的（window.onload  等加载函数除外）

- 重新声明 JavaScript 变量，该变量的值不会丢失

- 使用 **var** 关键字声明的全局作用域变量属于 window 对象(可以使用 window.carName 访问变量)

- 使用 **let** 关键字声明的全局作用域变量不属于 window 对象

- 在相同的作用域或块级作用域中，不能使用 **let** 关键字来重置 **var** 关键字声明的变量

- 在相同的作用域或块级作用域中，不能使用 **let** 关键字来重置 **let** 关键字声明的变量

- 在相同的作用域或块级作用域中，不能使用 **var** 关键字来重置 **let** 关键字声明的变量

- 局部变量在函数执行完毕后销毁,全局变量在页面关闭后销毁。

  



创建常量:const

- const 用于声明一个或多个常量，声明时必须进行初始化，且初始化后值不可再修改

- 块级作用域

- 不能和它所在作用域内的其他变量或函数拥有相同的名称

- 使用 const 定义的对象或者数组，是可变的

  ~~~js
  // 创建常量对象
  const car = {type:"Fiat", model:"500", color:"white"};
  // 修改属性:
  car.color = "red";
  // 添加属性
  car.owner = "Johnson";
  car = {type:"Volvo", model:"EX60", color:"red"};    // 错误,们不能对常量对象重新赋值
  
  // 修改常量数组
  const cars = ["Saab", "Volvo", "BMW"];
  cars[0] = "Toyota";  // 修改元素
  cars.push("Audi"); // // 添加元素
  ~~~

声明变量类型：new 关键字

声明新变量时，可以使用关键词 "new" 来声明其类型

~~~
var cars=   new Array;
var person= new Object;
~~~







### 数据类型

- **值类型(基本类型)**：字符串（String）、数字(Number)、布尔(Boolean)、空（Null）、未定义（Undefined）、Symbol
- **用数据类型（对象类型）**：对象(Object)、数组(Array)、函数(Function)，还有两个特殊的对象：正则（RegExp）和日期（Date）

#### 对象 object

对象也是一个变量，但对象可以包含多个值（多个变量），每个值以 **name:value** 对呈现

- 操作属性：点语法和中括号 （中括号的主要优势是可以通过变量访问属性

- 添加属性：object.新属性名 = ...

- 删除属性：delete object.需要删除的属性名
- 获取属性名：Object.getOwnPropertyNames('对象名') / Object.keys('对象名')
- 简写：属性名和对应值变量名称相同可简写
- 对象方法：对象方法通过添加 () 调用 (作为一个函数

~~~js
let object = {
    name,  // == name:name
    // 添加对象方法
methodName : function() {
    // 代码 
}
}
object.name = 'lisi'
object.age = 12
object['name'] // [] 里面可以是字符串，表达式，甚至是函数
delete people.name
Object.keys(object) // ["age"]
object.methodName() // 访问对象方法
~~~

- 合并对象：Object.assign()
- 展开：...
- 解构赋值：{对象匹配的属性:变量名} = object

~~~js
let obj1 = {a:1,b:2} ;
Object.assign(obj1,{c:3})
console.log(obj1) // {a:1,b:2,c:3}
let obj2 = {d:4,...obj1}
console.log(obj2)  // {d:4,a:1,b:2}
let {d:dd} = obj2;
console.log(dd); // 4
function obj1 ({a,b,c}) { // 函数解构传参
    console.log(a,b,c) // 1,2,3
}
~~~



#### 数组 []

数组的创建 ：

- var arr = [] ;
- var arr = new Array() ; (不建议使用这种方法来创建数组对象)

数组长度：.length

数组遍历--增强版for循环

~~~
var arr1 = [0,1,2,3];
for (var index in arr1) {
    console.log(arr1[index]); // 0,1,2,3
}
~~~

修改数组中的值：数组名[索引] = ‘修改值’

数组函数：

- .isAarray(arr) :判断一个对象是否为数组对象，返回true或者false

- .push() : 向数组尾部新增元素

- .pop(): 从数组最后一项删除

- .concat(): 合并数组，要用新的变量去接收

  ~~~
  arr1 = arr.concat([4,5]); //[0,1,2,3,4,5]
  ~~~

- .shift(): 从数组头部删除数据，只能删除一项

- .unshift() : 向数组头部新增元素

- .reverse(): 数组反转

- .sort():数组排序

  ~~~
  .sort(function(a, b){return a - b}); // 升序
  .sort(function(a, b){return a - b}); // 降序
  ~~~

  

- .slice(start,end): 数组切片，左闭右开 ，返回一个数组，不改变元数组

- .splice(start,end,elsedata)：将数组中指定索引的数据删除[并替换为其他数据:elsedata存在时]。这个方法会在原来的数组上进行修改,左闭右开

  ~~~
  arr1.splice(0,3,5,5)
  console.log(arr1) // /[5,5,2,3,4,5]
  ~~~

- .indexOf(要查的项，起点位置索引),如果查找成功，则返回索引，否则返回-1



#### 类型转换

 6 种数据类型：string，number，boolean，object，function，symbol

3 种对象类型：Object，Date，Array

2 个不包含任何值的数据类型：null，undefined

**typeof** ：查看 JavaScript 变量的数据类型

**constructor** ：返回所有 JavaScript 变量的构造函数

~~~js
// 使用 constructor 属性来查看对象是否为数组 (包含字符串 "Array")
function isArray(myArray) {
    return 
    myArray.constructor.toString().indexOf("Array") > -1;
}
~~~

类型转换：

- String()  : 将数字，字母，变量，表达式转换为字符串 

  == Number().toString() 

- 将字符串转换为数字

  - Number()
  - 'number' + /- number: 可用于将变量转换为数字

- json 与 js 之间的转换

  - JSON.stringify()–将JS对象转为JSON字符串，返回一个JSON字符串

  - JSON.parse()–将JSON字符串转为js对象

    ~~~js
    // 将JSON字符串转为js对象
    var json = '{"name":"ls","age":18}';
    var js = JSON.parse(json);   
    console.log(js.name);        //  ls
    
    var js1 = {name:"zs" , age:28 };
    var json1 = JSON.stringify(js1);
    console.log(json1); // '{"name":"zs","age":28}'
    ~~~

    

### 函数

“函数是对象”：JavaScript 函数有 **属性** 和 **方法**

- arguments.length 属性返回函数调用过程接收到的参数个数
- .toString() 方法将函数作为一个字符串返回

**箭头函数**：(参数1, 参数2, …, 参数N) => { 函数声明 } / 表达式(单一)

- 当我们使用箭头函数的时候，箭头函数会默认帮我们绑定外层 this 的值，所以在箭头函数中 this 的值和外层的 this 是一样的
- 箭头函数是不能提升的，所以需要在使用之前定义

~~~
const add = (x, y) => x + y; // add 为一个函数，执行的功能是 x+y
~~~

#### 闭包

闭包函数：声明在一个函数中的函数 ，能够读取父函数内部的变量，是父子函数连接的通道

闭包特点：

- 函数嵌套函数，内嵌函数对函数中的局部变量进行访问
- 局部变量会常驻在内存中：在函数内定义有共享意义的局部变量
- 让外部访问函数内部变量成为可能：函数向外返回此内嵌函数，外部可通过此内嵌函数访问声明在函数中的局部变量，而此变量在外部是通过其他路径无法访问的 

闭包缺点：

- 会造成内存泄漏（有一块内存空间被长期占用，而不被释放
  - 可以在退出函数之前，将不使用的局部变量全部删除
- 闭包可以在父函数外部改变父函数内部的值 (某些情况下这是优点

闭包使用场景：

- setTimeout循环
- 模拟私有方法
- 实现类和继承
- 匿名自执行函数
- 结果要缓存场景

**闭包找到的是同一地址中父级函数中对应变量最终的值**

```js
function fa() { 
  var i = 0; 
  function son() { alert(++i); } 
  return b; 
} 
var fn1 = fa();   //每次外部函数执行的时候,都会开辟一块内存空间,重新创建一个新的地址 -->外部函数的地址不同
fn1();      //1 
fn1();      //2 

var fn2 = fa(); // i 重新被声明 为0
fn2();      //1 
fn2();      //2 
```



#### 回调函数

回调函数：把函数当作一个参数传到另外一个函数中，在需要时调用

特点：

- 不会立即执行，通过 函数名() 调用
- 回调函数是一个闭包 --> 它能访问到父函数定义的变量

回调函数的 this  指向问题：指向是 **离它最近的或者嵌套级别的 function**的调用者

- 解决回调函数this指向的方法：

  - 把 **箭头函数** 当回调函数，然后作为参数传入另外的函数中

    

    



## 浅拷贝与深拷贝

浅拷贝：只复制了对象的一层属性,如果数据是基本数据类型，会拷贝其本身，如果数据是对象类型，那么只能拷贝其引用，对象的改变会反应到拷贝对象上。改变浅拷贝后对象的基本类型值，原对象的基本类型值不会被改变。改变浅拷贝后对象的引用类型值，原对象的基本类型值就会被改变

- 数组自带的浅拷贝方法：**slice()、concat、Array.from()、...** 

- 对象自带的浅拷贝方法： **...  、Object.assign()**

  ~~~
  var obj = {
      name:'lisi',
      like:['$','sleep']
  }
  var obj1 = {};
  Object.assign(obj1,obj);
  obj1.name = 'zs'; // obj.name 不改变
  obj1.like[1] = 'play'; // obj1.like[1] 改变
  ~~~

  

深拷贝：对象的改变不会影响拷贝对象，深拷贝会递归复制所有层级，会对对象以及对象的所有子对象进行拷贝。所以改变赋值后对象的基本类型值和引用类型值，原对象的基本类型值和引用类型值都不会被改变

- 深拷贝会带来性能上的问题
- 通过  $.extend()  ：将一个或多个对象的内容合并到目标对象
- 利用  JSON.stringify()  将 JS 对象序列化成 JSON 字符串，再使用  JSON.parse() 来反序列化还原 JS 对象(如果对象中定义了方法，JSON 无法复制方法)

~~~js
$.extend( deep, target, object1,…objectN )；
// deep：是否深度合并对象，默认为 false
// target：目标对象，其他对象的成员属性将被附加到该对象上
// object1：被合并的对象
var obj2 = {};
$.extend(true, obj2, obj);
obj2.like[1] = 'play'; // obj1.like[1] 不改变
var obj3 = JSON.parse(JSON.stringify(obj));
obj3.like[1] = 'play'; // obj3.like[1] 不改变
~~~

- JSON.parse(JSON.stringify()) 的缺陷：
  - 时间对象将变成字符串的形式
  -  RegExp、Error 对象得到空对象
  - 函数、undefined丢失
  -  NaN、Infinity 和 -Infinity -->null



## 定时器

在 JavaScript 中，我们可以利用定时器来延迟执行某些代码，或者以固定的时间间隔重复执行某些代码

如果时间到了之后还有其他的事情正在处理中，不管定时器的时间有没有到，都是不会执行定时器的

- setTimeout() 延迟定时器 ：可以在规定时间（以毫秒计）过后执行一次代码块
- setInterval() 循环定时器（‘间隔器’）：按照指定的周期（以毫秒计）来重复执行某些代码

~~~js
setTimeout(function(),millisec , cllback,...)
setInterval(function(),millisec , cllback,...)
// function() 必需。要调用的函数后要执行的 函数
// millisec ,在执行代码前需等待的毫秒数 , 没有会立即执行
// 第三个之后的所有参数，都将是第一个参数函数执行的实参
~~~

定时器清除：每一个定时器开启后，都会返回一个对应的id，通过这个id就可以清除定时器

- 这要求我们在开启定时器的同时定义一个变量接受定时器返回的id，用于清除定时器

~~~js
clearTimeout(timer)   //  清除setTimeout
clearInterval(timer)  //  清除setInterval
~~~

定时器是异步执行的：

- 当js遇到定时器，它会认为你特别耗时间，不管你写的时间间隔是多久，都会将匿名函数放到callback queue回调队列中，等待主线程的调用，当主线程的stack所有的任务都执行完了，再通过event loop（时间循环）把匿名函数放到stack主线程中运行

~~~
function log1 () { console.log(1) } ;
function log2 () { console.log(2) } ;
function log3 () { console.log(3) } ;

log1() ;
setTimeout(log1,4000) ;
setInterval(log2,1000) ;
setTimeout(log3,2000)
console.log(4)

// 1 ,2 -->主线程执行
// setTimeout(log1,4000) 开始计时，进入任务队列
// setInterval(log2,1000) 开始计时，进入任务队列
// setTimeout(log3,2000) 开始计时，进入任务队列
// 2 --> 1s
// 2,3 --2s
// 2 --> 3s
// 1,2 -->4s
// 2..... --> time++
~~~

#### 使用定时器的防抖节流

页面抖动：由于有些函数的执行频率（滚动监听）和用户操作不规范（多个用户频繁触发一个事件，例如双十一抢购），会增加服务器压力以及降低网页性能

防抖即是在一定时间内减少函数的执行频率，来达到性能优化的目的

延迟型：延迟结束那一刻才触发回调

```js
function debounce(fn) { // fn函数为你具体想要执行的操作
            let t = null  // 只在事件绑定时触发一次
            return function () {  // 使用return --> 每次触发事件，执行的是debounce的回调
                if (t) {
                    clearTimeout(t)
                }
 
                t = setTimeout(() => { // 箭头函数得到的是 return 里的 arguments
                    fn.apply(this, arguments) //拿到return里的this和arguments传给changeColor                    
                }, 1000)
            }
        }
```

前缘型：

```
function debounce(fn) {
            let t = null
            return function () {
                // 用firstClick来记录每一次定时器开始的第一次按下的动作
                var firstClick = !t
 
                if(t) {
                    clearTimeout(t)
                }
 
                if(firstClick) {
                    fn.apply(this, arguments)
                }
 
                //等待1秒后将 t 置为 null，在这1秒内如果再点击事件就会去到if(t)的执行
                t = setTimeout(() => {
                    t = null
                }, 1000)
            }
        }
```

节流：让函数在执行一次之后的某个时间段失效，过了这段时间后再进行激活

```js
  function throttle(fun,time){
            var valid = true;
            return function(){
                if (!valid){
                    return false;
                }
                valid = false;
                setTimeout(function(){
                    fun();
                    valid = true;
                },time)
            }
        }
————————————————
版权声明：本文为CSDN博主「清风不渡」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/WXKKang/article/details/126366894
```



## Promise







































































































































































































































