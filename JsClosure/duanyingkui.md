# 闭包

——就是能够读取其他函数内部变量的函数。

闭包有两个作用： 
* 第一个就是可以读取自身函数外部的变量（沿着作用域链寻找） 
* 第二个就是让这些外部变量始终保存在内存中

```
	function f1(){
		var n = 999;
		nAdd = function(){n += 1}
		function f2(){
		console.log(n);
		}
		return f2;
	}
	var result = f1();
	result(); // 999
	nAdd();
	result(); // 1000
```

在这段代码中，result实际上就是闭包f2函数。它一共运行了两次，第一次的值是999，第二次的值是1000。这证明了，函数f1中的局部变量n一直保存在内存中，并没有在f1调用后被自动清除。

为什么会这样呢？原因就在于f1是f2的父函数，而f2被赋给了一个全局变量，这导致f2始终在内存中，而f2的存在依赖于f1，因此f1也始终在内存中，不会在调用结束后，被垃圾回收机制（garbage collection）回收。

这段代码中另一个值得注意的地方，就是"nAdd=function(){n+=1}"这一行，首先在nAdd前面没有使用var关键字，因此nAdd是一个全局变量，而不是局部变量。其次，nAdd的值是一个匿名函数（anonymous function），而这个匿名函数本身也是一个闭包，所以nAdd相当于是一个setter，可以在函数外部对函数内部的局部变量进行操作。


### 使用闭包的注意点

* 1）由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。

* 2）闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。


代码一、

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
　　	console.log(object.getNameFunc()());
```

代码二、

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
　　console.log(object.getNameFunc()());
```



**后续补充**

```
	function outer(){
        var result = new Array();
        for(var i = 0; i < 2; i++){	//注：i是outer()的局部变量
            result[i] = function(){
               return i;
            }
        }
        return result;	//返回一个函数对象数组
        //这个时候会初始化result.length个关于内部函数的作用域链
      }
      var fn = outer();
      console.log(fn[0]());	//result：2
      console.log(fn[1]());	//result：2
```

调用fn[0]()的作用域链图：
http://img.blog.csdn.net/20170430122035791


