# 第二部分：JavaScript

## 1、介绍一下JS的内置类型有哪些？

基本数据类型：Undefined Null Boolean Number String

内置对象：Object是Javascript中所有对象的父对象

​        数据封装对象：Object Array Boolean  Number String

​        其他对象：Function Argument Math Date RegExp Error

## 2、介绍一下 typeof 区分类型的原理

instanceof的实现代码:



```
// L instanceof R
function instance_of(L, R) {//L 表示左表达式，R 表示右表达式
    var O = R.prototype;// 取 R 的显式原型
    L = L.__proto__;    // 取 L 的隐式原型
    while (true) { 
        if (L === null) //已经找到顶层
            return false;  
        if (O === L)   //当 O 严格等于 L 时，返回 true
            return true; 
        L = L.__proto__;  //继续向上一层原型链查找
    } 
}
```



首先typeof 能够判断基本数据类型，但是除了null，typeof null 返回的是object

但是对于对象来说typeof不能准确判断类型，typeof 函数会返回function，除此之外全部都是object，不能准确判断类型

instanceof可以判断复杂数据类型，基本数据类型不可以

instanceof是通过原型链来判断的 ，A instanceof B，在A的原型链中层层查找，是否有原型等于B.prototype，如果一直找到A的原型链的顶端（null，即Object.prototype._proto_），仍然不等于B，那么返回false，否则返回true

## 3、JavaScript 中的强制转型是指什么？

在 JavaScript 中，两种不同的内置类型间的转换被称为强制转型。强制转型在 JavaScript 中有两种形式：显式和隐式。

这是一个显式强制转型的例子：



```javascript
var a = "42";
var b = Number( a );
a; // "42"
b; // 42 -- 是个数字!
```

这是一个隐式强制转型的例子：



```javascript
var a = "42";
var b = a * 1; // "42" 隐式转型成 42 
a; // "42"
b; // 42 -- 是个数字!
```



 

## 4、说说你对javascript的作用域的理解

前言

学习 JavaScript 也有一段时间，今天抽空总结一下作用域，也方便自己以后翻阅。

什么是作用域

如果让我用一句简短的话来讲述什么是作用域，我的回答是：

> 其实作用域的本质是一套规则，它定义了变量的可访问范围，控制变量的可见性和生命周期。

既然作用域是一套规则，那么究竟如何设置这些规则呢？

先不急，在这之前，我们先来理解几个概念。

编译到执行的过程

下面我们就拿这段代码来讲述 JavaScript 编译到执行的过程。

```js
var a = 2;
```

首先我们来看一下在这个过程中，几个功臣所需要做的事。

1. 引擎（总指挥）：

从头到尾负责整个 JavaScript 程序的编译及执行过程。

1. 编译器（劳工）：
2. 词法分析（分词）
   解析成词法单元，`var`、`a`、`=`、`2`。
3. 语法分析（解析）
   将单词单元转换成抽象语法树（AST）。
4. 代码生成
   将抽象语法树转换成机器指令。
5. 作用域（仓库管理员）：

负责收集并维护所有生命的标识符（变量）组成的一系列查询，并实施一套非常严格的规则，确定当前执行的代码对这些标识符的访问权限。

然后我们再来看，执行这段代码时，每个功臣是怎么协同工作的。

引擎：

> 其实这段代码有两个完全不同的声明，`var a`和`a = 2`，一个由编译器在编译时处理，另一个则由引擎在运行时处理。

编译器：

1. 一套编译器常规操作下来，到代码生成步骤。
2. 遇到`var a`，会先询问作用域中是否已经存在同名变量，如果是，则忽略该声明，继续进行编译；否则它会要求作用域声明一个新的变量`a`。
3. 为引擎生成运行`a = 2`时所需的代码。

引擎：

> 会先询问作用域是否存在变量`a`，如果是，就会使用这个变量进行赋值操作；否则一直往外层嵌套作用域找（详见作用域嵌套），直至到全局作用域都没有时，抛出一个异常。

**总结：**变量的赋值操作会执行两个动作， 首先编译器会在当前作用域中声明一个变量（ 如果之前没有声明过），然后在运行时引擎会在作用域中查找该变量， 如果能够找到就会对它赋值。

LHS & RHS 查询

从上面可知，引擎在获得编译器给出的代码后，还会对作用域进行询问变量。

聪明的你肯定一眼就看出，`L`和`R`的含义，它们分别代表左侧和右侧。

现在我们把代码改成这样：

```js
var a = b;
```

这时引擎对`a`进行 LHS 查询，对`b`进行 RHS 查询，但是`L`和`R`并不一定指操作符的左右边，而应该这样理解：

> LHS 是为了找到赋值的目标。 RHS 是赋值操作的源头。也就是 LHS 是为了找到变量这个容器本身，给它赋值，而 RHS 是为了取出这个变量的值。

作用域嵌套

当一个块或函数嵌套在另一个块或函数中时，就发生了作用域的嵌套，进而形成了一条作用域链。因此，在当前作用域中无法找到某个变量时，引擎就会在外层嵌套的作用域中继续查找，直到找到该变量， 或抵达最外层的作用域(也就是全局作用域)为止。

词法作用域

作用域分为两种：

1. 词法作用域（较为普遍，JavaScript所使用的也是这种）
2. 动态作用域（使用较少，比如 Bash 脚本、Perl 中的一些模式等）

> 词法作用域是由你在写代码时将变量和块作用域写在哪里来决定的。

看以下代码，这个例子中有三个逐级嵌套的作用域。

```js
var a = 2; // 作用域1 全局
function foo(){ 
    var b = a * 2; // 作用域2 局部
    function bar(){
        var c = a * b; // 作用域3 局部
    }
}
```

1. 作用域是由你书写代码所在位置决定的。
2. 子级作用域可以访问父级作用域，而父级作用域则不能访问子级作用域。

引擎对作用域的查找

作用域查找会在找到第一个匹配的标识符时停止，在多层的嵌套作用域中可以定义同名的标识符，这叫做“遮蔽效应”（内部的标识符“遮蔽”了外部的标识符）。也就是说查找时会从运行所在的作用域开始，逐级往上查找，直到遇见第一个标识符为止。

全局变量（全局作用域下定义的变量）会自动变成全局对象（比如浏览器中的 window对象）。

```js
var a = 1;
function foo(){
    var a = 2;
    console.log(a); // 2
    function bar(){
        var a = 3;
        console.log(a); // 3
        console.log(window.a); // 1
    }
}
```

> 非全局的变量如果被遮蔽了，就无论如何都无法被访问到，所以在上述代码中，`bar`内的作用域无法访问到`foo`下定义的变量`a`。
> 词法作用域查找只会查找一级标识符，比如`a`、`b`，如果是`foo.bar`，词法作用域查找只会试图查找`foo`标识符，找到这个变量后，由对象属性访问规则接管属性的访问。

欺骗语法

虽然词法作用域是在代码编写时确定的，但还是有方法可以在引擎运行时动态修改词法作用域，有两种机制：

1. `eval`
2. `with`

eval

JavaScript 的 `eval`函数可以接受一个字符串参数并作为代码语句来执行， 就好像代码是原本就在那个位置一样，考虑以下代码：

```js
function foo(str){
    eval(str) // 欺骗
    console.log(a);
}
var a = 1;
foo("var a = 2;"); // 2
```

仿佛`eval`中传入的参数语句原本就在那一样，会创建一个变量`a`，并遮蔽了外部作用域的同名变量。

**注意**：

- `eval`通常被用来执行动态创建的代码，可以根据程序逻辑动态地将变量和函数以字符串形式拼接在一起之后传递进去。
- 在严格模式下，`eval`无法修改所在的作用域。
- 与`eval`相似的还有，`setTimeout`、`setInterval`、`new Function`。

with

`with`通常被当作重复引用同一个对象中的多个属性的快捷方式， 可以不需要重复引用对象本身。

使用方法如下：

```js
var obj1 = { a:1,b:2 };
function foo(obj){
    with(obj){
        a = 2;
        b = 3;
    }
}
foo(obj1);
console.log(obj1); // {a: 2, b: 3}
```

然而考虑以下代码：

```js
var obj2 = { a:1,b:2 };
function foo(obj){
    with(obj){
        a = 2;
        b = 3;
        c = 4;
    }
}
foo(obj2);
console.log(obj2); // {a: 2, b: 3}
console.log(c); // 4 不好，c被泄露到全局作用域下
```

尽管`with`可以将对象处理为词法作用域，但是这样块内部正常的`var`操作并不会限制在这个块的作用域下，而是被添加到`with`所在的函数作用域下，而不通过`var`声明变量将视为声明全局变量。

性能

`eval`和`with`会在运行时修改或创建新的作用域，以此来欺骗其他书写时定义的词法作用域，然而 JavaScript 引擎会在编译阶段进行性能优化，有些优化依赖于能够根据代码的词法进行静态分析，并预先确定所有的变量和函数的定义位置，才能在执行过程中快速找到标识符。但是通过`eval`和`with`来欺骗词法作用域会导致引擎无法知道他们对词法作用域做了什么样的改动，只能对部分不进行优化，因此如果在代码中大量使用`eval`或`with`就会导致代码运行起来变得非常慢。

函数作用域和块作用域

函数作用域

在 JavaScript 中每声明一个函数就会创建一个函数作用域，同时属于这个函数的所有变量在整个函数的范围内都可以使用。

块作用域

从 ES3 发布以来，JavaScript 就有了块作用域，创建块作用域的几种方式有：

- `with`

上面已经讲了，这里不再复述。

- `try/catch`

`try/catch` 的 `catch` 分句会创建一个块作用域，其中声明的变量仅在 catch 内部有效。

```
javascript try{ throw 2; }catch(a){ console.log(a); }
```

- `let`和`const`

ES6 引入的新关键词，提供了除 `var` 以外的变量声明方式，它们可以将变量绑定到所在的任意作用域中（通常是`{}`内部）。

```
javascript { let a = 2; } console.log(a); // ReferenceError: a is not defined
```

**注意**：使用 `let`和`const` 进行的声明不会在块作用域中进行提升。

提升

考虑这段代码：

```js
console.log( a ); 
var a = 2;
```

输入结果是`undefined`，而不是`ReferenceError`。

为什么呢？

前面说过，编译阶段时，会把声明分成两个动作，也就是只把`var a`部分进行提升。

所以第二段代码真正的执行顺序是：

```js
var a; // 这时 a 是 undefined
console.log(a);
a = 2;
```

- 编译阶段时会把所有的声明操作提升，而赋值操作原地执行。
- 函数声明会把整个函数提升，而不仅仅是函数名。

函数优先

虽然函数和变量都会被提升，但函数声明的优先级高于变量声明，所以：

```js
foo(); // 1
var foo;
function foo(){
    console.log(1);
}
foo = function(){
    console.log(2);
}
```

因为这个代码片段会被引擎理解为如下形式：

```js
function foo(){
    console.log(1);
}
foo(); // 1
foo = function() { 
  console.log( 2 );
};
```

这个值得一提的是，尽管`var foo`出现在`function foo()...`之前，但由于函数声明会被优先提升，所以它会被忽略（因为重复声明了）。 **注意：**

> JavaScript 会忽略前面已经声明过的声明，不管它是变量还是函数，只要其名称相同。

后记

## 5、什么是作用域链

在了解作用域链之前，先来了解下什么是作用域

> 作用域（scope），通常来说，一段程序代码中所用到的名字并不总是有效/可用的，而限定这个名字的可用性的代码范围就是这个名字的作用域。

js是没有块级作用域的，也就是说外面可以使用｛｝里面的变量，包括do while/for中的｛｝。

例如

```js
for ( var i=0; i<10; i++ ) {
    var a=3
}
console.log(a)//3
```

但函数内定义的变量函数外是不可以使用的，例如console.log(a),不能使用fn()里的var a=1,没有声明变量，就会报错。

```js
function fn() {
   var a=1;
}
console.log(a);//"ReferenceError:a is not defined"
```

但函数里面的却可以使用外面的变量，这就是引申出出了作用域链。

作用域链：

1. 函数在执行的过程中，先从自己内部找变量，
2. 如果找不到，再从**创建当前函数所在的作用域**去找, 以此往上，
3. 注意找的是变量的当前的状态。

例如

```js
var a = 1
function fn1(){
  function fn2(){
    console.log(a)
  }
  function fn3(){
    var a = 4
    fn2()
  }
  var a = 2
  return fn3
}
var fn = fn1()
fn() //输出2
```

解析：var fn = fn1(),执行函数fn1(),因为return fn3，再执行函数fn3(),再执行函数fn2(),再执行console.log(a),那么a是多少呢？fn2里没有变量a，去fn2的上一级（声明fn2的地方）fn1里去找，找到了 var a=2, 那么console.log(2)，输出了2。

## 6、解释下 let 和 const 的块级作用域

在ES6之前，我们都是用 var 关键字声明变量。无论声明在何处，都会被视为声明在函数的最顶部(不在函数内即在全局作用域的最顶部)。这就是函数变量提升 例如：

```javascript
	  function aa() {
    if(flag) {
        var test = 'hello man'
    } else {
        console.log(test)
    }
  }

12345678
```

变量声明后代码实际上是：

```javascript
  function aa() {
    var test // 变量提升，函数最顶部
    if(flag) {
        test = 'hello man'
    } else {
        //此处访问 test 值为 undefined
        console.log(test)
    }
    //此处访问 test 值为 undefined
  }
12345678910
```

所以不用关心 flag 是否为 true or false。实际上，无论如何 test 都会被创建声明。

接下来ES6主角登场：
我们通常用 let 和 const 来声明，let 表示变量、const 表示常量。let 和 const 都是块级作用域。怎么理解这个块级作用域？

在一个函数内部
在一个代码块内部
只要在 {}花括号内 的代码块即可以认为 let 和 const 的作用域。

```javascript
  function aa() {
    if(flag) {
       let test = 'hello man'
    } else {
        //test 在此处访问不到
        console.log(test)
    }
  }
12345678
```

et 的作用域是在它所在当前代码块，但不会被提升到当前函数的最顶部。

再来说说 const
const 声明的变量必须提供一个值，而且会被认为是常量，意思就是它的值被设置完成后就不能再修改了。

```javascript
    const name = 'cc'
    name = 'yy' // 再次赋值此时会报错
12
```

还有，如果 const 的是一个对象，对象所包含的值是可以被修改的。抽象一点儿说，就是对象所指向的地址不能改变，而变量成员是可以修改的。

看以下例子就非常清楚：

```javascript
    const student = { name: 'cc' }
    
    student.name = 'yy' // 修改变量成员，一点儿毛病没有
    
    student  = { name: 'yy' } // 修改变量绑定，这样子就会报错了
12345
```

`let`和`const`命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。

总之，在代码块内，使用`let`命令声明变量之前，该变量都是不可用的。这在语法上，称为“`暂时性死区`”（temporal dead zone，简称TDZ）。

```javascript
if (true) {
  // TDZ开始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError
 
  let tmp; // TDZ结束
  console.log(tmp); // undefined
 
  tmp = 123;
  console.log(tmp); // 123
 
  typeof x; // ReferenceError
  let x;
 
  typeof undeclared_variable // "undefined"

1234567891011121314151617
```

上面代码中，在let命令声明变量tmp，x之前，都属于变量tmp,x的“死区”。只要用到该变量就会报错.

undeclared_variable是一个不存在的变量名，结果返回“undefined”。

```javascript
function bar(x = y, y = 2) {
  return [x, y];
}
 
bar(); // 报错

123456
```

上面代码中，调用bar函数之所以报错（某些实现可能不报错），是因为参数x默认值等于另一个参数y，而此时y还没有声明，属于”死区“。如果y的默认值是x，就不会报错，因为此时x已经声明了。

```javascript
function bar(x = 2, y = x) {
  return [x, y];
}
bar(); // [2, 2]
```

## 7、**什么是JavaScript？（这是基本题，对很多程序员来说也是送分题！）**

JavaScript是客户端和服务器端脚本语言，可以插入到HTML页面中，并且是目前较热门的Web开发语言。同时，JavaScript也是面向对象编程语言。

 

## 8、对闭包的看法，为什么要用闭包？说一下闭包的原理以及应用场景

一、什么是闭包

闭包就是能够读取其他函数内部变量的函数。例如在javascript中，只有函数内部的子函数才能读取局部变量，所以闭包可以理解成“定义在一个函数内部的函数“。在本质上，闭包是将函数内部和函数外部连接起来的桥梁。

函数执行后返回结果是一个内部函数，并被外部变量所引用，如果内部函数持有被执行函数作用域的变量，即形成了闭包。

可以在内部函数访问到外部函数作用域。使用闭包，一可以读取函数作用域中的变量，二可以将函数中的变量存储到内存中，保护变量不被污染。而正因闭包会把函数中的变量值存储到内存中，会对内存有消耗，所以不能滥用闭包，否则会影响网页性能，造成内存泄露。当不需要使用闭包时，要及时释放内存，可将内存函数对象的的变量赋值为 null。

二、闭包原理

函数执行分为两个阶段（预编译阶段和执行阶段）。

- 在预编译阶段，如果发现内部函数使用了外部函数的变量，则会在内存中创建一个“闭包”对象并保持对应变量值，如果已存在“闭包”，则只需要增加对应的属性值即可。
- 执行完后，函数执行上下文会被销毁，函数对“闭包”对象的引用也会销毁，但其内部函数还持有对该“闭包”的引用，所以内部函数还可以继续使用“外部函数”中的变量。

利用函数作用域链的特性，一个函数内部定义的函数会将包含外部函数的活动对象添加到他的作用域链中，函数执行完毕，其执行作用域链被销毁，但其因内部函数的作用域链仍然在引用这个活动对象，所以其活动对象不会被销毁，直到内部函数被销毁后才销毁。

三、优点

1.可以从内部函数访问外部函数的作用域中的变量，且访问到的变量长期驻扎在内存中，可供之后使用

2.避免变量污染全局变量

3.把变量存到独立的作用域中，作为私有成员存在

四、缺点

1.对内存消耗有负面影响。因内部函数保存了对外部函数变量的引用，导致无法被垃圾回收，增大内存使用量，所以使用不当会造成内存泄露

2.对处理速度有负面影响。闭包的层级决定了引用的外部变量在查找时经过的作用域链长度

3.可能获取到意外的值

五、应用场景

场景一：典型的应用是模块封装，在各模块规范出来之前，都是用这样的方式阻止变量污染全局。

```javascript
var fn = (function(){



    //这样声明为模块的私有变量，外界无法直接访问



    var foo = 1;



    function fn(){};



    fn.prototype.bar = function(){



        return foo;



    }



    return fn;



}());
```

场景二：在循环中创建闭包，防止取到意外的值。如下，无论哪个元素触发事件，都会弹出3。因为函数执行后引用 i 是同一个，而 i 在循环结束后就是 3

```javascript
for(var i=0;i<3;i++){



    document.getElementById('id'+i).onclick = function(){



        alert(i);



    };



}



 



//可用闭包解决



function makeCallbak(num){



    return function(){



        alert(num);



    };



}



for(var i=0;i<3;i++){



     document.getElementById('id'+i).onclick = makeCallbal(i);



}
```

 

## 9、**列举Java和JavaScript之间的区别？**

Java是一门十分完整、成熟的编程语言。相比之下，JavaScript是一个可以被引入HTML页面的编程语言。这两种语言并不完全相互依赖，而是针对不同的意图而设计的。 Java是一种面向对象编程（OOPS）或结构化编程语言，类似的如C ++或C，而JavaScript是客户端脚本语言，它被称为非结构化编程。

## 10、如何确定this指向 

如果用一句话说明 this 的指向，那么即是: 谁调用它，this 就指向谁。

但是仅通过这句话，我们很多时候并不能准确判断 this 的指向。因此我们需要借助一些规则去帮助自己：

this 的指向可以按照以下顺序判断:

全局环境中的 this

浏览器环境：无论是否在严格模式下，在全局执行环境中（在任何函数体外部）this 都指向全局对象 `window`;

node 环境：无论是否在严格模式下，在全局执行环境中（在任何函数体外部），this 都是空对象 `{}`;

是否是 `new` 绑定

如果是 `new` 绑定，并且构造函数中没有返回 function 或者是 object，那么 this 指向这个新对象。如下:

> 构造函数返回值不是 function 或 object。`new Super()` 返回的是 this 对象。

 

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOS82LzI2LzE2YjhmOTRhYjI2NjIwZWQ_aW1hZ2VWaWV3Mi8wL3cvMTI4MC9oLzk2MC9mb3JtYXQvd2VicC9pZ25vcmUtZXJyb3IvMQ)

 

 

> 构造函数返回值是 function 或 object，`new Super()`是返回的是Super种返回的对象。

 

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOS82LzI2LzE2YjhmOTY4MmEzY2I2NWM_aW1hZ2VWaWV3Mi8wL3cvMTI4MC9oLzk2MC9mb3JtYXQvd2VicC9pZ25vcmUtZXJyb3IvMQ)

 

 

函数是否通过 call,apply 调用，或者使用了 bind 绑定，如果是，那么this绑定的就是指定的对象【归结为显式绑定】。

 

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOS82LzI2LzE2YjhmOTgyN2U5OWIwMzI_aW1hZ2VWaWV3Mi8wL3cvMTI4MC9oLzk2MC9mb3JtYXQvd2VicC9pZ25vcmUtZXJyb3IvMQ)

 

 

这里同样需要注意一种**特殊**情况，如果 call,apply 或者 bind 传入的第一个参数值是 `undefined` 或者 `null`，严格模式下 this 的值为传入的值 null /undefined。非严格模式下，实际应用的默认绑定规则，this 指向全局对象(node环境为global，浏览器环境为window)

 

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOS82LzI2LzE2YjhmOTliZDZhMzU2MmE_aW1hZ2VWaWV3Mi8wL3cvMTI4MC9oLzk2MC9mb3JtYXQvd2VicC9pZ25vcmUtZXJyb3IvMQ)

 

 

隐式绑定，函数的调用是在某个对象上触发的，即调用位置上存在上下文对象。典型的隐式调用为: `xxx.fn()`

 

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOS82LzI2LzE2YjhmOWQ0OGUyMDYzNTQ_aW1hZ2VWaWV3Mi8wL3cvMTI4MC9oLzk2MC9mb3JtYXQvd2VicC9pZ25vcmUtZXJyb3IvMQ)

 

 

默认绑定，在不能应用其它绑定规则时使用的默认规则，通常是独立函数调用。

非严格模式： node环境，执行全局对象 global，浏览器环境，执行全局对象 window。

严格模式：执行 undefined

 

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOS82LzI2LzE2YjhmOWY1MmU5NGRmNGE_aW1hZ2VWaWV3Mi8wL3cvMTI4MC9oLzk2MC9mb3JtYXQvd2VicC9pZ25vcmUtZXJyb3IvMQ)

 

 

箭头函数的情况：

箭头函数没有自己的this，继承外层上下文绑定的this。

 

## 11、改变this指向的方式有哪些？

call、apply、bind三者为改变this指向的方法。

共同点：第一个参数都为改变this的指针。若第一参数为null/undefined，this默认指向window

call（无数个参数）

- 第一个参数：改变this指向
- 第二个参数：实参
- 使用之后会自动执行该函数

```
function fn(a,b,c){
        console.log(this,a+b+c); // this指向window
    }
    fn();
    fn.call(document,1,2,3);//call改变之后this指向document  
    //输出 #document 6   1,2,3是实参 结果相加为6
```

apply（两个参数）

- 第一个参数：改变this指向
- 第二个参数：数组（里面为实参）
- 使用时候会自动执行函数

```
function fn(a,b,c){
        console.log(this,a+b+c); 
    }
    fn();
    fn.apply(document,[1,2,3]); 
```

bind（无数个参数）

- 第一个参数：改变this指向
- 第二个参数之后：实参
- 返回值为一个新的函数
- 使用的时候需要手动调用下返回 的新函数（不会自动执行）

```
function fn(a,b,c){
    console.log(this,a+b+c); //window
}
let ff = fn.bind('小明',1,2,3); //手动调用一下
```

call、apply与bind区别：前两个可以自动执行，bind不会自动执行，需要手动调用

call、bind与apply区别：前两个都有无数个参数，apply只有两个参数，而且第二个参数为数组

 

