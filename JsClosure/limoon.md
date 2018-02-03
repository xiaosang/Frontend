#### JS闭包


写在闭包之前：

**上下文（context）** 是一段程序运行所需要的最小数据集合。我们可以从上下文交换（context switch）来理解上下文，在多进程或多线程环境中，任务切换时首先要中断当前的任务，将计算资源交给下一个任务。因为稍后还要恢复之前的任务，所以中断的时候要保存现场，即当前任务的上下文，也可以叫做环境。即上下文就是恢复现场所需的最小数据集合。容易把人弄晕的一点是，我们这里说的上下文、环境有时候也称作作用域（scope），即这两个概念有时候是混用的。不过，它们有不同的侧重点，下一节将会说明。

另外，JavaScript 中常见的情形是一个方法/函数的执行。从一段程序的角度看，这段程序运行所需的所有变量，就是它的上下文。

**作用域（scope）** 是标识符（变量）在程序中的可见性范围。作用域规则是按照具体规则维护标识符的可见性，以确定当前执行的代码对这些标识符的访问权限。作用域（scope）是在具体的作用域规则之下确定的。

前面说过，有时候上下文、环境、作用域是同义词；不过，上下文（context）指代的是整体环境，作用域关注的是标识符（变量）的可访问性（可见性）。上下文确定了，根据具体编程语言的作用域规则，作用域也就确定了。这就是上下文与作用域的关系。

写 JavaScript 代码时，如果 Function 作为参数，可以指定它在具体对象上调用时，这个对象常常叫做 context


##### 1、闭包的概念
闭包是指有权访问另一个函数作用域中的变量的函数。
创建闭包的常见方式，就是在一个函数内部创建另一个函数。

闭包是一个函数和声明该函数的词法环境的组合。从理论角度来说，所有函数都是闭包。
而要理解闭包，首先必须理解JavaScript特殊的变量作用域。
##### 2、闭包举例
example one：

```
function makeFunc() {
    let name = "Mozilla";
    function displayName() {
        console.log(`name = ${name}`); 
    }
    return displayName;
}

let myFunc = makeFunc();
myFunc();
```
这段代码看起来别扭却能正常运行。在一些编程语言中，函数中的局部变量仅在函数的执行期间可用。一旦 makeFunc() 执行过后，我们会很合理的认为 name 变量将不再可用。然而，因为代码运行的没问题，所以很显然在 JavaScript 中并不是这样的。

这个谜题的答案是 myFunc 变成一个 闭包 了。 闭包是一种特殊的对象。它由两部分构成：函数，以及创建该函数的环境。环境由闭包创建时在作用域中的任何局部变量组成。在我们的例子中，myFuunc 是一个闭包，由 displayName 函数和闭包创建时存在的 "Mozilla" 字符串形成。

example two:
```
function makeAdder(x) {
  return function(y) {
    return x + y;
  };
}

var add5 = makeAdder(5);
var add10 = makeAdder(10);

console.log(add5(2));  // 7
console.log(add10(2)); // 12
```
在这个示例中，我们定义了 makeAdder(x) 函数：带有一个参数 x 并返回一个新的函数。返回的函数带有一个参数 y，并返回 x 和 y 的和。

从本质上讲，makeAdder 是一个函数工厂 — 创建将指定的值和它的参数求和的函数，在上面的示例中，我们使用函数工厂创建了两个新函数 — 一个将其参数和 5 求和，另一个和 10 求和。

add5 和 add10 都是闭包。它们共享相同的函数定义，但是保存了不同的环境。在 add5 的环境中，x 为 5。而在 add10 中，x 则为 10。

##### 3、闭包的应用场景
one:

闭包允许将函数与其所操作的某些数据（环境）关连起来。这显然类似于面向对象编程。在面向对象编程中，对象允许我们将某些数据（对象的属性）与一个或者多个方法相关联。

因而，一般说来，可以使用只有一个方法的对象的地方，都可以使用闭包。

我们在写JavaScript代码时大多都是由事件驱动的去定义某种行为，然后将其添加到用户所触发的事件之上（比如点击或按键）。我们的代码也通常添加为回调：响应事件而执行的函数。

比如我们想在页面上加一个可以调整字号的按钮。一种是以像素为单位指定body元素的font-size，然后通过相对的em单位设置页面中其它元素（例如页眉的字号）。使用JS来写的时候。我们就可以修改body的font-size属性：
```
function makeSizer(size) {
  return function() {
    document.body.style.fontSize = size + 'px';
  };
}

var size12 = makeSizer(12);
var size14 = makeSizer(14);
var size16 = makeSizer(16);
```
two:
用闭包模拟私有方法

three:

```
for (var i = 0; i < 5; i++) {
    setTimeout(function() {
        console.log('i: ',i);
    }, 1000);
}
 
console.log(i);
 
//输出
5
i:  5
i:  5
i:  5
i:  5
i:  5
```

