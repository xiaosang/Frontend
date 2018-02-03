# 变量作用域
 
一个变量的作用域(scope)是程序源代码中定义这个变量的区域。

变量的作用域无非就是两种：全局变量和局部变量。 

* 全局作用域： 
- 最外层函数定义的变量拥有全局作用域，即对任何内部函数来说，都是可以访问的：

``` var scope = "global scope";
	function checkScope(){
		console.log(scope);
	}
	checkScope();
```

* 局部作用域：
- 和全局作用域相反，局部作用域一般只在固定的代码片段内可访问到，而对于函数外部是无法访问的，最常见的例如：函数内部

``` function checkScope(){
		var scope = "local scope";	
	}
	checkScope();
	console.log(scope);
```

*需要注意的是，函数内部声明变量的时候，一定要使用var语句。如果不用的话，你实际上声明了一个全局变量！*

``` function checkScope(){
		scope = "global scope";	
	}
	checkScope();
	console.log(scope);
```

再来看一个代码

* 在函数体内，局部变量的优先级高于同名的全局变量。

``` var scope = "global";
	function checkScope(){
		var scope = "local";//同名的
		return scope;//返回局部变量的值
	}
	checkScope()
```

* 声明局部变量时必须使用var语句 

看下面这个

``` scope = "global";
	function checkScope2(){
		scope = "local";//修改了全局变量
		myscope = "local";//声明了一个全新的全局变量
		return [scope,myscope];//返回两个值
	}
	checkScope2() 	// => ["local","local"]
	scope			// => "local"	全局变量修改了
	myscope			// => "local"	全局命名空间乱了
```

函数定义是可以嵌套的。由于每个函数都有它自己的作用域，因此会出现几个局部作用域嵌套的情况，例如：

``` var scope = "global scope";				//全局变量
	function checkScope(){			
		var scope = "local scope";			//局部变量
		function nested(){
			var scope = "nested scope";		//嵌套作用域内的局部变量
			return scope;					//返回当前作用域内的局部变量
		}
		return nested();
	}
	checkScope();							// => "嵌套作用域"
```


## 函数作用域和声明提前

在一些类似C语言的编程语言中，花括号内的每一段代码都具有各自的作用域，而且变量在声明它们的代码段之外是不可见的，称为块级作用域(block scope)，而JavaScript中没有块级作用域。JavaScript取而代之地使用了函数作用域(function scope):变量在声明它们的函数体以及这个函数体嵌套的任意函数体内都是有定义的。

比如C语言：

``` for(int i = 0; i < 10; i++){
		//i的作用范围只在这个for循环
	}
	printf("%d",&i);//error
```


JavaScript：
``` for(var i = 1; i < 10; i++){
        //coding
    }
    console.log(i); //10  
```


**未完待续...**