## 12、 箭头函数的this 

1.普通函数的this：指向它的调用者，如果没有调用者则默认指向window.
2.箭头函数的this: 指向箭头函数定义时所处的对象，而不是箭头函数使用时所在的对象，默认使用父级的this.

## 13、谈一下你对原型链的理解，画一个经典的原型链图示

![img](https://pic3.zhimg.com/v2-b86ade7ff43bdf1b05f4c5dc8704f634_1440w.jpg?source=172ae18b)



具体参考 这篇文章  



https://note.youdao.com/ynoteshare1/index.html?id=0c454a4f45787988f5b2e6f7043f70e7&type=note





## 14、举例说明JS如何实现继承 

js继承总共分成5种，包括构造函数式继承、原型链式继承、组合式继承、寄生式继承和寄生组合式继承。

**构造函数式继承**

首先来看第一种，构造函数式继承，顾名思义，也就是利用函数去实现继承；

假设我们现在有一个父类函数：

```js
// 父类构造函数
function Parent(color) {
    this.color = color;
    this.print = function() {
        console.log(this.color);
    }
}
```

现在要编写一个子类函数来继承这个父类，如下:

```js
// 子类构造函数
function Son(color) {
    Parent.call(this, color);
}
```

上面代码可以看到，子类Son是通过Parent.call的方式去调用父类构造函数，然后把this对象传进去，执行父类构造函数之后，子类Son就拥有了父类定义的color和print方法。调用一下该方法，输出如下：

```js
// 测试
var son1 = new Son('red');
son1.print(); // red

var son2 = new Son('blue');
son2.print(); // blue
```

可以看到son1和son2都正常继承了父类的print方法和各自传进去的color属性；

以上就是构造函数式继承的实现了，这是最原始的js实现继承的方式；

但是当我们深入想一下会发现，**这种根本就不是传统意义上的继承！**

因为每一个Son子类调用父类生成的对象，都是各自独立的，也就是说，如果父类希望有一个公共的属性是所有子类实例共享的话，是没办法实现的。什么意思呢，来看下面的代码：

```js
function Flower() {
    this.colors = ['黄色', '红色'];
    this.print = function () {
        console.log(this.colors)
    }
}

function Rose() {
    Flower.call(this);
}

var r1 = new Rose();
var r2 = new Rose();

console.log(r1.print()); // [ '黄色', '红色' ]
console.log(r2.print()); // [ '黄色', '红色' ]
```

我们现在有一个基类Flower，它有一个属性colors，现在我们把某一个实例的colors值改一下：

```js
r1.colors.push('紫色');

console.log(r1.print()); // [ '黄色', '红色', '紫色' ]
console.log(r2.print()); // [ '黄色', '红色' ]
```

结果如上，显然，改变的只有r1的值，因为通过构造函数创造出来的实例对象中，所有的属性和方法都是实例内部独立的，并不会跟其他实例共享。
总结一下构造函数的优缺点：

- 优点：所有的**基本属性**独立，不会被其他实例所影响；
- 缺点：所有**希望共享的方法和属性**也独立了，没有办法通过修改父类某一处来达到所有子实例同时更新的效果；同时，每次创建子类都会调用父类构造函数一次，所以每个**子实例都拷贝了一份父类函数的内容**，如果父类很大的话会影响性能；

**原型链继承**

下面我们来看第二种继承方式，原型链式继承；

同样先来看下例子：

```js
function Parent() {
    this.color = 'red';
    this.print = function() {
        console.log(this.color);
    }
}
function Son() {
}
```

我们有一个父类和一个空的子类；

```js
Son.prototype = new Parent();
Son.prototype.constructor = Son;
```

接着我们**把子函数的原型属性赋值给了父函数的实例；**

```js
var son1 = new Son();
son1.print(); // red
```

最后新建子类实例，调用父类的方法，成功拿到父类的color和print属性方法；

我们重点来分析一下下面两行代码：

```js
Son.prototype = new Parent();
Son.prototype.constructor = Son;
```

这段代码中，子函数的原型赋给了父函数的实例，我们知道prototype是函数中的一个属性，js的一个特性就是：**如果一个对象某个属性找不到，会沿着它的原型往上去寻找，直到原型链的最后才会停止寻找。**关于原型更多基础的知识，可以参考一下其他文章，或许以后我也会出一期专门讲解原型和原型链的文章。

回到代码，我们看到最后实例son成功调用了Print方法，输出了color属性，这是因为son从函数Son的prototype属性上面去找到的，也就是从new Parent这个对象里面找到的；

这种方式也不是真正的继承，因为所有的子实例的属性和方法，都在父类同一个实例上了，所以一旦某一个子实例修改了其中的方法，其他所有的子实例都会被影响，来看下代码：

```js
function Flower() {
    this.colors = ['黄色', '红色'];
    this.print = function () {
        console.log(this.colors)
    }
}

function Rose() {}
Rose.prototype = new Flower();
Rose.prototype.constructor = Rose;

var r1 = new Rose();
var r2 = new Rose();

console.log(r1.print()); // [ '黄色', '红色' ]
console.log(r1.print()); // [ '黄色', '红色' ]

r1.colors.push('紫色');

console.log(r1.print()); // [ '黄色', '红色', '紫色' ]
console.log(r2.print()); // [ '黄色', '红色', '紫色' ]
```

还是刚才的例子，这次Rose子类选择了原型链继承，所以，子实例r1修改了colors之后，r2实例的colors也被改动了，这就是原型链继承不好的地方。

来总结下原型链继承的优缺点：

- 优点：很好的实现了方法的共享；
- 缺点：正是因为什么都共享了，所以导致一切的属性都是共享的，只要某一个实例进行修改，那么所有的属性都会变化；

**组合式继承** 

这里来介绍第三种继承方式，组合式继承；

这种继承方式很好理解，既然构造函数式继承和原型链继承都有各自的优缺点，那么我们把它们各自的优点整合起来，不就完美了吗？



组合式继承做的就是这个事情~来看一段代码例子：

```js
function Parent(color) {
    this.color = color;
}
Parent.prototype.print = function() {
    console.log(this.color);
}
function Son(color) {
    Parent.call(this, color);
}
Son.prototype = new Parent();
Son.prototype.constructor = Son;

var son1 = new Son('red');
son1.print(); // red

var son2 = new Son('blue');
son2.print(); // blue
```

上面代码中，在Son子类中，使用了Parent.call来调用父类构造函数，同时又将Son.prototype赋给了父类实例；为什么要这样做呢？为什么这样就能解决上面两种继承的问题呢？
我们接着分析一下，使用Parent.call调用了父类构造函数之后，那么，以后所有通过new Son创建出来的实例，就单独拷贝了一份**父类构造函数里面定义的属性和方法**，这是前面构造函数继承所提到的一样的原理；

然后，再把子类原型prototype赋值给父类的实例，这样，**所有子类的实例对象就可以共享父类原型上定义的所有属性和方法。**这也不难理解，因为子实例会沿着原型链去找到父类函数的原型。

因此，只要我们定义父类函数的时候，**将私有属性和方法放在构造函数里面，将共享属性和方法放在原型上，**就能让子类使用了。

以上就是组合式继承，它很好的融合了构造函数继承和原型链继承，发挥两者的优势之处，因此，它算是真正意义上的继承方式。

**寄生式继承** 

既然上面的组合式继承都已经这么完美了，为什么还需要其他的继承方式呢？
我们细想一下，Son.prototype = new Parent();这行代码，它有什么问题没有？

显然，每次我们实例化子类的时候，都需要调用一次父类构造函数，那么，如果父类构造函数是一个很大很长的函数，那么每次实例化子类就会执行很长时间。

实际上我们并不需要重新执行父类函数，我们只是想要继承父类的原型。

寄生式继承就是在做这个事情，它是基于原型链式继承的改良版：

```js
var obj = {
    color: 'red',
    print: function() {
        console.log(this.color);
    }
};

var son1 = Object.create(obj);
son1.print(); // red

var son2 = Object.create(obj);
son2.print(); // red
```

寄生式继承本质上还是原型链继承，Object.create(obj);方法意思是以obj为原型构造对象，所以寄生式继承不需要构造函数，但是同样有着原型链继承的优缺点，也就是它把所有的属性和方法都共享了。

**寄生组合式继承**

接下来到我们最后一个继承方式，也就是目前业界最为完美的继承解决方案：寄生组合式继承。

**没错，它就是es6的class语法实现原理。**
但是如果你理解了组合式继承，那么理解这个方式也很简单，只要记住，它出现的主要目的，是为了解决组合式继承中每次都需要new Parent导致的执行多一次父类构造函数的缺点。

下面来看代码：

```js
function Parent(color) {
    this.color = color;
}
Parent.prototype.print = function() {
    console.log(this.color);
}
function Son(color) {
    Parent.call(this, color);
}
Son.prototype = Object.create(Parent.prototype);
Son.prototype.constructor = Son;

var son1 = new Son('red');
son1.print(); // red

var son2 = new Son('blue');
son2.print(); // blue
```

这段代码不同之处只有一个，就是把原来的Son.prototype = new Parent();修改为了Son.prototype = Object.create(Parent.prototype);

我们前面讲过，Object.create方法是以传入的对象为原型，创建一个新对象；创建了这个新对象之后，又赋值给了Son.prototype，因此Son的原型最终指向的其实就是父类的原型对象，和new Parent是一样的效果；

到这里，我们5中js的继承方式也就讲完了；



## 15、**什么是负无穷大？**

负无穷大是JavaScript中的一个数字，可以通过将负数除以零来得到。

## 16、你对事件循环有了解吗？说说看！

![img](https://pic2.zhimg.com/v2-0b35a3df0b2e2712839ce551062e6d7f_1440w.jpg?source=172ae18b)





我们都知道，javascript从诞生之日起就是一门单线程的非阻塞的脚本语言。这是由其最初的用途来决定的：与浏览器交互。

单线程意味着，javascript代码在执行的任何时候，都只有一个主线程来处理所有的任务。

而非阻塞则是当代码需要进行一项异步任务（无法立刻返回结果，需要花一定时间才能返回的任务，如I/O事件）的时候，主线程会挂起（pending）这个任务，然后在异步任务返回结果的时候再根据一定规则去执行相应的回调。

单线程是必要的，也是javascript这门语言的基石，原因之一在其最初也是最主要的执行环境——浏览器中，我们需要进行各种各样的dom操作。试想一下 如果javascript是多线程的，那么当两个线程同时对dom进行一项操作，例如一个向其添加事件，而另一个删除了这个dom，此时该如何处理呢？因此，为了保证不会 发生类似于这个例子中的情景，javascript选择只用一个主线程来执行代码，这样就保证了程序执行的一致性。

当然，现如今人们也意识到，单线程在保证了执行顺序的同时也限制了javascript的效率，因此开发出了web worker技术。这项技术号称让javascript成为一门多线程语言。

然而，使用web worker技术开的多线程有着诸多限制，例如：所有新线程都受主线程的完全控制，不能独立执行。这意味着这些“线程” 实际上应属于主线程的子线程。另外，这些子线程并没有执行I/O操作的权限，只能为主线程分担一些诸如计算等任务。所以严格来讲这些线程并没有完整的功能，也因此这项技术并非改变了javascript语言的单线程本质。

可以预见，未来的javascript也会一直是一门单线程的语言。

话说回来，前面提到javascript的另一个特点是“非阻塞”，那么javascript引擎到底是如何实现的这一点呢？答案就是今天这篇文章的主角——event loop（事件循环）。

*注：虽然nodejs中的也存在与传统浏览器环境下的相似的事件循环。然而两者间却有着诸多不同，故把两者分开，单独解释。*

***正文\***

**浏览器环境下js引擎的事件循环机制**

**1.执行栈与事件队列**

当javascript代码执行的时候会将不同的变量存于内存中的不同位置：堆（heap）和栈（stack）中来加以区分。其中，堆里存放着一些对象。而栈中则存放着一些基础类型变量以及对象的指针。 但是我们这里说的执行栈和上面这个栈的意义却有些不同。

我们知道，当我们调用一个方法的时候，js会生成一个与这个方法对应的执行环境（context），又叫执行上下文。这个执行环境中存在着这个方法的私有作用域，上层作用域的指向，方法的参数，这个作用域中定义的变量以及这个作用域的this对象。 而当一系列方法被依次调用的时候，因为js是单线程的，同一时间只能执行一个方法，于是这些方法被排队在一个单独的地方。这个地方被称为执行栈。

当一个脚本第一次执行的时候，js引擎会解析这段代码，并将其中的同步代码按照执行顺序加入执行栈中，然后从头开始执行。如果当前执行的是一个方法，那么js会向执行栈中添加这个方法的执行环境，然后进入这个执行环境继续执行其中的代码。当这个执行环境中的代码 执行完毕并返回结果后，js会退出这个执行环境并把这个执行环境销毁，回到上一个方法的执行环境。。这个过程反复进行，直到执行栈中的代码全部执行完毕。





![img](https://pic4.zhimg.com/80/v2-da078fa3eadf3db4bf455904ae06f84b_720w.jpg)







**2.macro task与micro task**

以上的事件循环过程是一个宏观的表述，实际上因为异步任务之间并不相同，因此他们的执行优先级也有区别。不同的异步任务被分为两类：微任务（micro task）和宏任务（macro task）。

以下事件属于宏任务：

- `setInterval()`
- `setTimeout()`

以下事件属于微任务

- `new Promise()`
- `new MutaionObserver()`

前面我们介绍过，在一个事件循环中，异步事件返回结果后会被放到一个任务队列中。然而，根据这个异步事件的类型，这个事件实际上会被对应的宏任务队列或者微任务队列中去。并且在当前执行栈为空的时候，主线程会 查看微任务队列是否有事件存在。如果不存在，那么再去宏任务队列中取出一个事件并把对应的回到加入当前执行栈；如果存在，则会依次执行队列中事件对应的回调，直到微任务队列为空，然后去宏任务队列中取出最前面的一个事件，把对应的回调加入当前执行栈...如此反复，进入循环。

我们只需记住**当当前执行栈执行完毕时会立刻先处理所有微任务队列中的事件，然后再去宏任务队列中取出一个事件。同一次事件循环中，微任务永远在宏任务之前执行**。

这样就能解释下面这段代码的结果：

```text
setTimeout(function () {
    console.log(1);
});

new Promise(function(resolve,reject){
    console.log(2)
    resolve(3)
}).then(function(val){
    console.log(val);
})
```

结果为：

```text
2
3
1
 
```

**node环境下的事件循环机制**

**1.与浏览器环境有何不同?**

在node中，事件循环表现出的状态与浏览器中大致相同。不同的是node中有一套自己的模型。node中事件循环的实现是依靠的libuv引擎。我们知道node选择chrome v8引擎作为js解释器，v8引擎将js代码分析后去调用对应的node api，而这些api最后则由libuv引擎驱动，执行对应的任务，并把不同的事件放在不同的队列中等待主线程执行。 因此实际上node中的事件循环存在于libuv引擎中。

**2.事件循环模型**

下面是一个libuv引擎中的事件循环的模型:

```text
 ┌───────────────────────┐
┌─>│        timers         │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
│  │     I/O callbacks     │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
│  │     idle, prepare     │
│  └──────────┬────────────┘      ┌───────────────┐
│  ┌──────────┴────────────┐      │   incoming:   │
│  │         poll          │<──connections───     │
│  └──────────┬────────────┘      │   data, etc.  │
│  ┌──────────┴────────────┐      └───────────────┘
│  │        check          │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
└──┤    close callbacks    │
   └───────────────────────┘
```

*注：模型中的每一个方块代表事件循环的一个阶段*

这个模型是node官网上的一篇文章中给出的，我下面的解释也都来源于这篇文章。我会在文末把文章地址贴出来，有兴趣的朋友可以亲自与看看原文。

**3.事件循环各阶段详解**

从上面这个模型中，我们可以大致分析出node中的事件循环的顺序：

外部输入数据-->轮询阶段(poll)-->检查阶段(check)-->关闭事件回调阶段(close callback)-->定时器检测阶段(timer)-->I/O事件回调阶段(I/O callbacks)-->闲置阶段(idle, prepare)-->轮询阶段...

以上各阶段的名称是根据我个人理解的翻译，为了避免错误和歧义，下面解释的时候会用英文来表示这些阶段。

这些阶段大致的功能如下：

- timers: 这个阶段执行定时器队列中的回调如 `setTimeout()` 和 `setInterval()`。
- I/O callbacks: 这个阶段执行几乎所有的回调。但是不包括close事件，定时器和`setImmediate()`的回调。
- idle, prepare: 这个阶段仅在内部使用，可以不必理会。
- poll: 等待新的I/O事件，node在一些特殊情况下会阻塞在这里。
- check: `setImmediate()`的回调会在这个阶段执行。
- close callbacks: 例如`socket.on('close', ...)`这种close事件的回调。

下面我们来按照代码第一次进入libuv引擎后的顺序来详细解说这些阶段：

**poll阶段**

当个v8引擎将js代码解析后传入libuv引擎后，循环首先进入poll阶段。poll阶段的执行逻辑如下： 先查看poll queue中是否有事件，有任务就按先进先出的顺序依次执行回调。 当queue为空时，会检查是否有setImmediate()的callback，如果有就进入check阶段执行这些callback。但同时也会检查是否有到期的timer，如果有，就把这些到期的timer的callback按照调用顺序放到timer queue中，之后循环会进入timer阶段执行queue中的 callback。 这两者的顺序是不固定的，收到代码运行的环境的影响。如果两者的queue都是空的，那么loop会在poll阶段停留，直到有一个i/o事件返回，循环会进入i/o callback阶段并立即执行这个事件的callback。

值得注意的是，poll阶段在执行poll queue中的回调时实际上不会无限的执行下去。有两种情况poll阶段会终止执行poll queue中的下一个回调：1.所有回调执行完毕。2.执行数超过了node的限制。

**check阶段**

check阶段专门用来执行`setImmediate()`方法的回调，当poll阶段进入空闲状态，并且setImmediate queue中有callback时，事件循环进入这个阶段。

**close阶段**

当一个socket连接或者一个handle被突然关闭时（例如调用了`socket.destroy()`方法），close事件会被发送到这个阶段执行回调。否则事件会用`process.nextTick（）`方法发送出去。

**timer阶段**

这个阶段以先进先出的方式执行所有到期的timer加入timer队列里的callback，一个timer callback指得是一个通过setTimeout或者setInterval函数设置的回调函数。

**I/O callback阶段**

如上文所言，这个阶段主要执行大部分I/O事件的回调，包括一些为操作系统执行的回调。例如一个TCP连接生错误时，系统需要执行回调来获得这个错误的报告。

**4.process.nextTick,setTimeout与setImmediate的区别与使用场景**

在node中有三个常用的用来推迟任务执行的方法：process.nextTick,setTimeout（setInterval与之相同）与setImmediate

这三者间存在着一些非常不同的区别：

**process.nextTick()**

尽管没有提及，但是实际上node中存在着一个特殊的队列，即nextTick queue。这个队列中的回调执行虽然没有被表示为一个阶段，当时这些事件却会在每一个阶段执行完毕准备进入下一个阶段时优先执行。当事件循环准备进入下一个阶段之前，会先检查nextTick queue中是否有任务，如果有，那么会先清空这个队列。与执行poll queue中的任务不同的是，这个操作在队列清空前是不会停止的。这也就意味着，错误的使用`process.nextTick()`方法会导致node进入一个死循环。。直到内存泄漏。

那么合适使用这个方法比较合适呢？下面有一个例子：

```text
const server = net.createServer(() => {}).listen(8080);

server.on('listening', () => {});
```

这个例子中当，当listen方法被调用时，除非端口被占用，否则会立刻绑定在对应的端口上。这意味着此时这个端口可以立刻触发listening事件并执行其回调。然而，这时候`on('listening)`还没有将callback设置好，自然没有callback可以执行。为了避免出现这种情况，node会在listen事件中使用`process.nextTick()`方法，确保事件在回调函数绑定后被触发。

**setTimeout()和setImmediate()**

在三个方法中，这两个方法最容易被弄混。实际上，某些情况下这两个方法的表现也非常相似。然而实际上，这两个方法的意义却大为不同。

`setTimeout()`方法是定义一个回调，并且希望这个回调在我们所指定的时间间隔后第一时间去执行。注意这个“第一时间执行”，这意味着，受到操作系统和当前执行任务的诸多影响，该回调并不会在我们预期的时间间隔后精准的执行。执行的时间存在一定的延迟和误差，这是不可避免的。node会在可以执行timer回调的第一时间去执行你所设定的任务。

`setImmediate()`方法从意义上将是立刻执行的意思，但是实际上它却是在一个固定的阶段才会执行回调，即poll阶段之后。有趣的是，这个名字的意义和之前提到过的`process.nextTick()`方法才是最匹配的。node的开发者们也清楚这两个方法的命名上存在一定的混淆，他们表示不会把这两个方法的名字调换过来---因为有大量的node程序使用着这两个方法，调换命名所带来的好处与它的影响相比不值一提。

`setTimeout()`和不设置时间间隔的`setImmediate()`表现上及其相似。猜猜下面这段代码的结果是什么？

```text
setTimeout(() => {
    console.log('timeout');
}, 0);

setImmediate(() => {
    console.log('immediate');
});
```

实际上，答案是不一定。没错，就连node的开发者都无法准确的判断这两者的顺序谁前谁后。这取决于这段代码的运行环境。运行环境中的各种复杂的情况会导致在同步队列里两个方法的顺序随机决定。但是，在一种情况下可以准确判断两个方法回调的执行顺序，那就是在一个I/O事件的回调中。下面这段代码的顺序永远是固定的：

```text
const fs = require('fs');

fs.readFile(__filename, () => {
    setTimeout(() => {
        console.log('timeout');
    }, 0);
    setImmediate(() => {
        console.log('immediate');
    });
});
```

答案永远是：

```text
immediate
timeout
```

因为在I/O事件的回调中，setImmediate方法的回调永远在timer的回调前执行。

***尾声\***

javascrit的事件循环是这门语言中非常重要且基础的概念。清楚的了解了事件循环的执行顺序和每一个阶段的特点，可以使我们对一段异步代码的执行顺序有一个清晰的认识，从而减少代码运行的不确定性。合理的使用各种延迟事件的方法，有助于代码更好的按照其优先级去执行。这篇文章期望用最易理解的方式和语言准确描述事件循环这个复杂过程，但由于作者自己水平有限，文章中难免出现疏漏。如果您发现了文章中的一些问题，欢迎在留言中提出，我会尽量回复这些评论，把错误更正。



## 17、微任务和宏任务有什么区别

宏任务

(macro)task，可以理解是每次执行栈执行的代码就是一个宏任务（包括每次从事件队列中获取一个事件回调并放到执行栈中执行）。

浏览器为了能够使得JS内部(macro)task与DOM任务能够有序的执行，会在一个(macro)task执行结束后，在下一个(macro)task 执行开始前，对页面进行重新渲染，流程如下：

```text
(macro)task->渲染->(macro)task->...
```

宏任务包含：

```text
script(整体代码)
setTimeout
setInterval
I/O
UI交互事件
postMessage
MessageChannel
setImmediate(Node.js 环境)
```

微任务

microtask,可以理解是在当前 task 执行结束后立即执行的任务。也就是说，在当前task任务后，下一个task之前，在渲染之前。

所以它的响应速度相比setTimeout（setTimeout是task）会更快，因为无需等渲染。也就是说，在某一个macrotask执行完后，就会将在它执行期间产生的所有microtask都执行完毕（在渲染前）。

微任务包含：

```text
Promise.then
Object.observe
MutaionObserver
process.nextTick(Node.js 环境)
```

##  18、**什么是===运算符？**

***\*===被称为严格等式运算符，当两个操作数具有相同的值而没有任何类型转换时，该运算符返回true。\****


##  19、异步解决方案有哪些？

promise    回调  async await 

## 20、Promise, 说说你的理解 

一、Promise是什么？

Promise是最早由社区提出和实现的一种解决异步编程的方案，比其他传统的解决方案（回调函数和事件）更合理和更强大。

ES6 将其写进了语言标准，统一了用法，原生提供了Promise对象。
 ES6 规定，Promise对象是一个构造函数，用来生成Promise实例。

二、Promise是为解决什么问题而产生的？

promise是为解决异步处理回调金字塔问题而产生的

三、Promise的两个特点

1、Promise对象的状态不受外界影响

1）pending 初始状态

2）fulfilled 成功状态

3）rejected 失败状态

Promise 有以上三种状态，只有异步操作的结果可以决定当前是哪一种状态，其他任何操作都无法改变这个状态

2、Promise的状态一旦改变，就不会再变，任何时候都可以得到这个结果，状态不可以逆，只能由 pending变成fulfilled或者由pending变成rejected

四、Promise的三个缺点

1）无法取消Promise,一旦新建它就会立即执行，无法中途取消
 2）如果不设置回调函数，Promise内部抛出的错误，不会反映到外部
 3）当处于pending状态时，无法得知目前进展到哪一个阶段，是刚刚开始还是即将完成

五、Promise在哪存放成功回调序列和失败回调序列？

1）onResolvedCallbacks 成功后要执行的回调序列 是一个数组

2）onRejectedCallbacks 失败后要执行的回调序列 是一个数组

以上两个数组存放在Promise 创建实例时给Promise这个类传的函数中，默认都是空数组。
 每次实例then的时候 传入 onFulfilled 成功回调 onRejected 失败回调，如果此时的状态是pending 则将onFulfilled和onRejected push到对应的成功回调序列数组和失败回调序列数组中，如果此时的状态是fulfilled 则onFulfilled立即执行，如果此时的状态是rejected则onRejected立即执行

上述序列中的回调函数执行的时候 是有顺序的，即按照顺序依次执行

六、Promise的用法

1、Promise构造函数接受一个函数作为参数，该函数的两个参数分别是resolve和reject。它们是两个函数，由 JavaScript 引擎提供，不用自己部署。



```jsx
    const promise = new Promise(function(resolve, reject) {
      // ... some code

      if (/* 异步操作成功 */){
        resolve(value);
      } else {
        reject(error);
      }
    });
```

2、resolve函数的作用是，将Promise对象的状态从“未完成”变为“成功”（即从 pending 变为 resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；reject函数的作用是，将Promise对象的状态从“未完成”变为“失败”（即从 pending 变为 rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

3、Promise实例生成以后，可以用then方法分别指定resolved状态和rejected状态的回调函数。



```jsx
    promise.then(function(value) {
      // success
    }, function(error) {
      // failure
    });
```

then方法可以接受两个回调函数作为参数。第一个回调函数是Promise对象的状态变为resolved时调用，第二个回调函数是Promise对象的状态变为rejected时调用。其中，第二个函数是可选的，不一定要提供。这两个函数都接受Promise对象传出的值作为参数。

 

## 21、**说明如何使用JavaScript提交表单？**

要使用JavaScript提交表单，请使用

document.forms [0] .submit（）;

## 22、aync await的好处

`async`和`await`可以说是异步终极解决方案了，相比直接使用`Promise`来说，优势在于处理`then`的调用链，能够更清晰准确的写出代码，毕竟写一大堆`then`也很恶心，并且也能优雅地解决回调地狱问题。当然也存在一些缺点，因为`await`将异步代码改造成了同步代码，如果多个异步代码没有依赖性却使用了`await`会导致性能上的降低

##  23、移动端点击事件300ms延迟如何去掉？原因是什么？

300毫秒原因：

当用户第一次点击屏幕后，需要判断用户是否要进行双击操作，于是手机会等待300毫秒，

解决方案： faskclick.js

原理：

在检测到touchend事件的时候，会通过DOM自定义事件立即出发模拟一个click事件，并把浏览器在300ms之后真正的click事件阻止掉

```js
25.function Foo(){
    getName=function(){
         Console.log(1)
    }
    return this;
}
Foo.geteName=function(){console.log(2)}
Foo.prototype.geteName=function(){console.log(3)}
var geteName=function(){console.log(4)}
function getName(){console.log(5)}
//输出结果
Foo.geteName()//2
geteName()//4
Foo().geteName()//1
geteName()//1
new Foo.geteName()//2
new Foo().geteName()//3
new new Foo().geteName()//3
```

## 24、Cookie有哪些属性？其中HttpOnly，Secure，Expire分别有什么作用？ 

Cookie总是保存在客户端中，按在客户端中的存储位置，可分为内存Cookie和硬盘Cookie。内存Cookie由浏览器维护，保存在内存中，浏览器关闭后就消失了，其存在时间是短暂的。硬盘Cookie保存在硬盘里，有一个过期时间，除非用户手工清理或到了过期时间，硬盘Cookie不会被删除，其存在时间是长期的。所以，按存在时间，可分为非持久Cookie和持久Cookie。

HTTP请求+cookie的交互流程

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/20190123104207786.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5nbGYwMg==,size_16,color_FFFFFF,t_70)

如果步骤5携带的是过期的cookie或者是错误的cookie，那么将认证失败，返回至要求身份认证页面。

**HTTP协议作为无状态协议，对于HTTP协议而言，无状态同样指每次request请求之前是相互独立的，当前请求并不会记录它的上一次请求信息。那么问题来了，既然无状态，那完成一套完整的业务逻辑，发送多次请求的情况数不胜数，使用http如何将上下文请求进行关联呢？机智的人类通过优化，找到了一种简单的方式记录http协议的请求信息。**

优化后的HTTP请求：

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/20190123110201440.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5nbGYwMg==,size_16,color_FFFFFF,t_70)

1. 浏览器发送request请求到服务器，服务器除了返回请求的response之外，还给请求分配一个唯一标识ID，协同response一并返回给浏览器。
2. 同时服务器在本地创建一个MAP结构，专门以key-value（请求ID-会话内容）形式将每个request进行存储
   此时浏览器的request已经被赋予了一个ID，第二次访问时，服务器先从request中查找该ID，根据ID查找维护会话的content内容，该内容中记录了上一次request的信息状态。
3. 根据查找出的request信息生成基于这些信息的response内容，再次返回给浏览器。如果有需要会再次更新会话内容，为下一次请求提供准备。

所以根据这个会话ID，以建立多次请求-响应模式的关联数据传递。说到这里可能已经唤起了大家许多共鸣。这就是cookie和session对无状态的http协议的强大作用。服务端生成这个全局的唯一标识，传递给客户端用于唯一标记这次请求，也就是cookie；而服务器创建的那个map结构就是session。所以，cookies由服务端生成，用于标记客户端的唯一标识，无特定含义，在每次网络请求中，都会被传送。session服务端自己维护的一个map数据结构，记录key-content上下文内容状态。

cookie的属性

一般cookie具有7个属性，包括：

**Name**：就是cookieName，一般用字母或数字，不能包含特殊字符，没什么好说的。

**value**：cookieName对应的值。

**Domain**：域，表示当前cookie所属于哪个域或子域下面，例如.baidu.com就表示在.baidu.com下可以访问。

对于服务器返回的Set-Cookie中，如果没有指定Domain的值，那么其Domain的值是默认为当前所提交的http的请求所对应的主域名的。比如访问 [http://www.example.com](http://www.example.com/)，返回一个cookie，没有指名domain值，[那么其为值为默认的www.example.com](http://xn--www-c88da6fy7pz3cm18kef0b2qler5b.example.com/)。

**Path**：表示cookie的所属路径，一般设为“/”，表示同一个站点的所有页面都可以访问这个cookie。

**Expires/Max-age**：表示了cookie的有效期。expires的值，是一个GMT格式的时间，过了这个时间，该cookie就失效了。或者是用max-age指定当前cookie是在多长时间之后而失效。如果服务器返回的一个cookie，没有指定其expire time，那么表明此cookie有效期只是当前的session，即是session cookie，当前session会话结束后，就过期了。对应的，当关闭（浏览器中）该页面的时候，此cookie就应该被浏览器所删除了。

**secure**：表示该cookie只能用https传输。一般用于包含认证信息的cookie，要求传输此cookie的时候，必须用https传输。

**httponly**：表示此cookie必须用于http或https传输。这意味着，浏览器脚本，比如javascript中，是不允许访问操作此cookie的。

如果你用的是谷歌浏览器，此时可以右击鼠标，选择检查，选择Application标签页，左侧找到cookies点击就可以看到这几个属性了

## 25、 JavaScript中的循环结构都有什么？* 

For、While、do-while loops

## 26、**说明“==”和“===”之间的区别？**

“==”仅检查值相等，而“===”是一个更严格的等式判定，如果两个变量的值或类型不同，则返回false。

## 27、**3 + 2 +“7”的结果是什么？**

由于3和2是整数，它们将直接相加。由于7是一个字符串，它将会被直接连接，所以结果将是57。

## 28、**说明如何检测客户端机器上的操作系统？**

为了检测客户端机器上的操作系统，应使用navigator.appVersion字符串（属性）

## 29、将1234567转换为1,234,567

```
//法一  
function parseNum(num){  
    var list = new String(num).split('').reverse();  
    for(var i = 0; i < list.length; i++){  
        if(i % 4 == 3){  
            list.splice(i, 0, ',');  
        }  
    }  
    return list.reverse().join('');  
}  
  
console.log(parseNum(10000121213));  
  
  
//法二  
function parseNum(num){  
    var reg=/(?=(?!\b)(\d{3})+$)/g;  
    return String(num).replace(reg, ',');  
}  
console.log(parseNum(10000121213));  
  
  
//法三  
String.prototype.strReverse = function(){  
    return this.split('').reverse().join('');  
}  
  
function parseNum(num){  
    var str_num = String(num);  
    var len = str_num.length;  
    var tail = str_num.slice(0, len % 3);  
    tail = tail.strReverse();  
    var reg=/\d{3}/g;  
    var list = str_num.strReverse().match(reg);  
    list.push(tail);  
    var res = list.join(',').strReverse();  
    return res;  
}  
console.log(parseNum(10000121213));  
  
  
//法四  
function parseNum(num){  
    var list = String(num).split('').reverse();  
    var temp = [];  
    for(var i = 0, len = list.length; i < len; i = i + 3){  
        temp.push(list.slice(i, i + 3).join(''));  
    }  
    return temp.join(',').split('').reverse().join('');  
}  
console.log(parseNum(10000121213));  
```



## 30、**Javascript中的NULL是什么意思？**

NULL用于表示无值或无对象。它意味着没有对象或空字符串，没有有效的布尔值，没有数值和数组对象。

## 31、**delete操作符的功能是什么？**

**delete操作符用于删除程序中的所有变量或对象，但不能删除使用VAR关键字声明的变量。**

## 32、**JavaScript中有哪些类型的弹出框？**

Alert、Confirm and、Prompt

## 33、document load和documen ready 的区别

页面加载完成有两种事件

1.load是当页面所有资源全部加载完成后（包括DOM文档树，css文件，js文件，图片资源等），执行一个函数

问题：如果图片资源较多，加载时间较长，onload后等待执行的函数需要等待较长时间，所以一些效果可能受到影响

2.$(document).ready()是当DOM文档树加载完成后执行一个函数 （不包含图片，css等）所以会比load较快执行

在原生的jS中不包括ready()这个方法，只有load方法就是onload事件

## 34、如何自定义事件

1. 事件的创建

JS中，最简单的创建事件方法，是使用Event构造器：

```
var myEvent = new Event('event_name');
```

但是为了能够传递数据，就需要使用 CustomEvent 构造器：

```
var myEvent = new CustomEvent('event_name', {
    detail:{
        // 将需要传递的数据写在detail中，以便在EventListener中获取
        // 数据将会在event.detail中得到
    },
});
```

2. 事件的监听

JS的EventListener是根据事件的名称来进行监听的，比如我们在上文中已经创建了一个名称为**‘event_name’** 的事件，那么当某个元素需要监听它的时候，就需要创建相应的监听器：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//假设listener注册在window对象上
window.addEventListener('event_name', function(event){
    // 如果是CustomEvent，传入的数据在event.detail中
    console.log('得到数据为：', event.detail);

    // ...后续相关操作
});
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

至此，window对象上就有了对**‘event_name’** 这个事件的监听器，当window上触发这个事件的时候，相关的callback就会执行。

3. 事件的触发

对于一些内置（built-in）的事件，通常都是有一些操作去做触发，比如鼠标单击对应MouseEvent的click事件，利用鼠标（ctrl+滚轮上下）去放大缩小页面对应WheelEvent的resize事件。
然而，自定义的事件由于不是JS内置的事件，所以我们需要在JS代码中去显式地触发它。方法是使用 **dispatchEvent** 去触发（IE8低版本兼容，使用fireEvent）：



```
// 首先需要提前定义好事件，并且注册相关的EventListener
var myEvent = new CustomEvent('event_name', { 
    detail: { title: 'This is title!'},
});
window.addEventListener('event_name', function(event){
    console.log('得到标题为：', event.detail.title);
});
// 随后在对应的元素上触发该事件
if(window.dispatchEvent) {  
    window.dispatchEvent(myEvent);
} else {
    window.fireEvent(myEvent);
}
// 根据listener中的callback函数定义，应当会在console中输出 "得到标题为： This is title!"
```



需要特别注意的是，当一个事件触发的时候，如果相应的element及其上级元素没有对应的EventListener，就不会有任何回调操作。 
对于子元素的监听，可以对父元素添加事件托管，让事件在事件冒泡阶段被监听器捕获并执行。这时候，使用event.target就可以获取到具体触发事件的元素。

## 35、用setTImeout 来实现setInterval

```
复制代码
function interval(func, w, t){
    var interv = function(){
        if(typeof t === "undefined" || t-- > 0){
            setTimeout(interv, w);
            try{
                func.call(null);
            }
            catch(e){
                t = 0;
                throw e.toString();
            }
        }
    };

    setTimeout(interv, w);
};
复制代码
这个interval函数有一个叫做inter的内部函数，它通过setTimeout来自动被调用，在inter中有一个闭包，它检查了重复次数，调用了回调函数并通过setTimeout再次调用了interv。万一回调函数中出现了一个异常，interv调用将会终止，异常也会被抛出。

这种木事当然不能保证函数在固定的间隔中执行，但是它保证新的区间开始时上一个区间中的函数已经执行完毕，我认为这是非常重要的。

用法
现在我们可以在10秒的区间内执行一段代码10次，代码如下：

interval(function(){
    //执行的代码在这
},1000,10);
 
```



## 36、**什么是JavaScript Cookie？**

Cookie是用来存储计算机中的小型测试文件，当用户访问网站以存储他们需要的信息时，它将被创建。

## 37、**在JavaScript中使用innerHTML的缺点是什么？**

如果在JavaScript中使用innerHTML，缺点是：内容随处可见；不能像“追加到innerHTML”一样使用；即使你使用+ = like“innerHTML = innerHTML +'html'”旧的内容仍然会被html替换；整个innerHTML内容被重新解析并构建成元素，因此它的速度要慢得多；innerHTML不提供验证，因此我们可能会在文档中插入有效的和破坏性的HTML并将其中断。

## 38、**如何创建通用对象？**
通用对象可以创建为：

var I = new object();


## 39、**在JavaScript中使用的Push方法是什么？**

push方法用于将一个或多个元素添加或附加到数组的末尾。使用这种方法，可以通过传递多个参数来附加多个元素。

## 40、如何避免回调地狱

promise   async await

## 41、构造函数Fn，原型对象，实例对象，三者之间的关系

每创建一个函数，该函数都会自动带有一个prototype属性。该属性是一个指针，指向一个对象，该对象称之为原型对象(后期我们可以使用这个原型对象帮助我们在js中实现继承).

原型对象上默认有一个属性constructor,该属性也是一个指针，指向其相关联的构造函数。

通过调用构造函数产生的实例对象，都拥有一个内部属性，指向了原型对象。其实例对象能够访问原型对象上的所有属性和方法。

总结：三者的关系是，每个构造函数都有一个原型对象，原型对象上包含着一个指向构造函数的指针，而实例都包含着一个指向原型对象的内部指针。通俗的说，实例可以通过内部指针访问到原型对象，原型对象可以通过constructor找到构造函数。

实例：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
function People(){
    this.type='人'
}
People.prototype.showType=function(){
    alert(this.type);
}

var person=new People();
//调用原型对象上面的方法
person.showType();//最后结果弹框弹出人
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　以上代码定义了一个构造函数People(),People.prototype指向原型对象，其自带属性construtor又指回了People，即People.prototype.constructor==People.实例对象person由于其内部指针指向了原型对象，所以可以访问原型对象上的showType方法。

　　![img](https://images2018.cnblogs.com/blog/1375391/201804/1375391-20180413141148575-907366970.png)

记住People.prototype只是一个指针，指向的是原型对象，利用这个指针可以帮助我们实现js继承

2.原型链

　　在第一部分我们说到，所有的实例都有一个内部指针指向他的原型对象，并且可以访问到原型对象上的所有属性和方法。person实例对象指向了People的原型对象，可以访问People原型对象上的所有属性和方法。如果People原型对象变成了某一个类的实例aaa,这个实例又会指向一个新的原型对象AAA,那么person此时能访问aaa的实例属性和AAA原型对象上的所有属性和方法了。同理新的原型对象AAA碰巧有事另外一个对象的实例bbb,这个对象实例指向原型对象BBB，那么person就能访问bbb的实例属性和BBB原型上的属性和方法了。

![img](https://images2018.cnblogs.com/blog/1375391/201804/1375391-20180413144407775-1722807681.png)

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
function People(){
    this.type='人'
}
People.prototype.showType=function(){
   　alert(this.type);
}
function Woman(){
    this.sex='女';
    this.age=34;
}
Woman.prototype=new People();
var w=new Woman();console.log('大家好,我的种类是:'+w.type+",我的年龄是:"+w.age+",我的性别是:"+w.sex);//输出结果://大家好，我的种类是：人，我的年龄是:34，我的性格是:女//w.type是People上面定义的type
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

解释一下以上代码.以上代码，首先先定义了People构造函数，通过new People()得到实例，会包含一个实例对象type和一个原型属性showType。另外定义一个Woman构造函数，然后情况发生变化，本来构造函数woman的prototype会执行Woman的原型对象，但是我们这里稍有改变，**将Woman构造函数的prototype指向了People实例对象覆盖了woman的原型对象**。当Woman的实例对象woman去访问type属性时，js首先在woman实例属性中查找，发现没有定义，接着去woman的原型对象上找，woman的原型对象这里已经被我们改成了People实例，那就是去People实例上去找。先找People的实例属性，发现没有type，最后去People的原型对象上去找，终于找到了。这个查找就是这么一级一级的往上查找。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
function People(){
    this.type='人'
}
People.prototype.showType=function(){
   　alert(this.type);
}
function Woman(){
    this.sex='女';
    this.age=34;    this.type='女生';//如果这里定义了type属性，就不会层级查找，最后在People找到该属性
}
Woman.prototype=new People();
var w=new Woman();
console.log('大家好,我的种类是:'+w.type+",我的年龄是:"+w.age+",我的性别是:"+w.sex);
//输出结果:
//大家好，我的种类是：女生，我的年龄是:34，我的性格是:女
```



​    这就说明，我们可以通过原型链的方式，实现 Woman继承 People 的所有属性和方法。

　　总结来说：就是当重写了Woman.prototype指向的原型对象后，实例的内部指针也发生了改变，指向了新的原型对象，然后就能实现类与类之间的继承了。

## 42、对MVC，MVVM的理解

1、MVC

View 传送指令到 Controller

Controller 完成业务逻辑后，要求 Model 改变状态

Model 将新的数据发送到 View，用户得到反馈

所有的通信都是单向的。

![img](https://img-blog.csdn.net/20170917230010642?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWljaGFlbDg1MTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



2、MVVM

View：UI界面  

ViewModel：它是View的抽象，负责View与Model之间信息转换，将View的Command传送到Model；  

Model：数据访问层

![img](https://img-blog.csdn.net/20170917230054510?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWljaGFlbDg1MTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

## 43、**JavaScript中不同类型的错误有几种？**

有三种类型的错误：

- Load time errors：该错误发生于加载网页时，例如出现语法错误等状况，称为加载时间错误，并且会动态生成错误。
- Run time errors：由于在HTML语言中滥用命令而导致的错误。
- Logical Errors：这是由于在具有不同操作的函数上执行了错误逻辑而发生的错误。

## 44、 使用正则表达式验证邮箱格式。

一.相关的代码

 1 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif) function test()
 2 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)    {
 3 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)      var temp = document.getElementById("text1");
 4 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)      //对电子邮件的验证
 5 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)      var myreg = /^([a-zA-Z0-9]+[_|\_|\.]?)*[a-zA-Z0-9]+@([a-zA-Z0-9]+[_|\_|\.]?)*[a-zA-Z0-9]+\.[a-zA-Z]{2,3}$/;
 6 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)      if(!myreg.test(temp.value))
 7 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)      {
 8 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)        alert('提示\n\n请输入有效的E_mail！');
 9 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)        myreg.focus();
10 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)        return false;
11 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)      }
12 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)    }
13 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)    //由于方法相同，一下只写出相关的正则表达式
14 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)    //对于手机号码的验证（提供了两种方法）
15 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)    var mobile=/^((13[0-9]{1})|159|153)+\d{8}$/;
16 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)    var mobile1=/^(13+\d{9})|(159+\d{8})|(153+\d{8})$/;
17 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)    //对于区号的验证
18 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)    var phoneAreaNum = /^\d{3,4}$/;
19 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)    //对于电话号码的验证
20 ![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)    var phone =/^\d{7,8}$/;


二.解释相关的意义
   \1. /^$/ 这个是个通用的格式。
     ^ 匹配输入字符串的开始位置；$匹配输入字符串的结束位置
   \2. 里面输入需要实现的功能。
    \* 匹配前面的子表达式零次或多次；
    \+ 匹配前面的子表达式一次或多次；
    ？匹配前面的子表达式零次或一次；
    \d 匹配一个数字字符，等价于[0-9]

## 45、简述同步和异步的区别

同步：

同步的思想是：所有的操作都做完，才返回给用户。这样用户在线等待的时间太长，给用户一种卡死了的感觉（就是系统迁移中，点击了迁移，界面就不动了，但是程序还在执行，卡死了的感觉）。这种情况下，用户不能关闭界面，如果关闭了，即迁移程序就中断了。

异步：

将用户请求放入消息队列，并反馈给用户，系统迁移程序已经启动，你可以关闭浏览器了。然后程序再慢慢地去写入数据库去。这就是异步。但是用户没有卡死的感觉，会告诉你，你的请求系统已经响应了。你可以关闭界面了。

 

同步和异步本身是相对的

 

同步就相当于是 当客户端发送请求给服务端，在等待服务端响应的请求时，客户端不做其他的事情。当服务端做完了才返回到客户端。这样的话客户端需要一直等待。用户使用起来会有不友好。

异步就是，当客户端发送给服务端请求时，在等待服务端响应的时候，客户端可以做其他的事情，这样节约了时间，提高了效率。

存在就有其道理 异步虽然好 但是有些问题是要用同步用来解决，比如有些东西我们需要的是拿到返回的数据在进行操作的。这些是异步所无法解决的。

## 46、**对象属性如何分配？**

属性按以下方式分配给对象：

obj["class"] = 12;

或

obj.class = 12;

## 47、**获得CheckBox状态的方式是什么？**

alert（document.getElementById（'checkbox1'）。checked）;

如果CheckBox被检查，此警报将返回TRUE。

## 48、**解释window.onload和onDocumentReady？**

在载入页面的所有信息之前，不运行onload函数。这导致在执行任何代码之前会出现延迟。

onDocumentReady在加载DOM之后加载代码。这允许早期的代码操纵。

## 49、跨域请求资源的方法有哪些？

1.porxy代理 定义和用法:proxy代理用于将请求发送给后台服务器,通过服务器来发送请求,然后将请求的结果传递给前端。 实现方法:通过nginx代理; 注意点:如果你代理的是https协议的请求,那么你的...

2.CORS 【Cross-Origin Resource Sharing】 定义和用法:是现代浏览器支持跨域资源请求的一种最常用的...

3.jsonp 定义和用法:通过动态插入一个script标签。浏览器对script的资源引用...

## 50、****定义事件冒泡？ 

avaScript允许DOM元素嵌套在一起。在这种情况下，如果单击子级的处理程序，父级的处理程序也将执行同样的工作。

## 51、简述一下Sass，Less，请说明区别？

他们是动态的样式语言，是CSS预处理器,CSS上的一种抽象层。他们是一种特殊的语法/语言而编译成CSS。
变量符不一样，less是@，而Sass是$;
Sass支持条件语句，可以使用if{}else{},for{}循环等等。而Less不支持;
Sass是基于Ruby的，是在服务端处理的，而Less是需要引入less.js来处理Less代码输出Css到浏览器

## 52、数组扁平化，不用api

数组扁平化的自我（手撕）实现【核心：用到了递归，如果当前元素还是数组，继续递归，然后 res=res.concat(myFlat(arr[i])) 因为 concat是没有副作用与原数组的！！，否则是 res.push(arr[i]) 。

代码实现如下:

```javascript
function myFlat(arr){



    let res = [];



    for(let i=0; i<arr.length; i++){   



        if(arr[i] instanceof Array){



            res = res.concat(myFlat(arr[i]));



        }else {



            res.push(arr[i]);



        }



    }



    return res;



}



 



let arr = [1,[2,3,[4,5]]];



console.log(myFlat(arr))
```

2、运行结果是符合预期的

![img](https://img-blog.csdnimg.cn/20190918153757609.png)

## 53、**什么样的布尔运算符可以在JavaScript中使用？**

And”运算符（&&），'Or'运算符（||）和'Not'运算符（！）可以在JavaScript中使用。

*运算符没有括号。

## 54、用javascript实现观察者模式

## ES5下的实现

再ES5中主要是通过`Object.defineProperties`方法定义对象属性的设置(set)和获取(get),并再进行设置时执行相关的处理函数,如下:



```jsx
var targetObj={
  age:1
}

function observer(oldval,newval){
  console.log('name属性的值从 '+oldval+'改变为 '+newval);
}

Object.defineProperty(targetObj,'name',{
  enumerable:true,
  configurable:true,
  get:function(){
    return name;
  },
  set:function(val){
    //调用处理函数
    observer(name,val);
    name=val;
  }
});

targetObj.name="www";
targetObj.name="mmm";
console.info('targetObj:',targetObj);
```

结果为:



```css
name属性的值从 改变为 www
name属性的值从 www改变为 mmm
targetObj:{age:1,name:"mmm"}
```

## ES6的实现（使用set方法实现）



```jsx
class  TargetObj{
  constructor(age,name){
    this.name=name;
    this.age=age;
  }
  set name(val){
    Observer(name,val);
    name=val;
  }
}

function Observer(oldval,newval){
  console.info('name属性的值从 '+ oldval +' 改变为 ' + newval);
}

let targetObj2 = new TargetObj(1,'www');
targetObj2.name="mmm";
console.info(targetObj2);
```



 

## 55、简述一下面象对象的六法则

- 单一职责原则：一个类只做它该做的事情。（单一职责原则想表达的就是”高内聚”，写代码最终极的原则只有六个字”高内聚、低耦合”，所谓的高内聚就是一个代码模块只完成一项功能，在面向对象中，如果只让一个类完成它该做的事，而不涉及与它无关的领域就是践行了高内聚的原则，这个类就只有单一职责。我们都知道一句话叫”因为专注，所以专业”，一个对象如果承担太多的职责，那么注定它什么都做不好。这个世界上任何好的东西都有两个特征，一个是功能单一，好的相机绝对不是电视购物里面卖的那种一个机器有一百多种功能的，它基本上只能照相；另一个是模块化，好的自行车是组装车，从减震叉、刹车到变速器，所有的部件都是可以拆卸和重新组装的，好的乒乓球拍也不是成品拍，一定是底板和胶皮可以拆分和自行组装的，一个好的软件系统，它里面的每个功能模块也应该是可以轻易的拿到其他系统中使用的，这样才能实现软件复用的目标。）
- 开闭原则：软件实体应当对扩展开放，对修改关闭。（在理想的状态下，当我们需要为一个软件系统增加新功能时，只需要从原来的系统派生出一些新类就可以，不需要修改原来的任何一行代码。要做到开闭有两个要点：①抽象是关键，一个系统中如果没有抽象类或接口系统就没有扩展点；②封装可变性，将系统中的各种可变因素封装到一个继承结构中，如果多个可变因素混杂在一起，系统将变得复杂而混乱，如果不清楚如何封装可变性，可以参考《设计模式精解》一书中对桥梁模式的讲解的章节。）
- 依赖倒转原则：面向接口编程。（该原则说得直白和具体一些就是声明方法的参数类型、方法的返回类型、变量的引用类型时，尽可能使用抽象类型而不用具体类型，因为抽象类型可以被它的任何一个子类型所替代，请参考下面的里氏替换原则。）
- 里氏替换原则：任何时候都可以用子类型替换掉父类型。（关于里氏替换原则的描述，Barbara Liskov女士的描述比这个要复杂得多，但简单的说就是能用父类型的地方就一定能使用子类型。里氏替换原则可以检查继承关系是否合理，如果一个继承关系违背了里氏替换原则，那么这个继承关系一定是错误的，需要对代码进行重构。例如让猫继承狗，或者狗继承猫，又或者让正方形继承长方形都是错误的继承关系，因为你很容易找到违反里氏替换原则的场景。需要注意的是：子类一定是增加父类的能力而不是减少父类的能力，因为子类比父类的能力更多，把能力多的对象当成能力少的对象来用当然没有任何问题。）
- 接口隔离原则：接口要小而专，绝不能大而全。（臃肿的接口是对接口的污染，既然接口表示能力，那么一个接口只应该描述一种能力，接口也应该是高度内聚的。例如，琴棋书画就应该分别设计为四个接口，而不应设计成一个接口中的四个方法，因为如果设计成一个接口中的四个方法，那么这个接口很难用，毕竟琴棋书画四样都精通的人还是少数，而如果设计成四个接口，会几项就实现几个接口，这样的话每个接口被复用的可能性是很高的。Java中的接口代表能力、代表约定、代表角色，能否正确的使用接口一定是编程水平高低的重要标识。）
- 合成聚合复用原则：优先使用聚合或合成关系复用代码。（通过继承来复用代码是面向对象程序设计中被滥用得最多的东西，因为所有的教科书都无一例外的对继承进行了鼓吹从而误导了初学者，类与类之间简单的说有三种关系，Is-A关系、Has-A关系、Use-A关系，分别代表继承、关联和依赖。其中，关联关系根据其关联的强度又可以进一步划分为关联、聚合和合成，但说白了都是Has-A关系，合成聚合复用原则想表达的是优先考虑Has-A关系而不是Is-A关系复用代码，原因嘛可以自己从百度上找到一万个理由，需要说明的是，即使在Java的API中也有不少滥用继承的例子，例如Properties类继承了Hashtable类，Stack类继承了Vector类，这些继承明显就是错误的，更好的做法是在Properties类中放置一个Hashtable类型的成员并且将其键和值都设置为字符串来存储数据，而Stack类的设计也应该是在Stack类中放一个Vector对象来存储数据。记住：任何时候都不要继承工具类，工具是可以拥有并可以使用的，而不是拿来继承的。）
- 迪米特法则：迪米特法则又叫最少知识原则，一个对象应当对其他对象有尽可能少的了解。（迪米特法则简单的说就是如何做到”低耦合”，门面模式和调停者模式就是对迪米特法则的践行。对于门面模式可以举一个简单的例子，你去一家公司洽谈业务，你不需要了解这个公司内部是如何运作的，你甚至可以对这个公司一无所知，去的时候只需要找到公司入口处的前台美女，告诉她们你要做什么，她们会找到合适的人跟你接洽，前台的美女就是公司这个系统的门面。再复杂的系统都可以为用户提供一个简单的门面，Java Web开发中作为前端控制器的Servlet或Filter不就是一个门面吗，浏览器对服务器的运作方式一无所知，但是通过前端控制器就能够根据你的请求得到相应的服务。调停者模式也可以举一个简单的例子来说明，例如一台计算机，CPU、内存、硬盘、显卡、声卡各种设备需要相互配合才能很好的工作，但是如果这些东西都直接连接到一起，计算机的布线将异常复杂，在这种情况下，主板作为一个调停者的身份出现，它将各个设备连接在一起而不需要每个设备之间直接交换数据，这样就减小了系统的耦合度和复杂度，如下图所示。迪米特法则用通俗的话来将就是不要和陌生人打交道，如果真的需要，找一个自己的朋友，让他替你和陌生人打交。

  ![img](https://img-blog.csdn.net/20171030143641783?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdHJvdWJsZXNob290ZXI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

  ![img](https://img-blog.csdn.net/20171030143653588?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdHJvdWJsZXNob290ZXI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

## 56、谈谈垃圾回收机制方法以及内存管理

回收机制方式

1、定义和用法：垃圾回收机制(GC:Garbage Collection),执行环境负责管理代码执行过程中使用的内存。

2、原理：垃圾收集器会定期（周期性）找出那些不在继续使用的变量，然后释放其内存。但是这个过程不是实时的，因为其开销比较大，所以垃圾回收器会按照固定的时间间隔周期性的执行。

3、实例如下：

```
function` `fn1() {``  ``var` `obj = {name: ``'hanzichi'``, age: 10};``}``function` `fn2() {``  ``var` `obj = {name:``'hanzichi'``, age: 10};``  ``return` `obj;``}``var` `a = fn1();``var` `b = fn2();
```

fn1中定义的obj为局部变量，而当调用结束后，出了fn1的环境，那么该块内存会被js引擎中的垃圾回收器自动释放；在fn2被调用的过程中，返回的对象被全局变量b所指向，所以该块内存并不会被释放。

 4、垃圾回收策略：标记清除(较为常用)和引用计数。

**标记清除：**

　　定义和用法：当变量进入环境时，将变量标记"进入环境"，当变量离开环境时，标记为："离开环境"。某一个时刻，垃圾回收器会过滤掉环境中的变量，以及被环境变量引用的变量，剩下的就是被视为准备回收的变量。

　　到目前为止，IE、Firefox、Opera、Chrome、Safari的js实现使用的都是标记清除的垃圾回收策略或类似的策略，只不过垃圾收集的时间间隔互不相同。

**引用计数：**

　　定义和用法：引用计数是跟踪记录每个值被引用的次数。

　　基本原理：就是变量的引用次数，被引用一次则加1，当这个引用计数为0时，被视为准备回收的对象。

内存管理

1、什么时候触发垃圾回收？

垃圾回收器周期性运行，如果分配的内存非常多，那么回收工作也会很艰巨，确定垃圾回收时间间隔就变成了一个值得思考的问题。

IE6的垃圾回收是根据内存分配量运行的，当环境中的变量，对象，字符串达到一定数量时触发垃圾回收。垃圾回收器一直处于工作状态，严重影响浏览器性能。

IE7中，垃圾回收器会根据内存分配量与程序占用内存的比例进行动态调整，开始回收工作。

2、合理的GC方案：(1)、遍历所有可访问的对象; (2)、回收已不可访问的对象。

3、GC缺陷：(1)、停止响应其他操作；

4、GC优化策略：(1)、分代回收（Generation GC）;(2)、增量GC

## 57、开发过程中遇到内存泄漏的问题

1、定义和用法：

内存泄露是指一块被分配的内存既不能使用，又不能回收，直到浏览器进程结束。C#和Java等语言采用了自动垃圾回收方法管理内存，几乎不会发生内存泄露。我们知道，浏览器中也是采用自动垃圾回收方法管理内存，但由于浏览器垃圾回收方法有bug，会产生内存泄露。

2、内存泄露的几种情况:

(1)、当页面中元素被移除或替换时，若元素绑定的事件仍没被移除，在IE中不会作出恰当处理，此时要先手工移除事件，不然会存在内存泄露。

实例如下:

```
<div id=``"myDiv"``>``  ``<input type=``"button"` `value=``"Click me"` `id=``"myBtn"``>``</div>``<script type=``"text/javascript"``>``  ``var` `btn = document.getElementById(``"myBtn"``);``  ``btn.onclick = ``function``(){``    ``document.getElementById(``"myDiv"``).innerHTML = ``"Processing..."``;``  ``}``</script>
```

解决方法如下：

```
<div id=``"myDiv"``>``  ``<input type=``"button"` `value=``"Click me"` `id=``"myBtn"``>``</div>``<script type=``"text/javascript"``>``  ``var` `btn = document.getElementById(``"myBtn"``);``  ``btn.onclick = ``function``(){``  ``btn.onclick = ``null``;``    ``document.getElementById(``"myDiv"``).innerHTML = ``"Processing..."``;``  ``}``</script>
```

(2)、由于是函数内定义函数，并且内部函数--事件回调的引用外暴了，形成了闭包。闭包可以维持函数内局部变量，使其得不到释放。

实例如下：

```
function` `bindEvent(){``  ``var` `obj=document.createElement(``"XXX"``);``  ``obj.onclick=``function``(){``    ``//Even if it's a empty function``  ``}``}
```

解决方法如下：

```
function` `bindEvent(){``  ``var` `obj=document.createElement(``"XXX"``);``  ``obj.onclick=``function``(){``     ``//Even if it's a empty function``  ``}``  ``obj=``null``;``}
```

.

## 58、**定义事件冒泡？**

JavaScript允许DOM元素嵌套在一起。在这种情况下，如果单击子级的处理程序，父级的处理程序也将执行同样的工作。

## 59、**一个特定的框架如何使用JavaScript中的超链接定位？**

```
可以通过使用“target”属性在超链接中包含所需帧的名称来实现。

<a href=”newpage.htm” target=”newframe”>>New Page</a>
```



## 60、浏览器有哪些兼容问题，你封装过什么插件

1.cssHack
一丶http://browserhacks.com/
这个网站可以直接查询各种hack非常方便

2.通过Polyfill和shiv
Polyfill就像一个镊子可以帮我们刮平很多浏览器之的兼容性问题 : 
比如让不支持picture标签的引入picturefill插件就可以在不支持的浏览器使用picture标签
shiv和Polyfill差不多 htmlshiv : https://github.com/aFarkas/html5shiv 
比如这个html5shiv：
作用：在ie678不支持新的html5标签，通过引入这个库，就可以让这些浏览器有能力识别这些标签，其实就是利用了ie的createElement
<!--[if lt IE 9]>
<script src="bower_components/html5shiv/dist/html5shiv.js"></script>
<![endif]-->
像这样引入

3.respond
通过引入这样一个库可以让ie678支持媒体查询
连接: https://github.com/scottjehl/Respond

4.通过Modernizr
它可以查询浏览器对css3，html5的支持情况，如果浏览器支持某个特性，那么它就会向浏览器添加相对应的类，如果不支持它就会添加一个no开头的一个类
这是一个主动去检测兼容性的一种方式，对于一些实现性的，或者不确定兼容性的一些特性，建议使用这种主动性检测的方式，这样提供了一种主动性的解决方案
用法 ：点击 download ，自己选择特性 ，点击 build ，点击拷贝 ，拷贝到一个新的 js 文件，这个 js 就可以检测是否可以兼容哪个特性
如果支持，那么html标签上就会多一个类比如 svg 就会有 class="svg",这样就可以自己写两类类名 .svg 和 .no-svg 分别引入不同的样式
具体用法参考官方文档


## 61、如何防止XSRF攻击

一  CSRF是什么？
 跨站请求伪造（英语：Cross-site request forgery），也被称为 one-click attack 或者 session riding，通常缩写为 CSRF 或者 XSRF， 是一种挟制用户在当前已登录的Web应用程序上执行非本意的操作的攻击方法。

二、CSRF的原理

![img](https:////upload-images.jianshu.io/upload_images/10852140-d963ba5a5de3deeb.png?imageMogr2/auto-orient/strip|imageView2/2/w/700/format/webp)

image.png

三、CSRF的防范措施

1、验证 HTTP Referer 字段



```undefined
HTTP头中的Referer字段记录了该 HTTP 请求的来源地址。在通常情况下，访问一个安全受限页面的请求来自于同一个网站，而如果黑客要对其实施 CSRF 攻击，他一般只能在他自己的网站构造请求。因此，可以通过验证Referer值来防御CSRF 攻击。
```

2、验证码。
 验证码
 利用验证码将用户收到的信息与后台服务器进行比对，每次用户提交都需要用户在表单中填写一个图片上的随机字符串，不符则进行拒绝。
 3、添加token验证

CSRF攻击之所以能够成功，是因为攻击者可以完全伪造用户的请求，该请求中所有的用户验证信息都存在cookie中，因此攻击者可以在不知道这些验证信息的情况下直接利用用户自己的cookie来通过安全验证。要防止CSRF，关键在于在请求中放入黑客所不能伪造的信息，并且该信息不存在于cookie之中。可以在HTTP请求中以参数的形式加入一个随机产生的token，并在服务器建立一个拦截器来验证这个token，如果请求中没有token或者token不正确，则认为可能是CSRF攻击而拒绝该请求。
 现在业界一致的做法就是使用Anti CSRF Token来防御CSRF。



```undefined
用户访问某个表单页面。
服务端生成一个Token，放在用户的Session中，或者浏览器的Cookie中。
在页面表单附带上Token参数。
用户提交请求后，服务端验证表单中的Token是否与用户Session（或Cookies）中的Token一致，一致为合法请求，不是则非法请求。
```

这个Token值必须是随机的，不可预测的。由于Token的存在，攻击者无法再构造一个带有合法Token的请求实施CSRF攻击。另外使用Token应注意Token的保密性，尽量把敏感操作由GET改成POST，以form或者AJAX形式提交，避免Token泄露。

4、尽量使用POST，限制GET

GET接口能够直接将请求地址暴露给攻击者，所以要防止CSRF一定最好不要用GET。当然POST并不是万无一失，攻击者只需要构造一个form表单就可以，但需要在第三方页面做，这样就增加了暴露的可能性。



 


## 62、如何判断一个对象是否为数组，函数

在js中判断数据类型通常使用typeof，但是typeof在判断数组和对象时的结果都是object。

那么，怎么才能区分对象和数组呢？

1. 判断原型

var obj = {};

var arr = [];

 console.log(arr);

 console.log(obj);
数组和对象的原型对象不一样，由此可以通过判断原型来判断一个对象是否为数组

var obj = {};

var arr = [];

console.log(Object.getPrototypeOf(obj) === Object.prototype);

console.log(Object.getPrototypeOf(obj) === Array.prototype);
但是这种方法虽然通过检查原型来判断是否为数组，不是特别稳定，因为原型可以人为改变的一旦原型改变就可能会失效。

var obj = {};

 var arr = [];

 Object.setPrototypeOf(obj, Array.prototype);

 console.log(Object.getPrototypeOf(obj) === Object.prototype);//false

 console.log(Object.getPrototypeOf(obj) === Array.prototype);//true
此时arr的原型不是Array.prototype第一个返回的结果为false，如此，不能判断是否为数组。

2. 判断构造函数

创建对象和创建数组的构造函数不同可以通过此种方法判断一个对象是否为数组。

var obj = {};

 var arr = [];

 console.log(Object.getPrototypeOf(obj).constructor === Object);

 console.log(Object.getPrototypeOf(obj).constructor === Array);
由此可见，两个输出结果并不一样，并且也符合我们的认知，通过此种方法也可以判断一个对象是否为数组。

但该种方法与上一种方法的问题一样即当构造函数被修改之后就无法判断真正的数组。

var obj = {};

var arr = [];

Object.getPrototypeOf(obj).constructor = Array;

console.log(Object.getPrototypeOf(obj).constructor === Object);//false

console.log(Object.getPrototypeOf(obj).constructor === Array);//true

console.log(Object.getPrototypeOf(arr).constructor === Array);//true
由此可见，该方法的缺点与上一种方法的一样。

在判断构造函数方法中除了直接判断外还可以使用 instanceof 判断一个对象是否为一个构造函数的实例

var obj = {};

var arr = [];

console.log(obj instanceof Array);//false

console.log(arr instanceof Array);//true
由此可见，该方法也可以判断是否为数组。

3. Array.isArray()

通过Array.isArray()可以判断一个对象是否为数组。

var obj = {};

var arr = [];

console.log(Array.isArray(obj));//false

console.log(Array.isArray(arr));//true
由此可见此种方法可行，那么当我们把其构造函数改变后还会出现和上面一样的情况吗？

var obj = {};

var arr = [];

Object.getPrototypeOf(obj).constructor = Array;//false

console.log(Array.isArray(obj));//true

console.log(Array.isArray(arr));
由此可见，即便将obj的构造函数改为Array也能判断出真正的数组。

4. 用Object.toString()方法判断

var obj = {};

var arr = [];

console.log(obj.toString());//[object Object]

console.log(arr.toString());//
由此可见对象是Object创建的，但是数组的toString()返回的是空。

这是因为js中每个对象都有toString()方法，该方法继承自Object.toString()方法，但数组被重写了，但是我们可以使用call()来调用数组的toString的原始方法来判断。

var obj = {};

var arr = [];

console.log(obj.toString());//[object Object]

console.log(Object.prototype.toString.call(arr));//[object Array]
由此可见，也可以通过此种方法判断一个对象是否为数组。

那么，如果改变构造函数是否会发生类似之前的情况呢？

var obj = {};

var arr = [];

Object.getPrototypeOf(obj).constructor = Array;

console.log(obj.toString());//[object Object]

console.log(Object.prototype.toString.call(arr));//[object Array]
由此可见，此时还能正常判断并不会发生此前出现的情况。

使用场景

判断一个对象是否为数组在什么地方使用呢

 在进行克隆时，需要判断对象的属性是否为数组，根据结果采取不同的操作。

总结

 1. 判断原型和判断构造函数的方法的结果会因为原型和构造函数的改变发生改变，并不十分稳定。

 2. Array,isArray()和Object.toString()方法较为稳定，较为推荐。

 3. 通常在进行克隆时会用到判断一个对象是否为数组。





## 63、**JavaScript中如何使用事件处理程序？**



事件是由用户生成活动（例如单击链接或填写表单）导致的操作。需要一个事件处理程序来管理所有这些事件的正确执行。事件处理程序是对象的额外属性。此属性包括事件的名称以及事件发生时采取的操作。

## 64、**解释延迟脚本在JavaScript中的作用？**



默认情况下，在页面加载期间，HTML代码的解析将暂停，直到脚本停止执行。这意味着，如果服务器速度较慢或者脚本特别沉重，则会导致网页延迟。在使用Deferred时，脚本会延迟执行直到HTML解析器运行。这减少了网页加载时间，并且它们的显示速度更快。

## 65、实现一个函数clone，可以对javascript中的5种主要数据类型进行值复制。

```
var clone = function (type) {
    var o;
    var typeA = typeof type;
    switch (typeA){
        case 'string':
        o = typeA+'';
            break;
        case 'number':
        o = typeA-0;
            break;
        case 'undefined':
            break;
        case 'boolean':
            o = typeA;
            break;
        case 'object':
            if(type===null){
             o = null;
            }else {
              if(Object.prototype.toString.call(type).slice(8,-1)==='Array'){
                   o = [];
                    for(var i = 0;i<type.length;i++){
                        o.push(clone(type[i]));
                    }
              }else {
                  o = {};
                  for(var key in type){
                      o[key] = clone(type[key]);
                  }
              }
            }
            break;
        default:
            break;
    }
    return o;
}
```




## 66、**decodeURI（）和encodeURI（）是什么？**

EncodeURl（）用于将URL转换为十六进制编码。而DecodeURI（）用于将编码的URL转换回正常。

## 67、**解释JavaScript中的pop（）方法？**

```
pop（）方法与shift（）方法类似，但不同之处在于Shift方法在数组的开头工作。此外，pop（）方法将最后一个元素从给定的数组中取出并返回。然后改变被调用的数组。

例：

var cloths = [“Shirt”, “Pant”, “TShirt”];

cloths.pop();

//Now cloth becomes Shirt,Pant
```



## 68、解释什么是回调函数，并提供一个简单的例子。

回调函数是可以作为参数传递给另一个函数的函数，并在某些操作完成后执行。下面是一个简单的回调函数示例，这个函数在某些操作完成后打印消息到控制台。



```javascript
function modifyArray(arr, callback) {
 // 对 arr 做一些操作
 arr.push(100);
 // 执行传进来的 callback 函数
 callback();
}
var arr = [1, 2, 3, 4, 5];
modifyArray(arr, function() {
 console.log("array has been modified", arr);
});
```



 

## 69、面向对象编程与面向过程编程的区别

面向过程考虑数据变换; 面向过程的世界是以目标问题规定的I/O为中心的

面向对象考虑功能分工; 面向对象的世界是以内部实现的可理解性为中心的

## 70、eval是做什么的？性能怎么样？安全如何？ 

eval方法是在运行时对脚bai本进行解du释执行，而普通的javascript会有一个zhi预处dao理的过程。所以会有一些性能上zhuan的损失，但是通常通过一些手段能将这些性能损失降低到非常少。不至于谈虎色变。
eval通常用在一些需要动态执行字符串，或将字符串转为javascript对象的场景，比如将json字符串转为javascript对象。
至于eval容易被XSS攻击是属于想当然的说法吧，XSS攻击就是在你的页面上嵌入html或javascript代码，我觉得与是否使用eval方法没有什么关系。

## 71、函数节流、防抖

函数防抖(debounce)

概念： 在事件被触发n秒后再执行回调，如果在这n秒内又被触发，则重新计时。
生活中的实例： 如果有人进电梯（触发事件），那电梯将在10秒钟后出发（执行事件监听器），这时如果又有人进电梯了（在10秒内再次触发该事件），我们又得等10秒再出发（重新计时）。

函数节流(throttle)

概念： 规定一个单位时间，在这个单位时间内，只能有一次触发事件的回调函数执行，如果在同一个单位时间内某事件被触发多次，只有一次能生效。
生活中的实例： 我们知道目前的一种说法是当 1 秒内连续播放 24 张以上的图片时，在人眼的视觉中就会形成一个连贯的动画，所以在电影的播放（以前是，现在不知道）中基本是以每秒 24 张的速度播放的，为什么不 100 张或更多是因为 24 张就可以满足人类视觉需求的时候，100 张就会显得很浪费资源。


分析图
假设，我们观察的总时间为10秒钟，规定1秒作为一次事件的最小间隔时间。
如果触发事件的频率是 0.5s/次，那么
函数防抖如图

![img](https://img2018.cnblogs.com/blog/1151544/201906/1151544-20190606142434794-1253043957.png)

 

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

因为始终没法等一秒钟就被再次触发了，所以最终没有一次事件是成功的。
函数节流如图

![img](https://img2018.cnblogs.com/blog/1151544/201906/1151544-20190606142441724-816933604.png)

 

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

因为控制了最多一秒一次，频率为0.5s/次，所以每一秒钟就有一次事件作废。最终控制成1s/次
如果触发事件的频率是 2s/次，那么
函数防抖如图

![img](https://img2018.cnblogs.com/blog/1151544/201906/1151544-20190606142505531-1661322909.png)

 

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)因为2s/次已经大于了规定的最小时间，所以每计时两秒便触发一次。
函数节流如图

同样，2s/次 大于了最小时间规定，所以每一次触发都生效。
应用场景
对于函数防抖，有以下几种应用场景：

给按钮加函数防抖防止表单多次提交。
对于输入框连续输入进行AJAX验证时，用函数防抖能有效减少请求次数。
判断scroll是否滑到底部，滚动事件+函数防抖

总的来说，适合多次事件一次响应的情况

对于函数节流，有如下几个场景：

游戏中的刷新率
DOM元素拖拽
Canvas画笔功能

总的来说，适合大量事件按时间做平均分配触发。

函数防抖：

```
function debounce(fn, wait) {
  var timer = null;
  return function () {
      var context = this
      var args = arguments
      if (timer) {
          clearTimeout(timer);
          timer = null;
      }
      timer = setTimeout(function () {
          fn.apply(context, args)
      }, wait)
  }
}
var fn = function () {
  console.log('boom')
}
setInterval(debounce(fn,500),1000) // 第一次在1500ms后触发，之后每1000ms触发一次
setInterval(debounce(fn,2000),1000) // 不会触发一次（我把函数防抖看出技能读条，如果读条没完成就用技能，便会失败而且重新读条）
```

之所以返回一个函数，因为防抖本身更像是一个函数修饰，所以就做了一次函数柯里化。里面也用到了闭包，闭包的变量是timer。

函数节流

```
function throttle(fn, gapTime) {
  let _lastTime = null;
  return function () {
    let _nowTime = + new Date()
    if (_nowTime - _lastTime > gapTime || !_lastTime) {
      fn();
      _lastTime = _nowTime
    }
  }
}
let fn = ()=>{
  console.log('boom')
}
setInterval(throttle(fn,1000),10)
```

如图是实现的一个简单的函数节流，结果是一秒打出一次boom

小结

函数防抖和函数节流是在时间轴上控制函数的执行次数。防抖可以类比为电梯不断上乘客,节流可以看做幻灯片限制频率播放电影。

## 72、“use strict”的作用是什么？

use strict 出现在 JavaScript 代码的顶部或函数的顶部，可以帮助你写出更安全的 JavaScript 代码。如果你错误地创建了全局变量，它会通过抛出错误的方式来警告你。例如，以下程序将抛出错误：



```javascript
function doSomething(val) {
 "use strict"; 
 x = val + 10;
}
```

它会抛出一个错误，因为 x 没有被定义，并使用了全局作用域中的某个值对其进行赋值，而 use strict 不允许这样做。下面的小改动修复了这个错误：



```javascript
function doSomething(val) {
 "use strict"; 
 var x = val + 10;
}
```



 

## 73、了解ES6 的 Proxy吗？

概述

- Proxy用于修改某些操作的默认行为，等同于在语言层面上做出修改，所以属于一种“元编程”，即对编程语言进行编程。
- Proxy可以理解成在目标对象前架设一层**拦截层**，外界访问该对象都必须先通过这层拦截，因此提供一种机制可以对外界的访问进行拦截或过滤。

实例的方法

1.get()

- get方法用于拦截某个属性的读取操作。

```js
let person = {
    name : '张三'
}

let proxy = new Proxy(person,{
    get : function(target,property){
        if(property in target){
            return target[property];
        }else{
            throw new Error('property ' + property + ' no found!')
        }
    }
})

proxy.name;		//'张三'
proxy.age;		//property age no found!
12345678910111213141516
```

以上的实例当获取对象没有的属性age时，就会抛出错误；若不通过代理，则会返回undefined；

- **get方法可以继承**

```js
let proto = new Proxy({},{
    get(target,propertykey,receiver){
        console.log('get '+ propertykey);
        return target[propertykey];
    }
});

let obj = Object.create(proto);
obj.lalala; 		//get lalala
123456789
```

- 利用Proxy对象，可以将读取属性的操作变为执行某个函数，从而实现函数的链式操作；

```js
let pipe = (function(){
    return function(value){
        //创建函数执行栈
        let funcStack = [];
        //创建拦截器
        let oproxy = new Proxy({},{
            get: function(pipeObj,fnName){
                //当属性为get，返回函数栈一次执行函数后的结果
                if(fnName === 'get'){
                    return funcStack.reduce(function(val,fn){
                        return fn(val);
                    },value)
                }
                //否则将函数置入函数执行栈中，并返回又一个拦截器
                else{
                    funcStack.push(window[fnName]);
                    return oproxy;
                }
            }
        });
       // 将拦截器返回给pipe 
        return oproxy;
    }
}())

let double = n=>n*2;
let pow = n=>n*n;

pipe(2).double.pow.get;		//16
1234567891011121314151617181920212223242526272829
```

------

2.set()

- set方法用户拦截某个属性的赋值操作

```js
let ageLimit = {
    set : function(obj,prop,val){
        if(prop === 'age'){
            if(!Number.isInteger(val)){
                throw new TypeError('the age must be a integer')
            }
            if(val > 150 || val < 0){
                throw new RangeError('the age must be from 0 to 150')
            }
        }
        
        obj[prop] = val;
    }
}

let person = new Proxy({},ageLimit);
person.age = 200;	//the age must be from 0 to 150;
person.age = 'abc'	//the age must be a integer;
person.age = 20;	//正常
person.age;			// 20;
1234567891011121314151617181920
```

- 通过ageLimit拦截器，可以及时对用户的数据输入做数据验证，以保证数值规范；
- 有时候，我们会在对象上设置内部属性，属性名的第一个字符为下划线’_’，表示该属性不能被外部访问或使用。结合get和set方法，就可以做到防止内部属性被外部读写；

```js
let handler = {
    get (target , key){
        invariant(key,'get');
        return target[key];
    },
    set (target , key , value){
        invatiant(key,'set');
        taget[key] = value;
        return true;
    }
}


function invariant(key,action){
    if(key[0] === '_'){
        throw new Error('no allowed to ' + action + ' the property');
    }
}

let target = {
    _name : '张三',
    _age : 20
};
let proxy = new Proxy(target,handler);
proxy._name;	// no allowed to get the property
proxy._age = 12	// no allowed to set the property
1234567891011121314151617181920212223242526
```

------

3.apply()

- apply方法拦截函数的调用、call、apply操作
- apply方法接收三个参数：目标对象、目标对象的上下文的this、目标对象的参数数组

```js
let target = function(){
    console.log('I am the target');
}

let handler = {
    apply : function(){
        console.log('I am the apply');
    }
}

let p = new Proxy(target,handler);

p();	//I am the apply;
12345678910111213
```

4.其他方法

> 1. has(target,propkey)
>    - 拦截`hasProperty()`、`propkey in proxy`操作，返回一个布尔值；
> 2. deleteProperty(target,propkey)
>    - 拦截`delete proxy[propkey]`操作，返回一个布尔值；
> 3. ownKeys(target)
>    - 拦截`Object.getOwnPropertyNarnes(proxy)`、`Object.getOwnPropertySyrnbols (proxy)`、 `Object.keys(proxy)`，返回一个数组。该方法返回目标对象所有自身属性的属 性名，而 Object .keys()的返回结果仅包括目标对象自身的可遍历属性；
> 4. getOwnPropertyDescriptor(target,propkey)
>    - 拦截 `Object.getOwnPropertyDescriptor(proxy, propKey)`，返回属性的描述对象;
> 5. defineProperty(target,propkey)
>    - 拦截 `Object.defineProperty(proxy, propKey, propDesc)`、 `Object.define Properties(proxy, propDescs)`，返回一个布尔值;
> 6. preventExtensions(target)
>    - 拦截 `Object . preventExtensions (proxy)` ，返回一个布尔值;
> 7. getPrototypeOf(target)
>    - 拦截`Object.getPrototypeOf(proxy)`，返回一个对象；
> 8. isExtensible(target)
>    - 拦截`Object.isExtensible(proxy)`,返回一个布尔值；
> 9. setPrototypeOf(target)
>    - 拦截`Object.setPrototypeOf(proxy,proto)`,返回一个布尔值；若对象为函数，则还有二外两种操作可以拦截（apply,construct）
> 10. construct(target,args)
>     - 拦截Proxy实例作为构造函数调用的操作，比如 new proxy(…args);


## 74、深拷贝是什么？ 

变量存储类型分两类

①基本类型：直接存储在栈中的数据。（字符串、布尔值、未定义、数字、null）

②引用类型：将该对象引用地址存储在**栈**中，然后对象里面的数据存放在**堆**中。（数组、对象、函数）

这里解释一下为什么null是基本类型：有人说他用type of打印出来不是oject吗？

null只是一个空指针对象，没有数据。根据引用类型特点可以看一下是否符合。

=================

回到我们的问题上

说说深拷贝和浅拷贝还有赋值的区别，这样好理解

浅拷贝：也就是拷贝A对象里面的数据，但是不拷贝A对象里面的子对象

深拷贝：会克隆出一个对象，数据相同，但是引用地址不同（就是拷贝A对象里面的数据，而且拷贝它里面的子对象）

赋值：获得该对象的引用地址



![img](https:////upload-images.jianshu.io/upload_images/15856169-26e2e4a0fc8a39b4.png?imageMogr2/auto-orient/strip|imageView2/2/w/310/format/webp)

浅拷贝和深拷贝的区别

![img](https:////upload-images.jianshu.io/upload_images/15856169-88bd5975eafaa488.png?imageMogr2/auto-orient/strip|imageView2/2/w/600/format/webp)

三者的区别

下面用实例来说明

赋值特点说明：

![img](https:////upload-images.jianshu.io/upload_images/15856169-f1e9ecf6d9d60022.png?imageMogr2/auto-orient/strip|imageView2/2/w/894/format/webp)

赋值，疑惑点来自于下图。

![img](https:////upload-images.jianshu.io/upload_images/15856169-c267376dd0e7f2a3.png?imageMogr2/auto-orient/strip|imageView2/2/w/465/format/webp)

赋值打印图

浅拷贝特点说明：

![img](https:////upload-images.jianshu.io/upload_images/15856169-f0f7ebf1a5fb2f74.png?imageMogr2/auto-orient/strip|imageView2/2/w/601/format/webp)

obj还是上面的

深拷贝说明：

![img](https:////upload-images.jianshu.io/upload_images/15856169-81300a534b525fbb.png?imageMogr2/auto-orient/strip|imageView2/2/w/479/format/webp)

第一种方法的缺陷在于函数不能拷贝

![img](https:////upload-images.jianshu.io/upload_images/15856169-cb3b1c628336d4bc.png?imageMogr2/auto-orient/strip|imageView2/2/w/554/format/webp)



 

## 75、解释 JavaScript 中的 null 和 undefined。

JavaScript 中有两种底层类型：null 和 undefined。它们代表了不同的含义：

- 尚未初始化：undefined；
- 空值：null。

null和undefined是两个不同的对象, 有图为证:



![img](https:////upload-images.jianshu.io/upload_images/9890665-81a1b4abb5db481a.png?imageMogr2/auto-orient/strip|imageView2/2/w/256/format/webp)



 

## 76、解释 JavaScript 中的值和类型。

JavaScript提供两种数据类型: 基本数据类型和引用数据类型
 基本数据类型有:

- String
- Number
- Boolean
- Null
- Undefined
- Symbol

引用数据类型有:

- Object
- Array
- Function

 

## 77、scroll resize 使用函数节流实现不要频繁触发事件的需求

```

<script>
		function throttle(fun,delay){
			let timer = null;
			if(timer){
				clearTimeout(timer);
			}
			return function(){
				setTimeout(function(){
					fun();
				},delay);
			}
		} 
		var firstResize = true;
		window.onresize = function(){
			if(firstResize){
				throttle(function(){
					alert(1)
				},30)();
			}
			firstResize = false;
		}
	</script>
```



## 78、解释事件冒泡以及如何阻止它？

事件冒泡是指嵌套最深的元素触发一个事件，然后这个事件顺着嵌套顺序在父元素上触发。

防止事件冒泡的一种方法是使用 event.cancelBubble 或 event.stopPropagation()（低于 IE 9）。



 

## 79、JavaScript 中的 let 关键字有什么用？

用let声明变量只在块级作用域起作用，适合在for循环使用，也不会出现变量提升现象。同一个代码块内，不可重复声明的相同变量，不可重复声明函数内的参数。

## 80、 jQuery 优点和缺点

具体而言，jQuery有如下优势：

1，提供了用css选择符来选择dom元素的api（现在已经被浏览器内置支持）
2，提供了浏览器的检测api
3，提供了兼容的功能性api
4，提供了DOM的批处理操作（批处理思想永远都不会过时）
5，提供了dom操作的链式操作
6，提供了插件机制（代码复用变得容易，也不容易过时）


 缺点  现在已经很少操作DOM了  现代框架VUE中   jquery代码工程化  可维护性差vue很远



## 81、ES6 class关键字原理跟function什么区别

传统的javascript中只有对象，没有类的概念。它是基于原型的面向对象语言。原型对象特点就是将自身的属性共享给新对象。这样的写法相对于其它传统面向对象语言来讲，很有一种独树一帜的感脚！非常容易让人困惑！
 如果要生成一个对象实例，需要先定义一个构造函数，然后通过new操作符来完成。构造函数示例：



```jsx
//函数名和实例化构造名相同且大写（非强制，但这么写有助于区分构造函数和普通函数）
function Person(name,age) {
    this.name = name;
    this.age=age;
}
Person.prototype.say = function(){
    return "我的名字叫" + this.name+"今年"+this.age+"岁了";
}
var obj=new Person("laotie",88);//通过构造函数创建对象，必须使用new 运算符
console.log(obj.say());//我的名字叫laotie今年88岁了
```

构造函数生成实例的执行过程：



```dart
1.当使用了构造函数，并且new 构造函数(),后台会隐式执行new Object()创建对象;
2.将构造函数的作用域给新对象，（即new Object()创建出的对象），而函数体内的this就代表new Object()出来的对象。
3.执行构造函数的代码。
4.返回新对象（后台直接返回）;
```

ES6引入了Class（类）这个概念，通过class关键字可以定义类。该关键字的出现使得其在对象写法上更加清晰，更像是一种面向对象的语言。如果将之前的代码改为ES6的写法就会是这个样子：



```kotlin
class Person{//定义了一个名字为Person的类
    constructor(name,age){//constructor是一个构造方法，用来接收参数
        this.name = name;//this代表的是实例对象
        this.age=age;
    }
    say(){//这是一个类的方法，注意千万不要加上function
        return "我的名字叫" + this.name+"今年"+this.age+"岁了";
    }
}
var obj=new Person("laotie",88);
console.log(obj.say());//我的名字叫laotie今年88岁了
```

注意项



```bash
1.在类中声明方法的时候，千万不要给该方法加上function关键字
2.方法之间不要用逗号分隔，否则会报错
```

由下面代码可以看出类实质上就是一个函数。类自身指向的就是构造函数。所以可以认为ES6中的类其实就是构造函数的另外一种写法！



```tsx
console.log(typeof Person);//function
console.log(Person===Person.prototype.constructor);//true
```

以下代码说明构造函数的prototype属性，在ES6的类中依然存在着。
 `console.log(Person.prototype);//输出的结果是一个对象`
 实际上类的所有方法都定义在类的prototype属性上。代码证明下：



```jsx
Person.prototype.say=function(){//定义与类中相同名字的方法。成功实现了覆盖！
    return "我是来证明的，你叫" + this.name+"今年"+this.age+"岁了";
}
var obj=new Person("laotie",88);
console.log(obj.say());//我是来证明的，你叫laotie今年88岁了
```

当然也可以通过prototype属性对类添加方法。如下：



```jsx
Person.prototype.addFn=function(){
    return "我是通过prototype新增加的方法,名字叫addFn";
}
var obj=new Person("laotie",88);
console.log(obj.addFn());//我是通过prototype新增加的方法,名字叫addFn
```

还可以通过Object.assign方法来为对象动态增加方法



```jsx
Object.assign(Person.prototype,{
    getName:function(){
        return this.name;
    },
    getAge:function(){
        return this.age;
    }
})
var obj=new Person("laotie",88);
console.log(obj.getName());//laotie
console.log(obj.getAge());//88
```

constructor方法是类的构造函数的默认方法，通过new命令生成对象实例时，自动调用该方法。



```jsx
class Box{
    constructor(){
        console.log("啦啦啦，今天天气好晴朗");//当实例化对象时该行代码会执行。
    }
}
var obj=new Box();
```

constructor方法如果没有显式定义，会隐式生成一个constructor方法。所以即使你没有添加构造函数，构造函数也是存在的。constructor方法默认返回实例对象this，但是也可以指定constructor方法返回一个全新的对象，让返回的实例对象不是该类的实例。



```jsx
class Desk{
    constructor(){
        this.xixi="我是一只小小小小鸟！哦";
    }
}
class Box{
    constructor(){
       return new Desk();// 这里没有用this哦，直接返回一个全新的对象
    }
}
var obj=new Box();
console.log(obj.xixi);//我是一只小小小小鸟！哦
```

constructor中定义的属性可以称为实例属性（即定义在this对象上），constructor外声明的属性都是定义在原型上的，可以称为原型属性（即定义在class上)。hasOwnProperty()函数用于判断属性是否是实例属性。其结果是一个布尔值， true说明是实例属性，false说明不是实例属性。in操作符会在通过对象能够访问给定属性时返回true,无论该属性存在于实例中还是原型中。



```jsx
class Box{
    constructor(num1,num2){
        this.num1 = num1;
        this.num2=num2;
    }
    sum(){
        return num1+num2;
    }
}
var box=new Box(12,88);
console.log(box.hasOwnProperty("num1"));//true
console.log(box.hasOwnProperty("num2"));//true
console.log(box.hasOwnProperty("sum"));//false
console.log("num1" in box);//true
console.log("num2" in box);//true
console.log("sum" in box);//true
console.log("say" in box);//false
```

类的所有实例共享一个原型对象，它们的原型都是Person.prototype，所以**proto**属性是相等的



```jsx
class Box{
    constructor(num1,num2){
        this.num1 = num1;
        this.num2=num2;
    }
    sum(){
        return num1+num2;
    }
}
//box1与box2都是Box的实例。它们的__proto__都指向Box的prototype
var box1=new Box(12,88);
var box2=new Box(40,60);
console.log(box1.__proto__===box2.__proto__);//true
```

由此，也可以通过**proto**来为类增加方法。使用实例的**proto**属性改写原型，会改变Class的原始定义，影响到所有实例，所以不推荐使用！



```jsx
class Box{
    constructor(num1,num2){
        this.num1 = num1;
        this.num2=num2;
    }
    sum(){
        return num1+num2;
    }
}
var box1=new Box(12,88);
var box2=new Box(40,60);
box1.__proto__.sub=function(){
    return this.num2-this.num1;
}
console.log(box1.sub());//76
console.log(box2.sub());//20
```

**class不存在变量提升**，所以需要先定义再使用。因为ES6不会把类的声明提升到代码头部，但是ES5就不一样,**ES5存在变量提升**,可以先使用，然后再定义。



```jsx
//ES5可以先使用再定义,存在变量提升
new A();
function A(){

}
//ES6不能先使用再定义,不存在变量提升 会报错
new B();//B is not defined
class B{

}
```



 

## 82、如何检查一个数字是否为整数？

检查一个数字是小数还是整数，可以使用一种非常简单的方法，就是将它对 1 进行取模，看看是否有余数。



```javascript
function isInt(num) {
 return num % 1 === 0;
}
console.log(isInt(4)); // true
console.log(isInt(12.2)); // false
console.log(isInt(0.3)); // false
```

 

## 83、 什么是 IIFE（立即调用函数表达式）？

它是立即调用函数表达式（Immediately-Invoked Function Expression），简称 IIFE。函数被创建后立即被执行：



```javascript
(function IIFE(){
 console.log( "Hello!" );
})();
// "Hello!"
```

在避免污染全局命名空间时经常使用这种模式，因为 IIFE（与任何其他正常函数一样）内部的所有变量在其作用域之外都是不可见的。



 

## 84、如何在 JavaScript 中比较两个对象？

对于两个非原始值，比如两个对象（包括函数和数组），== 和 === 比较都只是检查它们的引用是否匹配，并不会检查实际引用的内容。

例如，默认情况下，数组将被强制转型成字符串，并使用逗号将数组的所有元素连接起来。所以，两个具有相同内容的数组进行 == 比较时不会相等：



```javascript
var a = [1,2,3];
var b = [1,2,3];
var c = "1,2,3";
a == c; // true
b == c; // true
a == b; // false
```

对于对象的深度比较，可以使用 deep-equal 这个库，或者自己实现递归比较算法



 


## 85、 你能解释一下 ES5 和 ES6 之间的区别吗？

- ECMAScript 5（ES5）：ECMAScript 的第 5 版，于 2009 年标准化。这个标准已在所有现代浏览器中完全实现。
- ECMAScript 6（ES6）或 ECMAScript 2015（ES2015）：第 6 版 ECMAScript，于 2015 年标准化。这个标准已在大多数现代浏览器中部分实现。

以下是 ES5 和 ES6 之间的一些主要区别：

箭头函数和字符串插值：



```jsx
const greetings = (name) => {
 return `hello ${name}`;
}
const greetings = name => `hello ${name}`;
```

常量

常量在很多方面与其他语言中的常量一样，但有一些需要注意的地方。常量表示对值的“固定引用”。因此，在使用常量时，你实际上可以改变变量所引用的对象的属性，但无法改变引用本身。



```cpp
const NAMES = [];
NAMES.push("Jim");
console.log(NAMES.length === 1); // true
NAMES = ["Steve", "John"]; // error
```

块作用域变量。

新的 ES6 关键字 let 允许开发人员声明块级别作用域的变量。let 不像 var 那样可以进行提升。

默认参数值

默认参数允许我们使用默认值初始化函数。如果省略或未定义参数，则使用默认值，也就是说 null 是有效值。



```jsx
// 基本语法
function multiply (a, b = 2) {
 return a * b;
}
multiply(5); // 10
```

类定义和继承

ES6 引入了对类（关键字 class）、构造函数（关键字 constructor）和用于继承的 extend 关键字的支持。

for…of 操作符

for…of 语句将创建一个遍历可迭代对象的循环。

用于对象合并的 Spread 操作



```cpp
const obj1 = { a: 1, b: 2 }
const obj2 = { a: 2, c: 3, d: 4}
const obj3 = {...obj1, ...obj2}
```

promise

promise 提供了一种机制来处理异步操作结果。你可以使用回调来达到同样的目的，但是 promise 通过方法链接和简洁的错误处理带来了更高的可读性。



```jsx
const isGreater = (a, b) => {
return new Promise ((resolve, reject) => {
 if(a > b) {
 resolve(true)
 } else {
 reject(false)
 }
 })
}
isGreater(1, 2)
.then(result => {
 console.log('greater')
})
.catch(result => {
 console.log('smaller')
})
```

模块导出和导入



```jsx
const myModule = { x: 1, y: () => { console.log('This is ES5') }}
export default myModule;
import myModule from './myModule';
```



 

## 86、 解释 JavaScript 中“undefined”和“not defined”之间的区别。

在 JavaScript 中，如果你试图使用一个不存在且尚未声明的变量，JavaScript 将抛出错误“var name is not defined”，让后脚本将停止运行。但如果你使用 typeof undeclared_variable，它将返回 undefined。

在进一步讨论之前，先让我们理解声明和定义之间的区别。

“var x”表示一个声明，因为你没有定义它的值是什么，你只是声明它的存在。



```jsx
var x; // 声明 x
console.log(x); // 输出: undefined
```

“var x = 1”既是声明又是定义（我们也可以说它是初始化），x 变量的声明和赋值相继发生。在 JavaScript 中，每个变量声明和函数声明都被带到了当前作用域的顶部，然后进行赋值，这个过程被称为提升（hoisting）。

当我们试图访问一个被声明但未被定义的变量时，会出现 undefined 错误。



```csharp
var x; // 声明
if(typeof x === 'undefined') // 将返回 true
```

当我们试图引用一个既未声明也未定义的变量时，将会出现 not defined 错误。



```cpp
console.log(y); // 输出: ReferenceError: y is not defined
```

 

## 87、如何在 JavaScript 中创建私有变量？

要在 JavaScript 中创建无法被修改的私有变量，你需要将其创建为函数中的局部变量。即使这个函数被调用，也无法在函数之外访问这个变量。例如：



```jsx
function func() {
 var priv = "secret code";
}
console.log(priv); // throws error
```

要访问这个变量，需要创建一个返回私有变量的辅助函数。



```jsx
function func() {
 var priv = "secret code";
 return function() {
 return priv;
 }
}
var getPriv = func();
console.log(getPriv()); // => secret code
```



 


## 88、 请解释原型设计模式。

原型模式可用于创建新对象，但它创建的不是非初始化的对象，而是使用原型对象（或样本对象）的值进行初始化的对象。原型模式也称为属性模式。

原型模式在初始化业务对象时非常有用，业务对象的值与数据库中的默认值相匹配。原型对象中的默认值被复制到新创建的业务对象中。

经典的编程语言很少使用原型模式，但作为原型语言的 JavaScript 在构造新对象及其原型时使用了这个模式。



 

## 89、模板引擎原理

板引擎是通过字符串拼接得到的

```js
let template = 'hello <% name %>!'
let template = 'hello ' + name + '!'
12
```

字符串是通过`new Function`执行的

```js
let name = 'world'
let template = `
  let str = 'hello ' +  name  + '!'
  return str
`
let fn = new Function('name', template)
console.log(fn(name)) // hello world!
1234567
```

将模板转换为字符串并通过函数执行返回

```js
let template = 'hello <% name %>!'
let name = 'world'
function compile (template) {
  let html = template.replace(/<%([\s\S]+?)%>/g, (match, code) => {
    return `' + ${code} + '`
  })
  html = `let str = '${html}'; return str`
  return new Function('name', html)
}
let str = compile(template)
console.log(str(name)) // hello world!
1234567891011
```

> 函数只能接收一个`name`变量作为参数，功能太单一了，一般会通过对象来传参，`with`来减少变量访问。

with功能

```js
let params = {
  name: '张三',
  age: 18
}
let str = ''
with (params) {
  str = `用户${name}的年龄是${age}岁`
}
console.log(str) // 用户张三的年龄是18岁
123456789
```

实现简单的模板引擎

```js
let template = 'hello <% name %>!'
let name = 'world'
function compile (template) {
  let html = template.replace(/<%([\s\S]+?)%>/g, (match, code) => {
    return `' + ${code.trim()} + '`
  })
  html = `'${html}'`
  html = `let str = ''; with (params) { str = ${html}; } return str`
  return new Function('params', html)
}
let str = compile(template)
console.log(str({ name })) // hello world!
```

## 90、 map和foreach的区别

- 都是循环遍历数组中的每一项
- forEach和map方法里每次执行匿名函数都支持3个参数，参数分别是item（当前每一项）、index（索引值）、arr（原数组）
- 匿名函数中的this都是指向window
- 只能遍历数组

```
array.map(function(item,index,arr){},this)
Array.forEach(function(item,index,arr){},this)
```

**二、区别：**

**1.forEach()**

 没有返回值。

**2.map()** 

有返回值，可以return 出来。

```
var arr = [1,23,3];



arr.map(function(value,index,array){



 



　　//do something



 



　　return XXX



 



})
```

###  

## 91、es6的新特性

1.不一样的变量声明：const和let

ES6推荐使用let声明局部变量，相比之前的var（无论声明在何处，都会被视为声明在函数的最顶部）
 let和var声明的区别：



```jsx
var x = '全局变量';
{
  let x = '局部变量';
  console.log(x); // 局部变量
}
console.log(x); // 全局变量
```

let表示声明变量，而const表示声明常量，两者都为块级作用域；const 声明的变量都会被认为是常量，意思就是它的值被设置完成后就不能再修改了：



```cpp
const a = 1
a = 0 //报错
```

如果const的是一个对象，对象所包含的值是可以被修改的。抽象一点儿说，就是对象所指向的地址没有变就行：



```csharp
const student = { name: 'cc' }

student.name = 'yy';// 不报错
student  = { name: 'yy' };// 报错
```

有几个点需要注意：

> - let 关键词声明的变量不具备变量提升（hoisting）特性
> - let 和 const 声明只在最靠近的一个块中（花括号内）有效
> - 当使用常量 const 声明时，请使用大写变量，如：CAPITAL_CASING
> - const 在声明时必须被赋值

2.模板字符串

在ES6之前，我们往往这么处理模板字符串：
 通过“\”和“+”来构建模板



```jsx
$("body").html("This demonstrates the output of HTML \
content to the page, including student's\
" + name + ", " + seatNumber + ", " + sex + " and so on.");
```

而对ES6来说

1. 基本的字符串格式化。将表达式嵌入字符串中进行拼接。用${}来界定；
2. ES6反引号(``)直接搞定；



```jsx
$("body").html(`This demonstrates the output of HTML content to the page, 
including student's ${name}, ${seatNumber}, ${sex} and so on.`);
```

3.箭头函数（Arrow Functions）

> ES6 中，箭头函数就是函数的一种简写形式，使用括号包裹参数，跟随一个 =>，紧接着是函数体；

箭头函数最直观的三个特点。

> - 不需要 function 关键字来创建函数
> - 省略 return 关键字
> - 继承当前上下文的 this 关键字



```jsx
// ES5
var add = function (a, b) {
    return a + b;
};
// 使用箭头函数
var add = (a, b) => a + b;

// ES5
[1,2,3].map((function(x){
    return x + 1;
}).bind(this));
    
// 使用箭头函数
[1,2,3].map(x => x + 1);
```

> 细节：当你的函数有且仅有一个参数的时候，是可以省略掉括号的。当你函数返回有且仅有一个表达式的时候可以省略{} 和 return；

4. 函数的参数默认值

在ES6之前，我们往往这样定义参数的默认值：



```jsx
// ES6之前，当未传入参数时，text = 'default'；
function printText(text) {
    text = text || 'default';
    console.log(text);
}

// ES6；
function printText(text = 'default') {
    console.log(text);
}

printText('hello'); // hello
printText();// default
```

5.Spread / Rest 操作符

> Spread / Rest 操作符指的是 ...，具体是 Spread 还是 Rest 需要看上下文语境。

当被用于迭代器中时，它是一个 Spread 操作符：



```jsx
function foo(x,y,z) {
  console.log(x,y,z);
}
 
let arr = [1,2,3];
foo(...arr); // 1 2 3
```

当被用于函数传参时，是一个 Rest 操作符：当被用于函数传参时，是一个 Rest 操作符：



```jsx
function foo(...args) {
  console.log(args);
}
foo( 1, 2, 3, 4, 5); // [1, 2, 3, 4, 5]
```

6.二进制和八进制字面量

> ES6 支持二进制和八进制的字面量，通过在数字前面添加 0o 或者0O 即可将其转换为八进制值：



```jsx
let oValue = 0o10;
console.log(oValue); // 8
 
let bValue = 0b10; // 二进制使用 `0b` 或者 `0B`
console.log(bValue); // 2
```

7.对象和数组解构



```jsx
// 对象
const student = {
    name: 'Sam',
    age: 22,
    sex: '男'
}
// 数组
// const student = ['Sam', 22, '男'];

// ES5；
const name = student.name;
const age = student.age;
const sex = student.sex;
console.log(name + ' --- ' + age + ' --- ' + sex);

// ES6
const { name, age, sex } = student;
console.log(name + ' --- ' + age + ' --- ' + sex);
```

8.对象超类

ES6 允许在对象中使用 super 方法：



```jsx
var parent = {
  foo() {
    console.log("Hello from the Parent");
  }
}
 
var child = {
  foo() {
    super.foo();
    console.log("Hello from the Child");
  }
}
 
Object.setPrototypeOf(child, parent);
child.foo(); // Hello from the Parent
             // Hello from the Child
```

9.for...of 和 for...in

for...of 用于遍历一个迭代器，如数组：



```jsx
let letters = ['a', 'b', 'c'];
letters.size = 3;
for (let letter of letters) {
  console.log(letter);
}
// 结果: a, b, c
```

for...in 用来遍历对象中的属性：



```jsx
 let stus = ["Sam", "22", "男"];
 for (let stu in stus) {
   console.log(stus[stu]);
  }
// 结果: Sam, 22, 男
```

10.ES6中的类

ES6 中支持 class 语法，不过，ES6的class不是新的对象继承模型，它只是原型链的语法糖表现形式。

函数中使用 static 关键词定义构造函数的的方法和属性：



```jsx
class Student {
  constructor() {
    console.log("I'm a student.");
  }
 
  study() {
    console.log('study!');
  }
 
  static read() {
    console.log("Reading Now.");
  }
}
 
console.log(typeof Student); // function
let stu = new Student(); // "I'm a student."
stu.study(); // "study!"
stu.read(); // "Reading Now."
```

类中的继承和超集：



```jsx
class Phone {
  constructor() {
    console.log("I'm a phone.");
  }
}
 
class MI extends Phone {
  constructor() {
    super();
    console.log("I'm a phone designed by xiaomi");
  }
}
 
let mi8 = new MI();
```

> extends 允许一个子类继承父类，需要注意的是，子类的constructor 函数中需要执行 super() 函数。
>  当然，你也可以在子类方法中调用父类的方法，如super.parentMethodName()。
>  在 这里 阅读更多关于类的介绍。

> 有几点值得注意的是：
>
> - 类的声明不会提升（hoisting)，如果你要使用某个 Class，那你必须在使用之前定义它，否则会抛出一个 ReferenceError 的错误
> - 在类中定义函数不需要使用 function 关键词

 

## 92、如何向 Array 对象添加自定义方法，让下面的代码可以运行？

```jsx
var arr = [1, 2, 3, 4, 5];
var avg = arr.average();
console.log(avg);
```

JavaScript 不是基于类的，但它是基于原型的语言。这意味着每个对象都链接到另一个对象（也就是对象的原型），并继承原型对象的方法。你可以跟踪每个对象的原型链，直到到达没有原型的 null 对象。我们需要通过修改 Array 原型来向全局 Array 对象添加方法。



```jsx
Array.prototype.average = function() {
 // 计算 sum 的值
 var sum = this.reduce(function(prev, cur) { return prev + cur; });
 // 将 sum 除以元素个数并返回
 return sum / this.length;
}
var arr = [1, 2, 3, 4, 5];
var avg = arr.average();
console.log(avg); // => 3
```

 

## 93、0.1+0.2等不等于0.3？自己封装一个让他们相等的方法

```
console.log(0.1+0.2===0.3)// true or false??
在正常的数学逻辑思维中，0.1+0.2=0.3这个逻辑是正确的，但是在JavaScript中0.1+0.2！==0.3，这是为什么呢？这个问题也会偶尔被用来当做面试题来考查面试者对JavaScript的数值的理解程度。



 



　　在JavaScript中的二进制的浮点数0.1和0.2并不是十分精确，在他们相加的结果并非正好等于0.3，而是一个比较接近的数字 0.30000000000000004 ，所以条件判断结果为false。



 



那么应该怎样来解决0.1+0.2等于0.3呢? 最好的方法是设置一个误差范围值，通常称为”机器精度“，而对于Javascript来说，这个值通常是2^-52,而在ES6中，已经为我们提供了这样一个



 



属性：Number.EPSILON，而这个值正等于2^-52。这个值非常非常小，在底层计算机已经帮我们运算好，并且无限接近0，但不等于0,。这个时候我们只要判断(0.1+0.2)-0.3小于



 



Number.EPSILON，在这个误差的范围内就可以判定0.1+0.2===0.3为true。
function numbersequal(a,b){ return Math.abs(a-b)<Number.EPSILON;



} 



var a=0.1+0.2， b=0.3;



console.log(numbersequal(a,b)); //true



 



但是这里要考虑兼容性的问题了，在chrome中支持这个属性，但是IE并不支持(笔者的版本是IE10不兼容)，所以我们还要解决IE的不兼容问题。
Number.EPSILON=(function(){   //解决兼容性问题



        return Number.EPSILON?Number.EPSILON:Math.pow(2,-52);



      })();



//上面是一个自调用函数，当JS文件刚加载到内存中，就会去判断并返回一个结果，相比if(!Number.EPSILON){



  //   Number.EPSILON=Math.pow(2,-52);



  //}这种代码更节约性能，也更美观。



function numbersequal(a,b){ 



    return Math.abs(a-b)<Number.EPSILON;



  }



//接下来再判断   



    var a=0.1+0.2, b=0.3;



  console.log(numbersequal(a,b)); //这里就为true了
```

## 94、跨域是什么？有哪些解决跨域的方法和方案？

CORS  PROXY  JSONP

## 95、什么是函数式编程？什么的声明式编程？

什么是声明式编程？一般来说我们对于声明式的理解都是相对于命令式（imperative）而言的。图灵教会了我们imperative的真谛，并赋予了它数学意义上的精确定义：一台有状态的机器，根据明确的指令（instruction）一步步的执行。而所谓的声明式，它可以看作是命令式的反面。曾有人言：一切非imperative，皆是declarative。从这个意义上说，越是偏离图灵机的图像越远的，就越是声明式的。

所以，函数式编程（Functional Programming）是声明式的，因为它不使用可变状态，也不需要指定任何的执行顺序关系（可以假定所有的函数都是同时执行的，因为存在引用透明性，所谓的参数和变量都只是一堆符号的别名而已）。逻辑式编程（Logical Programming）也是声明式的，因为我们只需要通过facts和rules描述我们所需要解决的问题，具体的求解路径由编译器和程序运行时自动决定。

## 96、super() 是否必须执行？不执行怎么让它不报错？

```
构造函数有什么作用？创建类的对象，只有对象才能调用类的属性和方法。当子类继承父类的属性和方法时，如何去调用父类的构造函数？Super()当父类构造函数是空参数时，系统会默认添加Super(),此时你可以省略。如果父类是非参数时，必须要添加Super(),初始化父类，初始化父类，子类才能调用父类属性和方法。这就是Super()的作用。
```

## 97、什么是 JavaScript 中的提升操作？

提升（hoisting）是 JavaScript 解释器将所有变量和函数声明移动到当前作用域顶部的操作。有两种类型的提升：

- 变量提升——非常少见
- 函数提升——常见

无论 var（或函数声明）出现在作用域的什么地方，它都属于整个作用域，并且可以在该作用域内的任何地方访问它。



```jsx
var a = 2;
foo(); // 因为`foo()`声明被"提升"，所以可调用
function foo() {
 a = 3;
 console.log( a ); // 3
 var a; // 声明被"提升"到 foo() 的顶部
}
console.log( a ); // 2
```



 


## 98、 以下代码输出的结果是什么？

```undefined
0.1 + 0.2 === 0.3
```

这段代码的输出是 false，这是由浮点数内部表示导致的。0.1 + 0.2 并不刚好等于 0.3，实际结果是 0.30000000000000004。解决这个问题的一个办法是在对小数进行算术运算时对结果进行舍入。



 

## 99、图片懒加载怎么实现 

**一、什么是懒加载**

将图片src先赋值为一张默认图片，当用户滚动滚动条到可视区域图片的时候，再去加载后续真正的图片

如果用户只对第一张图片感兴趣，那剩余的图片请求就可以节省了

**二、为什么要引入懒加载**

懒加载（LazyLoad）是前端优化的一种有效方式，极大的提升用户体验。图片一直是页面加载的流浪大户，现在一张图片几兆已经是很正常的事，远远大于代码的大小。倘若一次ajax请求10张图片的地址，一次性把10张图片都加载出来，肯定是不合理的。

**三、懒加载实现的原理**

先将img标签中的src链接设置为空，将真正的图片链接放在自定义属性（data-src），当js监听到图片元素进入到可视窗口的时候，将自定义属性中的地址存储到src中，达到懒加载的效果。

**四、懒加载中涉及的属性**

1 、document.documentElement.clientHeight;  //表示浏览器可见区域高度：

   document.body.clientHeight //是整个页面内容的高度，而非浏览器可见区域的高度

2 、document.documentElement.scrollTop;  //滚动条 已滚动的高度：

​    chrome 中 document.body.scrollTop //滚动条 滚过的高度

​    那么要得到滚动条的高度：有一个技巧：

　　var scrollTop=document.body.scrollTop || document.documentElement.scrollTop;

　　这两个值总会有一个恒为0，所以不用担心会对真正的scrollTop造成影响。一点小技巧，但很实用。

3、 offsetTop、offsetLeft

　　obj.offsetTop 指 obj 距离上方或上层控件的位置，整型，单位像素。

　　obj.offsetLeft 指 obj 距离左方或上层控件的位置，整型，单位像素。

　　obj.offsetWidth 指 obj 控件自身的宽度，整型，单位像素。

　　obj.offsetHeight 指 obj 控件自身的高度，整型，单位像素。

   offsetParent 不同于parentNode ,offsetParent 返回的是在结构层次中与这个元素最近的position为absolute\relative\static的元素或者body

具体滚动时涉及的属性值，请参考https://blog.csdn.net/zh_rey/article/details/78967174非常详细

**五、懒加载的实现**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Lazyload 2</title>
    <style>
    img {
        display: block;
        margin-bottom: 50px;
        height: 200px;
    }
    </style>
</head>
<body>
    <img src="images/loading.gif" data-src="images/1.png">
    <img src="images/loading.gif" data-src="images/2.png">
    <img src="images/loading.gif" data-src="images/3.png">
    <img src="images/loading.gif" data-src="images/4.png">
    <img src="images/loading.gif" data-src="images/5.png">
    <img src="images/loading.gif" data-src="images/6.png">
    <img src="images/loading.gif" data-src="images/7.png">
    <img src="images/loading.gif" data-src="images/8.png">
    <img src="images/loading.gif" data-src="images/9.png">
    <img src="images/loading.gif" data-src="images/10.png">
    <img src="images/loading.gif" data-src="images/11.png">
    <img src="images/loading.gif" data-src="images/12.png">
    <script>
    function throttle(fn, delay, atleast) {//函数绑定在 scroll 事件上，当页面滚动时，避免函数被高频触发，
        var timeout = null,//进行去抖处理
        startTime = new Date();
        return function() {
           　　var curTime = new Date();
        　　　　clearTimeout(timeout);
       　　　　 if(curTime - startTime >= atleast) {
        　　    fn();
           　　 startTime = curTime;
        　　　　}else {
           　　　　 timeout = setTimeout(fn, delay);
        　　　　}
        }
    }
    function lazyload() {
        var images = document.getElementsByTagName('img');
        var len    = images.length;
        var n      = 0;      //存储图片加载到的位置，避免每次都从第一张图片开始遍历        
        return function() {
        　　　　var seeHeight = document.documentElement.clientHeight;
       　　　　 var scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
     　　　　   for(var i = n; i < len; i++) {
           　　　　 if(images[i].offsetTop < seeHeight + scrollTop) {
               　　　　 if(images[i].getAttribute('src') === 'images/loading.gif') {
               　　　　　　  images[i].src = images[i].getAttribute('data-src');
               　　　　 }
           　　　　　　 n = n + 1;
            　　　　}
        　　　　}
        }
    }
    var loadImages = lazyload();
    loadImages();          //初始化首页的页面图片
    window.addEventListener('scroll', throttle(loadImages, 500, 1000), false);
　　//函数节流（throttle）与函数去抖（debounce）处理,
//500ms 的延迟，和 1000ms 的间隔，当超过 1000ms 未触发该函数，则立即执行该函数，不然则延迟 500ms 执行该函数
    </script>
</body>
</html>
```

## 100、for-in 循环会遍历出原型上的属性吗？ 

1.使用 for in 循环遍历对象的属性时，原型链上的所有属性都将被访问：

```
Object.prototype.say="cgl";   // 修改Object.prototype  
    var person ={ age: 18 };
    for (var key in person) {
        console.log(key, person[key]);//这里用person.key得不到对象key的值，用person[key] 或者 eval("person."+key);
    }   //结果： age 18 
                say cgl
123456
```

2.只遍历对象自身的属性，而不遍历继承于原型链上的属性，使用hasOwnProperty 方法过滤一下。

```
Object.prototype.say="cgl";
    var person ={
        age: 18
    };
    for (var key in person) {
        if(person.hasOwnProperty(key)){
            console.log(key, eval("person."+key));
        }
    }  
123456789
```

二.Object.keys()

Object.keys() 方法会返回一个由给定对象的自身可枚举属性组成的数组，数组中属性名的排列顺序和使用 for…in 循环遍历该对象时返回的顺序一致 （两者的主要区别是 一个 for-in 循环还会枚举其原型链上的属性）。返回值是这个对象的所有可枚举属性组成的字符串数组。

```
Object.prototype.say="cgl";
var person ={ age: 18};
console.log(Object.keys(person));//结果  ["age"] 
123
```

小技巧：object对象没有length属性，可以通过Object.keys(person).length，来获取person的长度了。

## 101、url从输入到渲染页面的全过程

　从输入URL到页面加载的主干流程如下：

　　1、浏览器构建HTTP Request请求

　　2、网络传输

　　3、服务器构建HTTP Response 响应

　　4、网络传输

　　5、浏览器渲染页面


## 102、http状态码有哪些

　200 OK 

　　　　*请求正常处理完毕*

　　204 No Content 

　　　　请求成功处理，没有实体的主体返回

301 Moved Permanently 
　　　　永久重定向，资源已永久分配新URI

　　302 Found 
　　　　临时重定向，资源已临时分配新URI

　　400 Bad Request 

　　　　请求报文语法错误或参数错误

　　401 Unauthorized 

　　　　要通过HTTP认证，或认证失败

　　403 Forbidden 

　　　　请求资源被拒绝

　　404 Not Found 
　　　　无法找到请求资源（服务器无理由拒绝）

　　500 Internal Server Error 
　　　　服务器故障或Web应用故障

　　503 Service Unavailable 
　　　　服务器超负载或停机维护

状态码如图：

　　　　　　![img](https://img2018.cnblogs.com/common/1419718/202001/1419718-20200101202120407-643788690.png)


## 103、call apply bind call和apply哪个性能更好  

call和apply都是function原型上的方法，而每一个函数作为function这个类上的实例可以调取原型上的call和apply,都是用来改变函数中this指向的，
**区别：**
call：传参是一个一个传给函数
apply：把所有要传的参数以数组的形式保存起来
fn.call(obj,10,20,30)
fn.apply(obj,[10,20,30])
**注意：**
bind也是用来改变函数中this指向，bind没有把函数立即执行，只是预先处理改变this
call的性能要比apply好一些，尤其是传递给函数的参数超过3个时所以后期开发的时候，可以使用call多一些
（传参数3个以内的话，call和apply性能差不多，超过3个以上call更好一些）

## 104、ES6箭头函数和普通函数有什么差异

面试中少不了面试官问箭头函数的 this 有何特殊。我们知道虽然 babel 转义后是在外层定义 _this 指向了外层的 this ，然后在传给内层的函数来解决这个事情的

```
function index() {
  let func = () => { console.log(this) }
}

// 根据 babel 官网 https://babel.docschina.org/repl 在线转译成
"use strict";

function index() {
  var _this = this;

  var func = function func() {
    console.log(_this);
  };
}
```

但原生的 ES6 的箭头函数可不是这样。且看 MDN 的描述：

> 箭头函数表达式的语法比函数表达式更简洁，并且没有自己的this，arguments，super或new.target。箭头函数表达式更适用于那些本来需要匿名函数的地方，并且它不能用作构造函数。

总结一下，箭头函数有坑。它并没有 this，就只是在函数执行时，取外部的作用域的 this

然后注意以下陷阱：箭头函数能 new 吗？若是被 babel 转译成了普通函数，new 调用却是没问题；但原生的不能，因为它没有 prototype 属性，new 操作符总需要作用 prototype 的，所以它不能用作构造函数。

```
var Foo = () => {};
var foo = new Foo(); // TypeError: Foo is not a constructor
```

箭头函数没有 prototype 属性

```
var Foo = () => {};
console.log(Foo.prototype); // undefined
```

箭头函数没有 argument

```
var func = () => { console.log(arguments) }
func(1) // Uncaught ReferenceError: arguments is not defined

// 多数情况可以使用剩余参数改写
var func = (...args) => { console.log(args) }
func(1) // [1]
```

## 105、**说说写JavaScript的基本规范？**

1) 不要在同一行声明多个变量
2) 使用 ===或!==来比较true/false或者数值
3) switch必须带有default分支
4) 函数应该有返回值
5) for if else 必须使用大括号
6) 语句结束加分号
7) 命名要有意义，使用驼峰命名法


## 106、**jQuery使用建议**

1) 尽量减少对dom元素的访问和操作
2) 尽量避免给dom元素绑定多个相同类型的事件处理函数，可以将多个相同类型事件
处理函数合并到一个处理函数，通过数据状态来处理分支
3) 尽量避免使用toggle事件

