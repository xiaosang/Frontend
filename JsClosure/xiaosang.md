# JavaScript闭包笔记

> 闭包简单理解成"定义在一个函数内部的函数"。

> 闭包就是能够读取其他函数内部变量的函数。

> 闭包就是将函数内部和函数外部连接起来的一座桥梁。

> 从技术的角度讲，所有的JavaScript函数都是闭包。

**它的最大用处有两个：**

* 1、保护函数内的变量安全（可以读取函数内部的变量）。
* 2、在内存中维持一个变量（让这些变量的值始终保持在内存中）。
 


**使用注意点**

* 1、由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。
* 2、闭包会在父函数外部，改变父函数内部变量的值。所以，如果把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。

定义一个倒计时，为了防止计时的变量被其他影响采用了闭包的形式
```
function time () {
    var countdown = 60;
    return function(){
        if(countdown==1)  clearInterval(inter)
        return --countdown;
    }
}
var t = time();
var inter = setInterval(function () { console.log(t());},1000);

```

**性能考量**
* 如果不是因为某些特殊任务而需要闭包，在没有必要的情况下，在其它函数中创建函数是不明智的，因为闭包对脚本性能具有负面影响，包括处理速度和内存消耗。
例如，在创建新的对象或者类时，方法通常应该关联于对象的原型，而不是定义到对象的构造器中。原因是这将导致每次构造器被调用，方法都会被重新赋值一次（也就是说，为每一个对象的创建）。
```
function MyObject(name, message) {
  this.name = name.toString();
  this.message = message.toString();
  this.getName = function() {
    return this.name;
  };

  this.getMessage = function() {
    return this.message;
  };
}
```
上面的代码并未利用到闭包的益处，因此，应该修改为如下常规形式：
```
function MyObject(name, message) {
  this.name = name.toString();
  this.message = message.toString();
}
MyObject.prototype = {
  getName: function() {
    return this.name;
  },
  getMessage: function() {
    return this.message;
  }
};
```

**扩展**

* 函数都有个函数环境
* 函数内部定义的方法和变量，要等到函数执行过以后，才会真正定义
```
//不用var关键字声明的变量等执行过后会成为全局变量
function a() {
    t = 123;
}
console.log(t)//报错
```
而

```
function a() {
    t = 123;
}
a();
console.log(t)//不会报错
```
