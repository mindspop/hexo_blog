# JavaScript Style Guide

> 前提说明：
> 本文通过对比一些主流 JavaScript Style Guide, 摘录了一些值得借鉴的语法书写方式，以提高程序执行性能。而格式问题「Formatting: space, newline, etc」暂且被忽略了，因为一般可以通过 IDE 编辑器设置处理，写的时候可以按照自己的习惯来，通过编辑器格式配置方式以保持和团队风格统一，常见格式建议可查看[参考资料](#reference)。

## 变量声明「Variable Declaration」

### Object
* 使用 Literal declaration

		var item = {};
		
* 使用同义词替换保留字

		// bad
		var superman = {
		  class: 'alien'
		};

		// bad
		var superman = {
		  klass: 'alien'
		};

		// good
		var superman = {
		  type: 'alien'
		};

### Array
	
	var items = [];

### Function

* 如果在`non-function block`(if, while, etc)，声明一个函数，需要把函数赋值给一个变量。「ECMA-262 中定义`a list of statements`为`a block`」

		// bad
		if (currentUser) {
  			function test() {
    		console.log('Nope.');
  			}
		}

		// good
		var test;
		if (currentUser) {
		  test = function test() {
		    console.log('Yup.');
		  };
		}
* 参数中不要使用`arguments`保留字「因为会覆盖 function scope 中的 arguments」

### 关于声明提升「Hoisting」
* 变量声明会被提前，但是赋值不会
* 函数声明时，函数名和函数体都会被提前

		function example() {
		
			// => Flying
  			superPower(); 
	
  			function superPower() {
    			console.log('Flying');
  			}
		}

## 类型检查「Type Checking 」
### Actual Types Checking
#### Types: String、Number、Boolean、Object 类型检查

	typeof variable === "Types"

#### Array
	Array.isArray(arrayLikeObject)
	
#### Node
	elem.nodeType === 1
	
#### null
	variable === null
	
#### undefined
	// 1. Global Variable
	typeof variable === "undefined"
	
	// 2. Local Variable
	variable === undefined
	
#### null or undefined
	variable == null

### 类型强制转换「Coerced Types」
	var number = 1,
	 string = "1",
	 bool = false;
	 
	 // "1"
	 number + "";
	 
	 // 1
	 +string;	
	 
	 // 0
	 +bool; 
	 
	 // "false"
	 bool + "";
	 
	 // "false"	 
	 !string;
	 
	 // "true"
	 !!number;

## 条件判断「Conditional Evaluation」

> Use === and !== over == and !=.

### Booleans, Truthies & Falsies 判断

	// Booleans:
	true, false
	
	// Truthy:
	"foo", 1, Object

	// Falsy:
	"", 0, null, undefined, NaN, void 0

### 判断数组是否为空

	// 当数组不为空时
	if (array.length) ...
	
	// 当数组为空时
	if (!array.length) ...
	
#### 判断字符串是否为空

	// 当字符串不为空时
	if (string) ...
	
	// 当字符串为空时
	if (!string) ...
	
### 判断引用是否为空「evaluating a reference」

	// 当引用不为空时
	if (foo) ...
	
	// 当引用为空时
	if (!foo) ...
	
	// 除去匹配: 0, "", null, undefined, NaN 情况，只匹配 boolean false
	if (foo === false) ...
	
	// 只匹配 null or undefined, 因为 null == undefined
	if (foo == null) ...
	
### 判断数组中是否存在某元素
	
	array.indexOf( "a" ) >= 0
	
## 常见操作优化

### Array 操作
* 使用 Array.push 而不是直接赋值
* 使用 Array.slice 拷贝数组
* 使用 Array.prototype.slice.call(arrayLikeObject) 将 array-like 对象转换为数组

### String 操作
* 使用 Array.join 拼接字符串
* 处理长字符串跨行时，用 “+” 拼接字符串

		// bad
		var errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';
		
		// bad
		var errorMessage = 'This is a super long error that was thrown because \
		of Batman. When you stop to think about how Batman had anything to do \
		with this, you would get nowhere \
		fast.';
		
		// good
		var errorMessage = 'This is a super long error that was thrown because ' +
		  'of Batman. When you stop to think about how Batman had anything to do ' +
		  'with this, you would get nowhere fast.';

### Object 操作
* 使用`.`读取字符串属性
* 使用`[]`读取变量数量

[参考资料](id:reference)：

1. [Principles of Writing Consistent, Idiomatic JavaScript](https://github.com/rwaldron/idiomatic.js)
2. [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)