## 107、**Ajax使用**

全称 ： Asynchronous Javascript And XML
所谓异步，就是向服务器发送请求的时候，我们不必等待结果，而是可以同时做其他的事情，等到有了结果它自己会根据设定进行后续操作，与此同时，页面是不会发生整页刷新的，提高了用户体验。
创建Ajax的过程：
1) 创建XMLHttpRequest对象（异步调用对象）

```text
var xhr = new XMLHttpRequest();
```

2) 创建新的Http请求（方法、URL、是否异步）

```text
xhr.open(‘get’,’example.php’,false);
```

3) 设置响应HTTP请求状态变化的函数。
onreadystatechange事件中readyState属性等于4。响应的HTTP状态为200(OK)或者304(Not Modified)。
4) 发送http请求

```text
xhr.send(data);
```

5) 获取异步调用返回的数据
注意：
1) 页面初次加载时，尽量在web服务器一次性输出所有相关的数据，只在页面加载完成之后，用户进行操作时采用ajax进行交互。
2) 同步ajax在IE上会产生页面假死的问题。所以建议采用异步ajax。
3) 尽量减少ajax请求次数
4) ajax安全问题，对于敏感数据在服务器端处理，避免在客户端处理过滤。对于关键业务逻辑代码也必须放在服务器端处理。


