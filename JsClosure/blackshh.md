###  熟练掌握JavaScript闭包
#### 什么是闭包
先看一段代码：
``` javascript
var scope = "global scope"; 
function checkScope() {
    var scope = "local scope";
    function f() {
        return scope;
    }
    return f();
}
checkScope();   //=> "local scope"
```
分析一下上面的代码，该代码定义了一个全局变量 scope，以及一个函数checkScope,在函数checkScope中，定一个一个局部变量，同样命名为scope，以及一个函数 f (嵌套函数)。

checkScope被调用时，return f()，运行内部嵌套函数f,f沿着作用域链从内向外寻找变量scope，找到“local scope”，停止寻找，因此，函数返回 “local scope”;

接下来，代码稍作修改：
``` javascript
var scope = "global scope"; 
function checkScope() {
    var scope = "local scope";
    function f() {
        return scope;
    }
    return f;
}
checkScope()();   //=> "这次返回什么？"
```
代码执行过程分析：
checkScope被调用时，将内部嵌套的函数f返回，因此checkScope()()这句执行时，其实运行的是f(),f函数返回scope变量，在这种情况下，f会从哪个作用域里去寻找变量scope呢？

>注意：函数的执行依赖于变量作用域，这个作用域是在函数定义时决定的，而不是函数调用时决定的。

嵌套函数f(), 被定义时，所在的作用域链中，变量scope是被绑定的值是“local scope”，而不是"global scope"，因此，以上代码的结果是啥？没错，是"local scope"!

这就是闭包的神奇特性：闭包可以捕获到局部变量和参数的外部函数绑定，即便外部函数的调用已经结束。

>为什么在闭包中，外部函数定义的局部变量在函数返回后依然存在？

>每次调用JS函数的时候，都会为之创建一个新的对象用来保存局部变量，把这个对象添加到作用域链中。当函数返回的时候，就从作用域链中将这个绑定变量的对象删除。如果不存在嵌套的函数，也没有其他引用指向这个绑定对象，他就会被当作垃圾回收掉。如果定义了嵌套的函数，每个嵌套的函数都各自对应一个作用域链，并且这个作用域链指向一个变量绑定对象。但如果这些嵌套的函数对象在外部函数中保存下来，那么它们也会和所指向的变量绑定对象一样当作垃圾回收。但是如果这个函数定义了嵌套的函数，并将它作为返回值返回或者存储在某处的属性里，这时就会有一个外部引用指向这个嵌套的函数，它就不会被当作垃圾回收，并且它所指向的变量绑定对象也不会被当作垃圾回收。

看到这里就大概能了解什么是闭包了：***闭包是由函数引用其周边状态（词法环境）绑在一起形成的（封装）组合结构***。在JS中，闭包在每个函数被创建时形成。由于闭包和它的词法环境绑在一起，因此闭包让我们能够从一个函数内部访问外部函数的作用域。要使用闭包，只需要简单的将一个函数定义在另一个函数内部，并将它暴露出来，要暴露一个函数，可以将它返回或者传给其他函数。内部函数将能够访问到外部函数作用域中的变量，即使外部函数已经执行完毕（上文已解释）。
#### 闭包的使用场景
(1)  实现封装
可以先来看一个关于封装的例子，在person之外的地方无法访问其内部的变量，而通过提供闭包的形式来访问：
``` javascript
var person = function(){    
    //变量作用域为函数内部，外部无法访问    
    var name = "default";       
       
    return {    
       getName : function(){    
           return name;    
       },    
       setName : function(newName){    
           name = newName;    
       }    
    }    
}();    
     
console.log(person.name);//直接访问，结果为undefined    
console.log(person.getName());  //  default  
person.setName("abc");    
console.log(person.getName());    //abc
```
(2)  缓存
假如有一个计算乘积的函数，mult函数接收一些number类型的参数，并返回乘积结果。为了提高函数性能，我们增加缓存机制，将之前计算过的结果缓存起来，下次遇到同样的参数，就可以直接返回结果，而不需要参与运算。这里，存放缓存结果的变量不需要暴露给外界，并且需要在函数运行结束后，仍然保存，所以可以采用闭包。
``` javascript
var mult = (function(){
    var cache = {};
    var calculate = function() {
        var a = 1;
        for(var i = 0, len = arguments.length; i < len; i++) {
            a = a * arguments[i];
        }
        return a;
    }
    
    return function() {
        var args = Array.prototype.join.call(arguments, ',');
        if(args in cache) {
            return cache[args];
        }
        
        return cache[args] = calculate.apply(null, arguments);
    }
}())
```
(3)  通过循环给页面上多个dom节点绑定事件
场景描述：假如页面上有5个button,要给button绑定onclick事件，点击的时候，弹出对应button的索引编号。
``` javascript
<!DOCTYPE html>
<html>
<head>
     <meta charset="UTF-8">
</head>
<body>
    <button>Button0</button>
    <button>Button1</button>
    <button>Button2</button>
    <button>Button3</button>
    <button>Button4</button>
</body>
</html>
```
先随手来一段for循环：
``` javascript
var btns = document.getElementsByTagName('button');
for(var i = 0, len = btns.length; i < len; i++) {
    btns[i].onclick = function() {
        alert(i);
    }
}
```
通过执行该段代码，发现不论点击哪个button ，均alert 5；

why?

因为，onclick事件是被异步触发的，当事件被触发时，for循环早已结束，此时变量 i 的值已经是 5 。所以，当onlick事件函数顺着作用域链从内向外查找变量 i 时，找到的值总是 5 。

那怎么能循环给button添加事件，并且还能alert出来不同的值呢？答案当然是：“闭包”！在闭包的作用下，定义事件函数的时候，每次循环的i值都被封闭起来，这样在函数执行时，会查找定义时的作用域链，这个作用域链里的 i 值是在每次循环中都被保留的，因此点击不同的button会alert出来不同的 i 。

使用闭包：
``` javascript
for(var i = 0, len = btns.length; i < len; i++) {
    (function(i) {
        btns[i].onclick = function() {
            alert(i);
        }
    }(i))
}
```
运行以上代码，是符合我们需求的。