> 1、for循环和循环体外部的console是同步的，所以先执行for循环，再执行外部的console.log。（同步优先）
> 
> 2、for循环里面有一个setTimeout回调，他是垫底的存在，只能最后执行。（回调垫底）
> 
> 那么，为什么我们最先输出的是5呢？
> 
> 非常好理解，for循环先执行，但是不会给setTimeout传参（回调垫底），等for循环执行完，就会给setTimeout传参，而外部的console打印出5是因为for循环执行完成了。



最简单的解决方法是let修改。

可以看这里[网红前端面试题](https://segmentfault.com/a/1190000009711065)


##### 4、使用闭包的意义

局部变量无法共享和长久的保存，而全局变量可能造成变量污染，所以我们希望有一种机制既可以长久的保存变量又不会造成全局污染。

##### 5、面试中关于闭包
```
function fun(n,o) {
  console.log(o)
  return {
    fun:function(m){
      return fun(m,n);
    }
  };
}
var a = fun(0);  a.fun(1);  a.fun(2);  a.fun(3);//undefined,?,?,?
var b = fun(0).fun(1).fun(2).fun(3);//undefined,?,?,?
var c = fun(0).fun(1);  c.fun(2);  c.fun(3);//undefined,?,?,?
//问:三行a,b,c的输出分别是什么？
```

```
//第一行a
var a = fun(0);  a.fun(1);  a.fun(2);  a.fun(3);
```


可以得知，第一个fun(0)是在调用第一层fun函数。第二个fun(1)是在调用前一个fun的返回值的fun函数，所以：

第后面几个fun(1),fun(2),fun(3),函数都是在调用第二层fun函数。

遂：

在第一次调用fun(0)时，o为undefined；

第二次调用fun(1)时m为1，此时fun闭包了外层函数的n，也就是第一次调用的n=0，即m=1，n=0，并在内部调用第一层fun函数fun(1,0);所以o为0；

第三次调用fun(2)时m为2，但依然是调用a.fun，所以还是闭包了第一次调用时的n，所以内部调用第一层的fun(2,0);所以o为0

第四次同理；

即：最终答案为undefined,0,0,0


```
//第二行b
var b = fun(0).fun(1).fun(2).fun(3);//undefined,?,?,?
```


先从fun(0)开始看，肯定是调用的第一层fun函数；而他的返回值是一个对象，所以第二个fun(1)调用的是第二层fun函数，后面几个也是调用的第二层fun函数。

遂：

在第一次调用第一层fun(0)时，o为undefined；

第二次调用 .fun(1)时m为1，此时fun闭包了外层函数的n，也就是第一次调用的n=0，即m=1，n=0，并在内部调用第一层fun函数fun(1,0);所以o为0；

第三次调用 .fun(2)时m为2，此时当前的fun函数不是第一次执行的返回对象，而是第二次执行的返回对象。而在第二次执行第一层fun函数时时(1,0)所以n=1,o=0,返回时闭包了第二次的n，遂在第三次调用第三层fun函数时m=2,n=1，即调用第一层fun函数fun(2,1)，所以o为1；

第四次调用 .fun(3)时m为3，闭包了第三次调用的n，同理，最终调用第一层fun函数为fun(3,2)；所以o为2；

即最终答案：undefined,0,1,2


```
//第三行c
var c = fun(0).fun(1);  c.fun(2);  c.fun(3);//undefined,?,?,?
```


根据前面两个例子，可以得知：

fun(0)为执行第一层fun函数，.fun(1)执行的是fun(0)返回的第二层fun函数，这里语句结束，遂c存放的是fun(1)的返回值，而不是fun(0)的返回值，所以c中闭包的也是fun(1)第二次执行的n的值。c.fun(2)执行的是fun(1)返回的第二层fun函数，c.fun(3)执行的也是fun(1)返回的第二层fun函数。

遂：

在第一次调用第一层fun(0)时，o为undefined；

第二次调用 .fun(1)时m为1，此时fun闭包了外层函数的n，也就是第一次调用的n=0，即m=1，n=0，并在内部调用第一层fun函数fun(1,0);所以o为0；

第三次调用 .fun(2)时m为2，此时fun闭包的是第二次调用的n=1，即m=2，n=1，并在内部调用第一层fun函数fun(2,1);所以o为1；

第四次.fun(3)时同理，但依然是调用的第二次的返回值，遂最终调用第一层fun函数fun(3,1)，所以o还为1

即最终答案：undefined,0,1,1



##### 6、两个小的题目
```
var name = "The Window";

　　var object = {
　　　　name : "My Object",

　　　　getNameFunc : function(){
　　　　　　return function(){
　　　　　　　　return this.name;
　　　　　　};

　　　　}

　　};

　　alert(object.getNameFunc()());
```

```
var name = "The Window";

　　var object = {
　　　　name : "My Object",

　　　　getNameFunc : function(){
　　　　　　var that = this;
　　　　　　return function(){
　　　　　　　　return that.name;
　　　　　　};

　　　　}

　　};

　　alert(object.getNameFunc()());
```