## 108、tcp三次握手，四次挥手，可靠传输原理(二面三面都问了)

CP建立连接为什么是三次握手，而不是两次或四次？

TCP，名为传输控制协议，是一种可靠的传输层协议，IP协议号为6。

顺便说一句，原则上任何数据传输都无法确保绝对可靠，三次握手只是确保可靠的基本需要。

举个日常例子，打电话时我们对话如下：

 ![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9QaWFJUWxkWVdaTk1ZV1JGM2FJT1RWcGxjVGhBMjVIdGlhQVJ3T2NmUXJrcHU1bzRiQTdiYURvc09HSmZhRkNpYVhYSXBqdWM2RGxnNk5HT2g2NmlhOFAxOEEvNjQw?x-oss-process=image/format,png)

对应为客户端与服务器之间的通信：

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9QaWFJUWxkWVdaTk1ZV1JGM2FJT1RWcGxjVGhBMjVIdGlhRk5yeG9RMmFwVldSTEFuZ2ljS2ljU0NES0E1SmlhdXpSa2JyUkp4Tzg3NjRlOTFOSFd4Z3M1RXp3LzY0MA?x-oss-process=image/format,png)

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9QaWFJUWxkWVdaTk1ZV1JGM2FJT1RWcGxjVGhBMjVIdGlhaWFGVjBaQnltdEE5dWlhUkpWeHp0N2tKc09hTm9CaWJJSGhveWpOV3VRSGJpYk1UOVowamtyOVRTdy82NDA?x-oss-process=image/format,png)

于是有了如下对话：

我：1+1等于几？

她：2,2+2等于几？

我：4

首先两个人约定协议

1.感觉网络情况不对的时候，任何一方都可以发起询问

2.任何情况下，若发起询问后5秒还没收到回复，则认为网络不通

3.网络不通的情况下等1min路由器之后再发起询问

对于我而言，发起 “1+1等于几”的询问后

\1. 若5s内没有收到回复，则认为网络不通

\2. 若收到回复，则我确认①我能听到她的消息 ②她能听到我的消息，然后回复她的问题的答案

对于她而言，当感觉网络情况不对的时候

\1. 若没有收到我的询问，则她发起询问

\2. 若收到“1+1等于几”，则她确认 ①她可以听到我的消息，然后回复我的问题的答案和她的问题“2，2+2等于几”

\3. 若5s内没有收到我的回复“4”，则她确认 ②我听不见她的消息

\4. 若5s内收到了我的回复“4”，则她确认 ②我可以听见她的消息

这样，如果上面的对话得以完成，就证明双方都可以确认自己可以听到对方的声音，对方也可以听到自己的声音！

这个故事可以解释TCP为什么要三次握手吗 ... 囧

**关于四次挥手**

先由客户端向服务器端发送一个FIN，请求关闭数据传输。

当服务器接收到客户端的FIN时，向客户端发送一个ACK，其中ack的值等于FIN+SEQ

然后服务器向客户端发送一个FIN，告诉客户端应用程序关闭。

当客户端收到服务器端的FIN是，回复一个ACK给服务器端。其中ack的值等于FIN+SEQ

 ![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9QaWFJUWxkWVdaTk1ZV1JGM2FJT1RWcGxjVGhBMjVIdGlhcktzS1ZpY3RjUkc2MndnaWN6OEcxUHZodXVpY2Y2QTNYcnE0UUFZVEpkVEsxaWFGeTBKMXNNcFl5dy82NDA?x-oss-process=image/format,png)

 

**为什么要4次挥手？**

确保数据能够完整传输。

当被动方收到主动方的FIN报文通知时，它仅仅表示主动方没有数据再发送给被动方了。

但未必被动方所有的数据都完整的发送给了主动方，所以被动方不会马上关闭SOCKET,它可能还需要发送一些数据给主动方后，

再发送FIN报文给主动方，告诉主动方同意关闭连接，所以这里的ACK报文和FIN报文多数情况下都是分开发送的。

**一、TCP报文格式**

TCP报文格式图：

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9QaWFJUWxkWVdaTk1ZV1JGM2FJT1RWcGxjVGhBMjVIdGlhV0tYYWIxdHZmZm5QTDd3VkJYZk96RDZzTnlnMzBuOUlmb3lmWGZGNm9xNDY2WXhWeVRPYVdRLzY0MA?x-oss-process=image/format,png) 

上图中有几个字段需要重点介绍下：

 （1）序号：Seq序号，占32位，用来标识从TCP源端向目的端发送的字节流，发起方发送数据时对此进行标记。

 （2）确认序号：Ack序号，占32位，只有ACK标志位为1时，确认序号字段才有效，Ack=Seq+1。

 （3）标志位：共6个，即URG、ACK、PSH、RST、SYN、FIN等，具体含义如下：

 （A）URG：紧急指针（urgent pointer）有效。

 （B）ACK：确认序号有效。

 （C）PSH：接收方应该尽快将这个报文交给应用层。

 （D）RST：重置连接。

 （E）SYN：发起一个新连接。

 （F）FIN：释放一个连接。

 需要注意的是：

 （A）不要将确认序号Ack与标志位中的ACK搞混了。

 （B）确认方Ack=发起方Req+1，两端配对。 

**二、三次握手**

TCP(Transmission Control Protocol)　传输控制协议

TCP是主机对主机层的传输控制协议，提供可靠的连接服务，采用三次握手确认建立一个连接

位码即tcp标志位,有6种标示:

**SYN(synchronous建立联机)**

**ACK(acknowledgement 确认)**

**PSH(push传送)**

**FIN(finish结束)**

**RST(reset重置)**

**URG(urgent紧急)**

Sequence number(顺序号码)

Acknowledge number(确认号码) 

establish  建立，创建 

所谓**三次握手**（Three-Way Handshake）即建立TCP连接，**是指建立一个TCP连接时，需要客户端和服务端总共发送3个包以确认连接的建立**。在socket编程中，这一过程由客户端执行connect来触发，整个流程如下图所示：


![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9QaWFJUWxkWVdaTk1ZV1JGM2FJT1RWcGxjVGhBMjVIdGlhNUk1WERtVVJTWjZXdEdqUnBsTzVZWjBpY3g2dnNlYTVHMVZvMmdTM0liZVhCTGliWXkzaWNRUmh3LzY0MA?x-oss-process=image/format,png)


 （1）第一次握手：Client将标志位SYN置为1，随机产生一个值seq=J，并将该数据包发送给Server，**Client进入SYN_SENT状态**，等待Server确认。

 （2）第二次握手：Server收到数据包后由标志位SYN=1知道Client请求建立连接，Server将标志位SYN和ACK都置为1，ack (number )=J+1，随机产生一个值seq=K，并将该数据包发送给Client以确认连接请求，**Server进入SYN_RCVD状态**。

 （3）第三次握手：Client收到确认后，检查ack是否为J+1，ACK是否为1，如果正确则将标志位ACK置为1，ack=K+1，并将该数据包发送给Server，Server检查ack是否为K+1，ACK是否为1，**如果正确则连接建立成功，Client和Server进入ESTABLISHED状态**，完成三次握手，随后Client与Server之间可以开始传输数据了。


![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X2pwZy9QaWFJUWxkWVdaTk1ZV1JGM2FJT1RWcGxjVGhBMjVIdGlhdkNEZFJZN09zMlhLaWFGRWliTW5zdGhYYXNISFhrdFl3enlQd0U4VHVMUm1FTEc0YWNLT0ZMYUEvNjQw?x-oss-process=image/format,png)


 **SYN攻击**：

在三次握手过程中，**Server发送SYN-ACK之后，收到Client的ACK之前的TCP连接称为半连接（half-open connect），此时Server处于SYN_RCVD状态**，当收到ACK后，Server转入ESTABLISHED状态。

**SYN攻击就是Client在短时间内伪造大量不存在的IP地址，并向Server不断地发送SYN包，Server回复确认包，并等待Client的确认，由于源地址是不存在的，因此，Server需要不断重发直至超时**，这些**伪造的SYN包将长时间占用未连接队列，导致正常的SYN请求因为队列满而被丢弃**，从而引起网络堵塞甚至系统瘫痪。

SYN攻击时一种典型的DDOS攻击，检测SYN攻击的方式非常简单，即当Server上有大量半连接状态且源IP地址是随机的，则可以断定遭到SYN攻击了，使用如下命令可以让之现行：

 \#netstat -nap | grep SYN_RECV 

**三、四次挥手**

三次握手耳熟能详，四次挥手估计就..所谓四次挥手（Four-Way Wavehand）即终止TCP连接，就是指断开一个TCP连接时，需要客户端和服务端总共发送4个包以确认连接的断开。在socket编程中，这一过程由客户端或服务端任一方执行close来触发，整个流程如下图所示：


![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9QaWFJUWxkWVdaTk1ZV1JGM2FJT1RWcGxjVGhBMjVIdGlhb1RIaWE2NkNqMGtmVjA1WUpveFF2d09KcjMxbVh1Ulg0d3JPNk1FVUxkMVo5ekU3R2oyWlV5dy82NDA?x-oss-process=image/format,png)


由于TCP连接时全双工的，因此，每个方向都必须要单独进行关闭，这一原则是当一方完成数据发送任务后，发送一个FIN来终止这一方向的连接，收到一个FIN只是意味着这一方向上没有数据流动了，即不会再收到数据了，但是在这个TCP连接上仍然能够发送数据，直到这一方向也发送了FIN。首先进行关闭的一方将执行主动关闭，而另一方则执行被动关闭，上图描述的即是如此。

 （1）第一次挥手：Client发送一个FIN，用来关闭Client到Server的数据传送，Client进入FIN_WAIT_1状态。

 （2）第二次挥手：Server收到FIN后，发送一个ACK给Client，确认序号为收到序号+1（与SYN相同，一个FIN占用一个序号），Server进入CLOSE_WAIT状态。

 （3）第三次挥手：Server发送一个FIN，用来关闭Server到Client的数据传送，Server进入LAST_ACK状态。

 （4）第四次挥手：Client收到FIN后，Client进入TIME_WAIT状态，接着发送一个ACK给Server，确认序号为收到序号+1，Server进入CLOSED状态，完成四次挥手。


![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X2pwZy9QaWFJUWxkWVdaTk1ZV1JGM2FJT1RWcGxjVGhBMjVIdGlhRzNHSkVQdkE0bmlhemhiWHBHRzZpYTZEZnlwVTdaSG1rck5LWm4ySVhoYWFpY21kSVVpYWRxSlppYXcvNjQw?x-oss-process=image/format,png)


上面是一方主动关闭，另一方被动关闭的情况，实际中还会出现同时发起主动关闭的情况，具体流程如下图：


![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9QaWFJUWxkWVdaTk1ZV1JGM2FJT1RWcGxjVGhBMjVIdGlhZ24yR1Zoc2lhVXYyRExlN1VCeVFKNWFyaWNLVXZDcGliU2ljR01KOFlPNmE2MVQ5ZW9LWjhBZnVyQS82NDA?x-oss-process=image/format,png)

流程和状态在上图中已经很明了了，在此不再赘述，可以参考前面的四次挥手解析步骤。

**四、附注**

关于三次握手与四次挥手通常都会有典型的面试题，在此提出供有需求的XDJM们参考：

 （1）三次握手是什么或者流程？四次握手呢？答案前面分析就是。

 （2）为什么建立连接是三次握手，而关闭连接却是四次挥手呢？

这是因为**服务端在LISTEN状态下，收到建立连接请求的SYN报文后，把ACK和SYN放在一个报文里发送给客户端。****而关闭连接时，当收到对方的FIN报文时，仅仅表示对方不再发送数据了但是还能接收数据**，己方也未必全部数据都发送给对方了，所以己方可以立即close，也可以发送一些数据给对方后，再发送FIN报文给对方来表示同意现在关闭连接，因此，己方ACK和FIN一般都会分开发送。（来自Champin）

## 109、进程线程区别

1、功能不同

进程是计算机中的程序关于某数据集合上的一次运行活动，是系统进行资bai源分配和调度的基本单位，是操作系统结构的基础。

线程是操作系统能够进行运算调度的最小单位。它被包含在进程之中，是进程中的实际运作单位。

2、工作原理不同

在早期面向进程设计的计算机结构中，进程是程序的基本执行实体；在当代面向线程设计的计算机结构中，进程是线程的容器。程序是指令、数据及其组织形式的描述，进程是程序的实体。

线程是独立调度和分派的基本单位。线程可以为操作系统内核调度的内核线程，如Win32线程；由用户进程自行调度的用户线程，如Linux平台的POSIX Thread；或者由内核与用户进程，如Windows 7的线程，进行混合调度。

3、作用不同

进程是操作系统中最基本、重要的概念。是多道程序系统出现后，为了刻画系统内部出现的动态情况，描述系统内部各道程序的活动规律引进的一个概念,所有多道程序设计操作系统都建立在进程的基础上。

通常在一个进程中可以包含若干个线程，它们可以利用进程所拥有的资源。在引入线程的操作系统中，通常都是把进程作为分配资源的基本单位，而把线程作为独立运行和独立调度的基本单位。

## 110、禁止事件冒泡，禁止默认事件

1.event.stopPropagation()方法 这是阻止事件的冒泡方法,不让事件向documen上蔓延,但是默认事件任然会执行,当你掉用这个方法的时候,如果点击一个...

2.event.preventDefault()方法 这是阻止默认事件的方法,调用此方法是...

3.return false ; 这个方法比较暴力,他会同事阻止事件冒泡也会阻止默认事件 

## 111、import export commonJs对比区别

一、标准不同

CommonJS 中的 require/exports 和比ES6 中的 import/export出现得早，node.js是他的实现；CommonJS 中的 require/exports 是老的标准，ES6 中的 import/export要比它更加权威。es6通过babel转化为es5执行，所以写的import/export是通过babel转换为require/exports执行的。二者其实都可以看做是输出一个对象和引入一个对象并使用其中的属性或方法。
二、书写格式不同

- require的调用时间为运行时调用，所以require可以出现在文件的任何地方。动态加载
- import是编译时调用，所以必须放在文件头部。静态加载
- export default导出的东西只能用import导入。
- module.exports和exports的东西可以以任何一种方式导入。

这个太简单了，不作鳌述。后面补上
三、加载机制不同



## 112、为什么javascript是单线程

单线程：同一个时间只能做一件事

JavaScript的单线程，与他的用途有关。作为浏览器脚本语言，JavaScript的主要用途是与用户互动，以及操作DOM。这就决定了他只能是单线程，否则会带来很复杂的同步问题。比如，假定JavaScript同时有两个线程，一个线程在某个DOM节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？

为了利用多核CPU的计算能力，HTML5提出Web Worker 标准，允许JavaScript脚本创建多个线程，但是子线程完全受主线程控制，且不得操作DOM。所以，这个新标准并没有改变JavaScript单线程的本质。

JavaScript为什么需要异步

如果JS中不存在异步,只能自上而下执行，如果上一行解析时间很长，那么下面的代码就会被阻塞。对于用户而言,阻塞就意味着"卡死"，这样就导致了很差的用户体验。

单线程如何实现异步

通过事件循环（Event loop）来实现的。



## 113、使用箭头函数应该注意什么



箭头函数有几个使用注意点。
（1）函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。
（2）不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。
（3）不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用Rest参数代替。
（4）不可以使用yield命令，因此箭头函数不能用作Generator函数。





## 114、hash chunkhash contenthash三者区别



一、hash（所有文件哈希值相同，只要改变内容跟之前的不一致，所有哈希值都改变，没有做到缓存意义）

hash是跟整个项目的构建相关，构建生成的文件hash值都是一样的，所以hash计算是跟整个项目的构建相关，同一次构建过程中生成的hash都是一样的，只要项目里有文件更改，整个项目构建的hash值都会更改。

如果出口是hash，那么一旦针对项目中任何一个文件的修改，都会构建整个项目，重新获取hash值，缓存的目的将失效。

二、chunkhash（同一个模块，就算将js和css分离，其哈希值也是相同的，修改一处，js和css哈希值都会变，同hash，没有做到缓存意义）

它根据不同的入口文件(Entry)进行依赖文件解析、构建对应的chunk，生成对应的hash值。我们在生产环境里把一些公共库和程序入口文件区分开，单独打包构建，接着我们采用chunkhash的方式生成hash值，那么只要我们不改动公共库的代码，就可以保证其hash值不会受影响。

由于采用chunkhash，所以项目主入口文件main.js及其对应的依赖文件main.css由于被打包在同一个模块，所以共用相同的chunkhash。
 这样就会有个问题，只要对应css或则js改变，与其关联的文件hash值也会改变，但其内容并没有改变，所以没有达到缓存意义。

三、contenthash（只要文件内容不一样，产生的哈希值就不一样）

contenthash表示由文件内容产生的hash值，内容不同产生的contenthash值也不一样。在项目中，通常做法是把项目中css都抽离出对应的css文件来加以引用。

**所以css文件最好使用contenthash。**



 



## 115、**栈和堆的区别？**

栈（stack）：由编译器自动分配释放，存放函数的参数值，局部变量等；
堆（heap）：一般由程序员分配释放，若程序员不释放，程序结束时可能由操作系统释放。





## 116、**Javascript实现继承的几种方式**



工厂方法

```
function creatPerson(name, age) {
            
    var obj = new Object();

    obj.name = name;
    obj.age = age;

    obj.sayName = function() {
        window.alert(this.name);
    };
            
    return obj;
}
```

构造函数方法

```
function Person(name, age) {

    this.name = name;
    this.age = age;

    this.sayName = function() {
        window.alert(this.name);
    };
}
```

原型方法

```
function Person() {
        
}
        
Person.prototype = {
    constructor : Person,
    name : "Ning",
    age : "23",
    sayName : function() {
        window.alert(this.name);
    }
};
```

大家可以看到这种方法有缺陷，类里属性的值都是在原型里给定的。

组合使用构造函数和原型方法（使用最广）

```
function Person(name, age) {
    this.name = name;
    this.age = age;
}
        
Person.prototype = {
    constructor : Person, 
    sayName : function() {
        window.alert(this.name);
    }
};
```

将构造函数方法和原型方法结合使用是目前最常用的定义类的方法。这种方法的好处是实现了属性定义和方法定义的分离。比如我可以创建两个对象`person1`和`person2`，它们分别传入各自的`name`值和`age`值，但`sayName()`方法可以同时使用原型里定义的。

JavaScript实现继承的3种方法

借用构造函数法（又叫经典继承）

```
function SuperType(name) {

    this.name = name;

    this.sayName = function() {
        window.alert(this.name);
    };
}
        
function SubType(name, age) {

    SuperType.call(this, name); //在这里借用了父类的构造函数

    this.age = age;
}
```

对象冒充

```
function SuperType(name) {

    this.name = name;
    
    this.sayName = function() {
        window.alert(this.name);
    };
}
        
function SubType(name, age) {

    this.supertype = SuperType; //在这里使用了对象冒充
    this.supertype(name);

    this.age = age;
}
```

组合继承（最常用）

```
function SuperType(name) {

    this.name = name;

}
        
SuperType.prototype = {
        
    sayName : function() {
        window.alert(this.name);
    }
};
        
function SubType(name, age) {
    
    SuperType.call(this, name); //在这里继承属性
    this.age = age;
}

SubType.prototype = new SuperType(); //这里继承方法
```

组合继承的方法是对应着我们用‘组合使用构造函数和原型方法’定义父类的一种继承方法。同样的，我们的属性和方法是分开继承的。

 



## 117、**谈谈this的理解**



1) this总是指向函数的直接调用者（而非间接调用者）
2) 如果有new关键字，this指向new出来的那个对象
3) 在事件中，this指向目标元素，特殊的是IE的attachEvent中的this总是指向全局对象window。

## 118、map fillter reduce 各自有什么作用



`map` 作用是生成一个新数组，遍历原数组，将每个元素拿出来做一些变换然后放入到新的数组中。

```javascript
[1, 2, 3].map(v => v + 1) // -> [2, 3, 4]
```

另外 `map` 的回调函数接受三个参数，分别是当前索引元素，索引，原数组

```javascript
['1','2','3'].map(parseInt)
```

- 第一轮遍历 `parseInt('1', 0) -> 1`

第二轮遍历 `parseInt('2', 1) -> NaN`

- 第三轮遍历 `parseInt('3', 2) -> NaN`

`filter` 的作用也是生成一个新数组，在遍历数组的时候将返回值为 `true` 的元素放入新数组，我们可以利用这个函数删除一些不需要的元素

```javascript
let array = [1, 2, 4, 6]



let newArray = array.filter(item => item !== 6)



console.log(newArray) // [1, 2, 4]
```

和 `map` 一样，`filter` 的回调函数也接受三个参数，用处也相同。

最后我们来讲解 `reduce` 这块的内容，同时也是最难理解的一块内容。`reduce` 可以将数组中的元素通过回调函数最终转换为一个值。

如果我们想实现一个功能将函数里的元素全部相加得到一个值，可能会这样写代码

```javascript
const arr = [1, 2, 3]



let total = 0



for (let i = 0; i < arr.length; i++) {



  total += arr[i]



}



console.log(total) //6 
```

但是如果我们使用 `reduce` 的话就可以将遍历部分的代码优化为一行代码

```javascript
const arr = [1,2,3]



const sum = arr.reduce((acc,current)=>acc + current,0)



console.log(sum)
```

对于 `reduce` 来说，它接受两个参数，分别是回调函数和初始值，接下来我们来分解上述代码中 `reduce` 的过程

- 首先初始值为 `0`，该值会在执行第一次回调函数时作为第一个参数传入
- 回调函数接受四个参数，分别为累计值、当前元素、当前索引、原数组，后三者想必大家都可以明白作用，这里着重分析第一个参数
- 在一次执行回调函数时，当前值和初始值相加得出结果 `1`，该结果会在第二次执行回调函数时当做第一个参数传入

所以在第二次执行回调函数时，相加的值就分别是 `1` 和 `2`，以此类推，循环结束后得到结果 `6`

想必通过以上的解析大家应该明白 **`reduce`** 是如何通过回调函数将所有元素最终转换为一个值的，当然 **`reduce`** 还可以实现很多功能，接下来我们就通过 **`reduce`** 来实现 `map` 函数

```
const arr = [1, 2, 3]



const mapArray = arr.map(value => value * 2)



const reduceArray = arr.reduce((acc, current) => {



  acc.push(current * 2)



  return acc



}, [])



console.log(mapArray, reduceArray) // [2, 4, 6]
```

## 119、JS的基本数据类型判断有什么方法



**1、typeof**

typeof 是一个操作符，其右侧跟一个一元表达式，并返回这个表达式的数据类型。返回的结果用该类型的字符串(全小写字母)形式表示，包括以下 7 种：number、boolean、symbol、string、object、undefined、function 等。

```
typeof``''``;``// string 有效``typeof``1;``// number 有效``typeof``Symbol();``// symbol 有效``typeof``true``;``//boolean 有效``typeof``undefined;``//undefined 有效``typeof``null``;``//object 无效``typeof``[] ;``//object 无效``typeof``new``Function();``// function 有效``typeof``new``Date();``//object 无效``typeof``new``RegExp();``//object 无效
```

有些时候，typeof 操作符会返回一些令人迷惑但技术上却正确的值：

- 对于基本类型，除 null 以外，均可以返回正确的结果。
- 对于引用类型，除 function 以外，一律返回 object 类型。
- 对于 null ，返回 object 类型。
- 对于 function 返回  function 类型。

其中，null 有属于自己的数据类型 Null ， 引用类型中的 数组、日期、正则 也都有属于自己的具体类型，而 typeof 对于这些类型的处理，只返回了处于其原型链最顶端的 Object 类型，没有错，但不是我们想要的结果。

**2、instanceof**

instanceof 是用来判断 A 是否为 B 的实例，表达式为：A instanceof B，如果 A 是 B 的实例，则返回 true,否则返回 false。 在这里需要特别注意的是：**instanceof 检测的是原型**，我们用一段伪代码来模拟其内部执行过程：

```
instanceof (A,B) = {``  ``var``L = A.__proto__;``  ``var``R = B.prototype;``  ``if``(L === R) {``    ``// A的内部属性 __proto__ 指向 B 的原型对象``    ``return``true``;``  ``}``  ``return``false``;``}
```

从上述过程可以看出，当 A 的 __proto__ 指向 B 的 prototype 时，就认为 A 就是 B 的实例，我们再来看几个例子：

```
[] instanceof Array;``// true``{} instanceof Object;``// true``new``Date() instanceof Date;``// true` `function Person(){};``new``Person() instanceof Person;` `[] instanceof Object;``// true``new``Date() instanceof Object;``// true``new``Person instanceof Object;``// true
```

我们发现，虽然 instanceof 能够判断出 [ ] 是Array的实例，但它认为 [ ] 也是Object的实例，为什么呢？

我们来分析一下 [ ]、Array、Object 三者之间的关系：

从 instanceof 能够判断出 [ ].__proto__  指向 Array.prototype，而 Array.prototype.__proto__ 又指向了Object.prototype，最终 Object.prototype.__proto__ 指向了null，标志着原型链的结束。因此，[]、Array、Object 就在内部形成了一条原型链：

![img](https://images2015.cnblogs.com/blog/849589/201601/849589-20160112232510850-2003340583.png)

从原型链可以看出，[] 的 __proto__  直接指向Array.prototype，间接指向 Object.prototype，所以按照 instanceof 的判断规则，[] 就是Object的实例。依次类推，类似的 new Date()、new Person() 也会形成一条对应的原型链 。因此，**instanceof 只能用来判断两个对象是否属于实例关系****， 而不能判断一个对象实例具体属于哪种类型。**

instanceof 操作符的问题在于，它假定只有一个全局执行环境。如果网页中包含多个框架，那实际上就存在两个以上不同的全局执行环境，从而存在两个以上不同版本的构造函数。如果你从一个框架向另一个框架传入一个数组，那么传入的数组与在第二个框架中原生创建的数组分别具有各自不同的构造函数。

```
var``iframe = document.createElement(``'iframe'``);``document.body.appendChild(iframe);``xArray = window.frames[0].Array;``var``arr =``new``xArray(1,2,3);``// [1,2,3]``arr instanceof Array;``// false
```

针对数组的这个问题，ES5 提供了 Array.isArray() 方法 。该方法用以确认某个对象本身是否为 Array 类型，而不区分该对象在哪个环境中创建。

```
if``(Array.isArray(value)){``  ``//对数组执行某些操作``}
```

Array.isArray() 本质上检测的是对象的 [[Class]] 值，[[Class]] 是对象的一个内部属性，里面包含了对象的类型信息，其格式为 [object Xxx] ，Xxx 就是对应的具体类型 。对于数组而言，[[Class]] 的值就是 [object Array] 。

**3、constructor**

当一个函数 F被定义时，JS引擎会为F添加 prototype 原型，然后再在 prototype上添加一个 constructor 属性，并让其指向 F 的引用。如下所示：

![img](https://images2015.cnblogs.com/blog/849589/201705/849589-20170508125250566-1896556617.png)

当执行 var f = new F() 时，F 被当成了构造函数，f 是F的实例对象，此时 F 原型上的 constructor 传递到了 f 上，因此 f.constructor == F

![img](https://images2015.cnblogs.com/blog/849589/201705/849589-20170508125714941-1649387639.png)

可以看出，F 利用原型对象上的 constructor 引用了自身，当 F 作为构造函数来创建对象时，原型上的 constructor 就被遗传到了新创建的对象上， 从原型链角度讲，构造函数 F 就是新对象的类型。这样做的意义是，让新对象在诞生以后，就具有可追溯的数据类型。

同样，JavaScript 中的内置对象在内部构建时也是这样做的：

![img](https://images2015.cnblogs.com/blog/849589/201705/849589-20170508131800457-2091987664.png)

**细节问题：**

> \1. null 和 undefined 是无效的对象，因此是不会有 constructor 存在的，这两种类型的数据需要通过其他方式来判断。
>
> \2. 函数的 constructor 是不稳定的，这个主要体现在自定义对象上，当开发者重写 prototype 后，原有的 constructor 引用会丢失，constructor 会默认为 Object

![img](https://images2015.cnblogs.com/blog/849589/201705/849589-20170508132757347-1999338357.png)

为什么变成了 Object？

因为 prototype 被重新赋值的是一个 { }， { } 是 new Object() 的字面量，因此 new Object() 会将 Object 原型上的 constructor 传递给 { }，也就是 Object 本身。

因此，为了规范开发，在重写对象原型时一般都需要重新给 constructor 赋值，以保证对象实例的类型不被篡改。

**4、toString**

toString() 是 Object 的原型方法，调用该方法，默认返回当前对象的 [[Class]] 。这是一个内部属性，其格式为 [object Xxx] ，其中 Xxx 就是对象的类型。

对于 Object 对象，直接调用 toString() 就能返回 [object Object] 。而对于其他对象，则需要通过 call / apply 来调用才能返回正确的类型信息。

```
Object.prototype.toString.call(``''``) ; ``// [object String]``Object.prototype.toString.call(1) ;  ``// [object Number]``Object.prototype.toString.call(``true``) ;``// [object Boolean]``Object.prototype.toString.call(Symbol());``//[object Symbol]``Object.prototype.toString.call(undefined) ;``// [object Undefined]``Object.prototype.toString.call(``null``) ;``// [object Null]``Object.prototype.toString.call(``new``Function()) ;``// [object Function]``Object.prototype.toString.call(``new``Date()) ;``// [object Date]``Object.prototype.toString.call([]) ;``// [object Array]``Object.prototype.toString.call(``new``RegExp()) ;``// [object RegExp]``Object.prototype.toString.call(``new``Error()) ;``// [object Error]``Object.prototype.toString.call(document) ;``// [object HTMLDocument]``Object.prototype.toString.call(window) ;``//[object global] window 是全局对象 global 的引用
```

## 120、 **eval是做什么的？**

它的功能是把对应的字符串解析成JS代码并运行；应该避免使用eval，不安全，非常耗性能（2次，一次解析成js语句，一次执行）。



## 121、**什么是window对象? 什么是document对象?**

window对象代表浏览器中打开的一个窗口。document对象代表整个html文档。实际上，document对象是window对象的一个属性。





## 122、**null，undefined的区别？**

null表示一个对象被定义了，但存放了空指针，转换为数值时为0。
undefined表示声明的变量未初始化，转换为数值时为NAN。
typeof(null) -- object;
typeof(undefined) -- undefined



## 123、**["1", "2", "3"].map(parseInt) 答案是多少？**



[1,NaN,NaN]

解析：
Array.prototype.map()
array.map(callback[, thisArg])
callback函数的执行规则
参数：自动传入三个参数
currentValue（当前被传递的元素）；
index（当前被传递的元素的索引）；
array（调用map方法的数组）

parseInt方法接收两个参数
第三个参数["1", "2", "3"]将被忽略。parseInt方法将会通过以下方式被调用
parseInt("1", 0)
parseInt("2", 1)
parseInt("3", 2)

parseInt的第二个参数radix为0时，ECMAScript5将string作为十进制数字的字符串解析；
parseInt的第二个参数radix为1时，解析结果为NaN；
parseInt的第二个参数radix在2—36之间时，如果string参数的第一个字符（除空白以外），不属于radix指定进制下的字符，解析结果为NaN。
parseInt("3", 2)执行时，由于"3"不属于二进制字符，解析结果为NaN。



## 124、new的 原理是什么？ 

1. 创建一个空对象，构造函数中的this指向这个空对象
2. 这个新对象被执行 [[原型]] 连接
3. 执行构造函数方法，属性和方法被添加到this引用的对象中
4. 如果构造函数中没有返回其它对象，那么返回this，即创建的这个的新对象，否则，返回构造函数中返回的对象。



## 

## 125、数组去重的方法



一、简单的去重方法

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
// 最简单数组去重法
/*
* 新建一新数组，遍历传入数组，值不在新数组就push进该新数组中
* IE8以下不支持数组的indexOf方法
* */
function uniq(array){
    var temp = []; //一个新的临时数组
    for(var i = 0; i < array.length; i++){
        if(temp.indexOf(array[i]) == -1){
            temp.push(array[i]);
        }
    }
    return temp;
}

var aa = [1,2,2,4,9,6,7,5,2,3,5,6,5];
console.log(uniq(aa));
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

二、对象键值法去重

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
/*
* 速度最快， 占空间最多（空间换时间）
*
* 该方法执行的速度比其他任何方法都快， 就是占用的内存大一些。
* 现思路：新建一js对象以及新数组，遍历传入数组时，判断值是否为js对象的键，
* 不是的话给对象新增该键并放入新数组。
* 注意点：判断是否为js对象键时，会自动对传入的键执行“toString()”，
* 不同的键可能会被误认为一样，例如n[val]-- n[1]、n["1"]；
* 解决上述问题还是得调用“indexOf”。*/
function uniq(array){
    var temp = {}, r = [], len = array.length, val, type;
    for (var i = 0; i < len; i++) {
        val = array[i];
        type = typeof val;
        if (!temp[val]) {
            temp[val] = [type];
            r.push(val);
        } else if (temp[val].indexOf(type) < 0) {
            temp[val].push(type);
            r.push(val);
        }
    }
    return r;
}

var aa = [1,2,"2",4,9,"a","a",2,3,5,6,5];
console.log(uniq(aa));
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

三、排序后相邻去除法

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
/*
* 给传入数组排序，排序后相同值相邻，
* 然后遍历时,新数组只加入不与前一值重复的值。
* 会打乱原来数组的顺序
* */
function uniq(array){
    array.sort();
    var temp=[array[0]];
    for(var i = 1; i < array.length; i++){
        if( array[i] !== temp[temp.length-1]){
            temp.push(array[i]);
        }
    }
    return temp;
}

var aa = [1,2,"2",4,9,"a","a",2,3,5,6,5];
console.log(uniq(aa));
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

四、数组下标法

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
/*
*
* 还是得调用“indexOf”性能跟方法1差不多，
* 实现思路：如果当前数组的第i项在当前数组中第一次出现的位置不是i，
* 那么表示第i项是重复的，忽略掉。否则存入结果数组。
* */
function uniq(array){
    var temp = [];
    for(var i = 0; i < array.length; i++) {
        //如果当前数组的第i项在当前数组中第一次出现的位置是i，才存入数组；否则代表是重复的
        if(array.indexOf(array[i]) == i){
            temp.push(array[i])
        }
    }
    return temp;
}

var aa = [1,2,"2",4,9,"a","a",2,3,5,6,5];
console.log(uniq(aa));
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

五、优化遍历数组法

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
// 思路：获取没重复的最右一值放入新数组
/*
* 推荐的方法
*
* 方法的实现代码相当酷炫，
* 实现思路：获取没重复的最右一值放入新数组。
* （检测到有重复值时终止当前循环同时进入顶层循环的下一轮判断）*/
function uniq(array){
    var temp = [];
    var index = [];
    var l = array.length;
    for(var i = 0; i < l; i++) {
        for(var j = i + 1; j < l; j++){
            if (array[i] === array[j]){
                i++;
                j = i;
            }
        }
        temp.push(array[i]);
        index.push(i);
    }
    console.log(index);
    return temp;
}

var aa = [1,2,2,3,5,3,6,5];
console.log(uniq(aa));
```



## 126、**关于事件，IE与火狐的事件机制有什么区别？ 如何阻止冒泡？**

IE为事件冒泡，Firefox同时支持事件捕获和事件冒泡。但并非所有浏览器都支持事件捕获。jQuery中使用`event.stopPropagation()`方法可阻止冒泡;（旧IE的方法 `ev.cancelBubble = true;`）





## 127、 **javascript 代码中的"use strict";是什么意思 ? 使用它区别是什么？**





除了正常模式运行外，ECMAscript添加了第二种运行模式：“严格模式”。
作用：
1) 消除js不合理，不严谨地方，减少怪异行为
2) 消除代码运行的不安全之处，
3) 提高编译器的效率，增加运行速度
4) 为未来的js新版本做铺垫。





## 128、介绍一下es6中Set,Map的区别？

ES6提供了新的数据结构Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。

Set函数可以接受一个数组（或类似数组的对象）作为参数，用来初始化。





```
// 例一
var set = new Set([1, 2, 3, 4, 4]);
[...set]
// [1, 2, 3, 4]

var s = new Set();

[2, 3, 5, 4, 5, 2, 2].map(x => s.add(x));

for (let i of s) {
  console.log(i);
}
// 2 3 5 4
```





在Set内部，两个`NaN`是相等。两个对象总是不相等的。可以用length来检测

四个操作方法：

- `add(value)`：添加某个值，返回Set结构本身。
- `delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
- `has(value)`：返回一个布尔值，表示该值是否为`Set`的成员。
- `clear()`：清除所有成员，没有返回值

set内部的元素可以遍历for...of...

weakset

WeakSet结构与Set类似，也是不重复的值的集合。

**WeakSet和Set的区别：**

- WeakSet的成员只能是对象，而不能是其他类型的值
- WeakSet中的对象都是弱引用，即垃圾回收机制不考虑WeakSet对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于WeakSet之中。这个特点意味着，无法引用WeakSet的成员，因此WeakSet是不可遍历的。

Map

Map结构提供了“值—值”的对应，是一种更完善的Hash结构实现。如果你需要“键值对”的数据结构，Map比Object更合适。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。



```
var m = new Map();
var o = {p: "Hello World"};

m.set(o, "content")
m.get(o) // "content"

m.has(o) // true
m.delete(o) // true
m.has(o) // false
```

个对象的引用，Map结构才将其视为同一个键。这一点要非常小心。



```
var map = new Map();

map.set(['a'], 555);
map.get(['a']) // undefined
上面代码的set和get方法，表面是针对同一个键，但实际上这是两个值，内存地址是不一样的，因此get方法无法读取该键，返回undefined。
```



如果Map的键是一个简单类型的值（数字、字符串、布尔值），则只要两个值严格相等，Map将其视为一个键，包括0和-0。另外，虽然NaN不严格相等于自身，但Map将其视为同一个键。

实例属性和方法：size、set、get、has、delete、clear

遍历方法：keys（）、values（）、entries（）、forEach（）



## 129、并行和并发的区别



并发是指一个处理器同时处理多个任务。
并行是指多个处理器或者是多核的处理器同时处理多个不同的任务。
并发是逻辑上的同时发生（simultaneous），而并行是物理上的同时发生。
来个比喻：并发是一个人同时吃三个馒头，而并行是三个人同时吃三个馒头。



## 130、为什么操作dom慢



**JS引擎和和渲染引擎的模块化设计，使得它们可以独立优化，运行速度更快，但是这种设计带来的后果就是DOM操作会越来越慢**

## 131、 **如何判断一个对象是否属于某个类？**





使用instanceof 即if(a instanceof Person){alert('yes');}

## 132、**new操作符具体干了什么呢?**



1) 创建一个空对象，并且 this 变量引用该对象，同时还继承了该函数的原型。
2) 属性和方法被加入到 this 引用的对象中。
3) 新创建的对象由 this 所引用，并且最后隐式的返回 this 。





## 133、 **Javascript中，执行时对象查找时，永远不会去查找原型的函数？**



Object.hasOwnProperty(proName)：是用来判断一个对象是否有你给出名称的属性。不过需要注意的是，此方法无法检查该对象的原型链中是否具有该属性，该属性必须是对象本身的一个成员。





## 134、**对JSON的了解？**



全称：JavaScript Object Notation
JSON中对象通过“{}”来标识，一个“{}”代表一个对象，如{“AreaId”:”123”}，对象的值是键值对的形式（key：value）。JSON是JS的一个严格的子集，一种轻量级的数据交换格式，类似于xml。数据格式简单，易于读写，占用带宽小。
两个函数：
JSON.parse(str)
解析JSON字符串 把JSON字符串变成JavaScript值或对象
JSON.stringify(obj)
将一个JavaScript值(对象或者数组)转换为一个 JSON字符串
eval('('＋json＋')')
用eval方法注意加括号 而且这种方式更容易被攻击

## 135、**JS延迟加载的方式有哪些？**

S的延迟加载有助与提高页面的加载速度。
defer和async、动态创建DOM方式（用得最多）、按需异步载入JS
defer：延迟脚本。立即下载，但延迟执行（延迟到整个页面都解析完毕后再运行），按照脚本出现的先后顺序执行。
async：异步脚本。下载完立即执行，但不保证按照脚本出现的先后顺序执行。



## 136、**模块化开发怎么做？**



模块化开发指的是在解决某一个复杂问题或者一系列问题时，依照一种分类的思维把问题进行系统性的分解。模块化是一种将复杂系统分解为代码结构更合理，可维护性更高的可管理的模块方式。对于软件行业：系统被分解为一组高内聚，低耦合的模块。
（1）定义封装的模块
（2）定义新模块对其他模块的依赖
（3）可对其他模块的引入支持。在JavaScript中出现了一些非传统模块开发方式的规范。 CommonJS的模块规范，AMD（Asynchronous Module Definition），CMD（Common Module Definition）等。AMD是异步模块定义，所有的模块将被异步加载，模块加载不影响后边语句运行。



## 137、**AMD（Modules/Asynchronous-Definition）、CMD（Common Module Definition）规范区别？**



AMD 是 RequireJS 在推广过程中对模块定义的规范化产出。CMD 是 SeaJS 在推广过程中对模块定义的规范化产出。
区别：
1) 对于依赖的模块，AMD 是提前执行，CMD 是延迟执行。不过 RequireJS 从 2.0 开始，也改成可以延迟执行（根据写法不同，处理方式不同）。
2) CMD 推崇依赖就近，AMD 推崇依赖前置。
3) AMD 的 API 默认是一个当多个用，CMD 的 API 严格区分，推崇职责单一。

```text
// CMD
define(function(require, exports, module) {
    var a = require('./a')
    a.doSomething()
    // 此处略去 100 行
    var b = require('./b') // 依赖可以就近书写
    b.doSomething()
})
// AMD 默认推荐
define(['./a', './b'], function(a, b) { // 依赖必须一开始就写好
    a.doSomething();
    // 此处略去 100 行
    b.doSomething();
})
```



## 138、**requireJS的核心原理是什么？（如何动态加载的？如何避免多次加载的？如何缓存的？）**



核心是js的加载模块，通过正则匹配模块以及模块的依赖关系，保证文件加载的先后顺序，根据文件的路径对加载过的文件做了缓存。





## 139、ts和js的区别



javascript是一个弱类型语言，Typescript是Javascript的一个超集，最大区别就是Ts提供了类型系统。



## 140、**call和apply**





call（）方法和apply（）方法的作用相同，动态改变某个类的某个方法的运行环境。他们的区别在于接收参数的方式不同。在使用call（）方法时，传递给函数的参数必须逐个列举出来。使用apply（）时，传递给函数的是参数数组。



##  141、**数组对象有哪些原生方法，列举一下**

pop、push、shift、unshift、splice、reverse、sort、concat、join、slice、toString、indexOf、lastIndexOf、reduce、reduceRight
forEach、map、filter、every、some



## 142、 **那些操作会造成内存泄漏**

全局变量、闭包、DOM清空或删除时，事件未清除、子元素存在引用

## 143、是否所有函数都有prototype 一说？



> 我们创建的每个函数都有一个 prototype（原型）属性，这个属性是一个指针，指向一个对象，
> 而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。
>
> 无论什么时候，只要创建了一个新函数，就会根据一组特定的规则为该函数创建一个prototype属性，这个属性指向函数的原型对象。在默认情况下，所有原型对象都会自动获得一个constructor（构造函数）属性，这个属性包含一个指向 prototype 属性所在函数的指针。

引自<<JavaScript 高级程序设计(第三版)>>中译本 第6.2.3节, 原型模式.

也就是JavaScript的prototype是仅函数拥有, 而对象也拥有prototype是源于其constructor属性所拥有的prototype.



##  
























