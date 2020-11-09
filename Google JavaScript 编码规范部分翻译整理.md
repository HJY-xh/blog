---
title: Google JavaScript 编码规范部分整理
categories: 技术
tags:
 - JavaScript
date: 2020-02-17 16:54:50
---

>本博客 [hjy-xh](https://hjy-xh.github.io/)，转载请申明出处

## 写在前面
本文参考:
- [https://google.github.io/styleguide/jsguide.html](https://google.github.io/styleguide/jsguide.html)
- [http://alloyteam.github.io/JX/doc/specification/google-javascript.xml](http://alloyteam.github.io/JX/doc/specification/google-javascript.xml)
- [https://max.book118.com/html/2019/0202/7052110006002005.shtm](https://max.book118.com/html/2019/0202/7052110006002005.shtm)
- [https://segmentfault.com/a/1190000012916070#item-5-22](https://segmentfault.com/a/1190000012916070#item-5-22)
- [https://github.com/wayou/wayou.github.io/issues/21](https://github.com/wayou/wayou.github.io/issues/21)

因本人水平有限,如有错误,可及时联系作者修改.

## 背景
JavaScript 是一种客户端脚本语言，Google 的许多开源工程中都有用到它。这份指南列出了编写 JavaScript 时需要遵守的规则，当且仅当 JavaScript 源文件遵守此处规则时，它才被描述为 Google 风格。

## JavaScript 语言规范
- 局部变量声明
	- 使用`const` 和`let`
	用 `const`或 `let` 声明所有局部变量，默认情况下使用`const`，除非需要重新分配变量。
	
	- 一个变量一个声明
	每个局部变量声明仅声明一个变量。不使用诸如 `let a = 1, b = 2;` 这样的声明形式。
	
	- 在需要时进行声明，并尽快初始化
	局部变量不是在其包含的块或类似块的结构的开头习惯性地声明的。而是将局部声明在接近首次使用它们的地方(在合理范围内)，以最大程度地减小其范围。
	
	- 根据需要声明类型
	可以在声明的局部变量上方添加JSDoc类型注释，或者如果不存在其他JSDoc，则可以在变量名称之前内联。
	
	例：
```javascript
	const /**! Array <number> */ data = [];
	
	/**
	 * 一些描述
	 * @type {! Array <number>}
	 */ 
	const data = [];
```
	不允许混合使用内联和JSDoc样式：因为编译器将仅处理第一个JSDoc，即内联注释会丢失，例：
```javascript
	/** 一些描述。*/ const /**！Array <number> */ 数据= [];
```
	
	**提示：在许多情况下，编译器可以推断出模板化类型，但不能推断其参数。当初始化字面量或构造函数调用不包含模板参数类型的任何值(例如，空数组，对象，Map和Set)或变量在闭包中修改时，在这些情况下，局部变量类型注释特别有用，否则编译器会将模板参数推断成未知。**

- 数组字面量
	- 使用逗号结尾
	如果最后一个元素和右括号之间有换行符，请在结尾加上逗号，例：
	```javascript
		const values = [
			'first value',
			'second value',
		];
	```
	- 不要使用可变参数的 `Array构造函数` 
	构造函数很容易因为传参不恰当而导致错误。请改用字面量形式。
	不建议的形式，例：

	```javascript
		const a1 = new Array(x1, x2, x3);
		const a2 = new Array(x1, x2);
		const a3 = new Array(x1);
		const a4 = new Array();
	```
	除了第三种情况外，这可以按照预期工作：如果x1为整数，那么a3则是一个长度为x1的数组，其所有元素均为`undefined`。如果x1为其他任何数字，则将引发异常。
	建议的写法，例：
	```javascript
		const a1 = [x1, x2, x3];
		const a2 = [x1, x2];
		const a3 = [x1];
		const a4 = [];
	```
	在适当的时候，允许用`new Array(length)`显示分配一个给定长度的数组。
	
	- 非数字属性
	请勿在数组上定义或使用非数字属性（除了`length`）。使用`Map`（或`Object`）代替。
	
	- 解构
	数组字面量可用于分配的左侧以执行解构（例如，从单个数组中解压缩多个值或可迭代时）。 可以包含最后一个rest元素（在...和变量名之间没有空格）,例：
	```javascript
		const [a, b, c, ...rest] = generateResults();
		//解构赋值还可以忽略某些元素
		let [, b,, d] = someArray
	```
	解构也可以用于函数参数（请注意，参数名称是必需的，但会被忽略）。如果解构数组的参数是可选的，要始终指定`[]`为默认值，并在左侧提供默认值，例：
	```javascript
		/** @param {!Array<number>=} param1 */
		function optionalDestructuring([a = 4, b = 2] = []) { … };
	```
	错误的写法，例：
	```javascript
		function badDestructuring([a, b] = [4, 2]) { … };
	```
	**提示：对于将多个值（解包）到一个函数的参数或返回中，在可能的情况下，优先选择对象分解而不是数组分解，因为它允许命名单个元素并为每个元素指定不同的类型。**
	
	- 展开运算符
	数组字面量可以包含展开运算符（`…` ），以将元素从一个或多个可迭代对象中展开，而不是用更笨拙的构造```Array.prototype``` 。变量紧跟在展开运算符后，没有空格。

- 对象字面量
	- 不要使用`Object构造函数`
	虽然`Object 构造器`没有上述类似的问题, 但鉴于可读性和一致性考虑, 最好还是在字面上更清晰地指明，例：
	```javascript
		var o = {};

		var o2 = { a: 0, b: 1, c: 2 }
	```
	
	- 请勿混用带引号和不带引号的键
	对象字面量可以表示结构（具有未加引号的键和/或符号）或字典（具有引号和/或计算的键）。不要将这两种键类型混合在单个对象字面量中，例：
	```javascript
		let obj = {
			width: 42,// struct风格的未加引号的键
			'maxWidth': 43 // dict风格的加引号的键
		}
	```
	
	- 这还扩展到将属性名称传递给函数，例如 `hasOwnProperty`。特别是这样做会破坏编译后的代码，因为编译器无法重命名/混淆字符串文字。
	不建议的写法，例：
	```javascript
		/** @type {{width: number, maxWidth: (number|undefined)}} */
		const o = {width: 42};
		if (o.hasOwnProperty('maxWidth')) {
		...
		}
	```
		最好这样实现，例：
	```javascript
		/** @type {{width: number, maxWidth: (number|undefined)}} */
		const o = {width: 42};
		if (o.maxWidth != null) {
		...
		}
	```
	- 计算的属性名称
	允许使用计算的属性名称（例如`{['key'+ foo（）]：42}`），并且将其视为dict样式（带引号的）键（即，不得与非引号的键混合使用），除非计算出的属性 是一个符号（例如[Symbol.iterator]）。 枚举值也可以用于计算键，但不应与同一字面量中的非枚举键混合使用,例：
	```javascript
		function foo(){
			return 'width'
		}
		let obj = {
			width: 42,
			['max' + foo()] : 43
		}
	```
	
	- 方法速记
	可以使用方法简写（`{method（）{…}}`）代替冒号后紧跟函数或箭头函数常量，从而在对象上定义方法。
	例：
	```javascript
		let obj = {
			width: 42,
			getWidth(){
				return this.width;
			}
		}
	```
	
	- 速记属性
	对象字面量允许使用速记属性,例：
	```javascript
		const foo = 1;
		const bar = 2;
		const obj = {
		foo,
		bar,
		method() { return this.foo + this.bar; }
		};
	```
	
	- 解构
	对象解构模式可以在分配的左侧使用，以执行解构并从单个对象解压缩多个值。
	分解后的对象也可以用作函数参数，但应保持尽可能的简单：单个级别的未引用速记属性。 参数解构中可能不使用更深层的嵌套和计算的属性。 在解构参数的左侧指定任何默认值（`{str ='some default'} = {}`，而不是`{str} = {str：'some default'}`），如果解构对象为本身是可选的，它必须默认为`{}`。 可以为分解结构参数的JSDoc指定任何名称（该名称未使用，但编译器需要）,例：
	```javascript
		/**
		* @param {string} ordinary
		* @param {{num: (number|undefined), str: (string|undefined)}=} param1
		*     num: The number of times to do something.
		*     str: A string to do stuff to.
		*/
		function destructured(ordinary, {num, str = 'some default'} = {})
		不被允许的写法，例：
		/** @param {{x: {num: (number|undefined), str: (string|undefined)}}} param1 */
		function nestedTooDeeply({x: {num, str}}) {};
		/** @param {{num: (number|undefined), str: (string|undefined)}=} param1 */
		function nonShorthandProperty({num: a, str: b} = {}) {};
		/** @param {{a: number, b: number}} param1 */
		function computedKey({a, b, [a + b]: c}) {};
		/** @param {{a: number, b: string}=} param1 */
		function nontrivialDefault({a, b} = {a: 2, b: 4}) {};
	```
	
	- 枚举
	枚举是通过将@enum批注添加到对象字面量中来定义的。 定义枚举后，可能无法将其他属性添加到枚举中。 枚举必须是常量，并且所有枚举值都必须是不可变的,例：
	```javascript
		/**
		* Supported temperature scales.
		* @enum {string}
		*/
		const TemperatureScale = {
			CELSIUS: 'celsius',
			FAHRENHEIT: 'fahrenheit',
		};

		/**
		* An enum with two options.
		* @enum {number}
		*/
		const Option = {
			/** The option used shall have been the first. */
			FIRST_OPTION: 1,
			/** The second among two options. */
			SECOND_OPTION: 2,
		};
	```

- 类
	- 构造函数
	构造函数是可选的,子类构造函数必须在设置任何字段或以其他方式访问`this`之前调用`super（）`。
	正确的写法,例:
	```javascript
		constructor(name, age, height) {
			super(name,age);
			this.height = height;
		}
	```
	错误的写法,例:
	```javascript
		constructor(name, age, height) {
			this.height = height;
			super(name,age);
			//SyntaxError: 'this' is not allowed before super()
		}
	```
	
	- 字段
	在构造函数中设置所有具体对象的字段（即方法以外的所有属性）。使用 @const 修饰的字段代表常量，不能被重新赋值。 使用适当的可见性注释（@private，@protected，@package）注释非公共字段，并在所有@private字段的名称后面加上下划线。 字段永远不会设置在具体类的原型上,例:
	```javascript
		class Foo {
		constructor() {
			/** @private @const {!Bar} */
			this.bar_ = computeBar();

			/** @protected @const {!Baz} */
			this.baz = computeBaz();
		}
		}
	```
	**提示:类在初始化之后，就不能再向其添加或删除属性了，因为这会影响虚拟机对其进行优化。如果必要，可以将之后才进行初始化的字段先赋值为 undefined，这样先占位之后，防止后面再添加新属性。对象身上的@struct 注释可以对不存在的字段的访问进行检查。类自带了这一功能。**
	
	- 计算属性
	计算属性只能用于类的属性是 symbol 的情况。 Dict-style 类型的属性（带引号或非 symbol 的计算属性是不被允许的。对于可遍历的类，需要定义其 [Symbol.iterator] 方法。其他情况下少用 Symbol。
	
	**注意:使用其他内建的 symbol 时要格外小心（e.g. Symbol.isConcatSpreadable）,因为编译器没有对它进行垫片（向后兼容）处理，所以在旧版浏览器中会有问题**
	
	- 静态方法
	在不影响可读性的前提下，推荐使用模块内部的函数而不是静态方法。
	
	静态方法应该只用于基类。静态方法不应该从一个保存了实例的变量身上调用，这个实例有可能是构造器或者子类的构造器初始化而来（静态方法应该使用 @nocollapse 来注释），而且如果子类没有定义该方法的话，不应该从子类直接调用。
	错误的写法,例:
	```javascript
		class Base { /** @nocollapse */ static foo() {} }
		class Sub extends Base {}
		function callFoo(cls) { cls.foo(); }  // discouraged: don't call static methods dynamically
		Sub.foo();  // illegal: don't call static methods on subclasses that don't define it themselves
	```
	
	- 声明类的旧方式
	ES6 方式的类声明是首选，但在某些情况下ES6类可能不可行。例如：
		- 如果存在或将要存在子类，包括框架创建的子类，还不能立即使用 ES6 风格的类声明。因为如果基类使用 ES6 方式的话，所有子类代码都需要更改。
		
		- 有些框架在调用子类构造器时需要显式提供 this，而 ES6 风格的类中在调用 super 前是获取不到 this 的。

	此规则还应用于这些代码：`let`，`const`，默认参数，rest 和箭头函数。

	通过 goog.defineClass 可以进行类 ES6 方式的类声明：
	```javascript
		let C = goog.defineClass(S, {
		/**
		* @param {string} value
		*/
		constructor(value) {
			S.call(this, 2);
			/** @const */
			this.prop = value;
		},

		/**
		* @param {string} param
		* @return {number}
		*/
		method(param) {
			return 0;
		},
		});
	```
	另外，尽管`goog.defineClass`所有新代码都应首选，但也可以使用更传统的语法,例:
	```javascript
		/**
		* @constructor @extends {S}
		* @param {string} value
		*/
		function C(value) {
		S.call(this, 2);
		/** @const */
		this.prop = value;
		}
		goog.inherits(C, S);

		/**
		* @param {string} param
		* @return {number}
		*/
		C.prototype.method = function(param) {
		return 0;
		};
	```
	如果有基类的话，实例中的属性需要在基类的构造器中定义。而方法则需要在构造器的原型上定义。
	
	一开始正确地定义构造器的继承关系并不是件容易的事！所以，最好使用 the Closure Library 提供的 `goog.inherits` 方法。

	- 不要直接操作`prototype`
	通过 class 关键字定义类比操作 prototype 更加简洁和直观。一般情况下的代码并没有必要操作原型，尽管它们对于定义旧式类声明中定义的类仍然有用。明确禁止混入和修改内置对象的原型.
	例外:框架代码(例如Polymer或Angular)可能需要使用`prototype`s，否则实现起来会更加丑陋。

	- Getters and Setters
	不要使用 JavaScript `getter`和 `setter`属性。其行为不透明出问题难追查，编译器支持上也有局限。提供正常的方法来代替他们。
	
	例外：在某些情况下不可避免地会定义`getter`或`setter`（例如`Angular`和`Polymer`等数据绑定框架，或者无法与调整的外部API的兼容时）。仅在这些情况下，使用`getter`和`setter`时要格外小心，前提是它们是通过`get`和`set`简写方法关键字定义的`Object.defineProperties`（或（不是 `Object.defineProperty`，这会干扰属性重命名））。Getters不得更改可观察状态。
	不允许的写法,例:
	```javascript
		class Foo {
		get next() { return this.nextId++; }
		}
	```

	- 重写 toString
	可以重写 toString 方法，但始终应该返回成功，并且不产生副作用（side effects）。
	**提示:需要注意的是，特别是在 toString 中调用其他方法时，特殊情况可能导致死循环。**

	- 接口
	接口可以通过 @interface 或 @record 来声明。通过 @record 声明的接口能够被显式（i.e. 通过 @implements）或者隐式地被类或对象实现。
	接口上的所有非静态方法主体都必须为空块。 字段必须在类构造函数中声明为未初始化的成员,例:
	```javascript
		/**
		* Something that can frobnicate.
		* @record
		*/
		class Frobnicator {
		constructor() {
			/** @type {number} The number of attempts before giving up. */
			this.attempts;
		}

		/**
		* Performs the frobnication according to the given strategy.
		* @param {!FrobnicationStrategy} strategy
		*/
		frobnicate(strategy) {}
		}
	```

	- 抽象类
	适当时使用抽象类。抽象类和方法必须使用注释@abstract。不要使用`goog.abstractMethod`。请参阅[抽象类和方法](https://github.com/google/closure-compiler/wiki/@abstract-classes-and-methods)。

- 函数
	- 顶级函数
	顶级函数可以直接在导出对象上定义，也可以在本地声明，也可以选择导出,例:
	```javascript
		/** @param {string} str */
		exports.processString = (str) => {
		// Process the string.
		};
	```
	
	```javascript
		/** @param {string} str */
		const processString = (str) => {
		// Process the string.
		};

		exports = {processString};
	```

	- 嵌套函数及闭包
	函数内可包含嵌套函数的定义。如果需要，可以赋值给一个 const 变量。

	- 箭头函数
	箭头函数提供了简洁的函数语法，并简化了嵌套函数的作用域。 优先选择箭头函数而不是function关键字，特别是对于嵌套函数。
	
	推荐使用箭头函数代替 `f.bind(this)`，特别是代替 `goog.bind(f,this)`。 避免`const self = this`这样的写法。箭头函数特别适合用于可能会传参回调。
	
	箭头的左侧包含零个或多个参数。如果只有一个未分解的参数，则参数周围的括号是可选的。使用括号时，可以指定内联参数类型
	
	**提示:始终都写括号是种好的做法，因为后面如果一旦新加了参数又忘记写括号则会有语法错误。**
	
	箭头的右侧包含函数的主体。 默认情况下，主体为block语句（零个或多个用花括号括起来的语句）。 如果发生以下情况之一，则主体也可能是隐式返回的单个表达式：程序逻辑要求返回值，或者void运算符位于单个函数或方法调用之前（使用void确保未定义返回，防止泄漏值并传达意图）。 如果单一表达形式提高了可读性（例如，对于简短表达或简单表达），则它是首选,例:
	```javascript
		/**
		* Arrow functions can be documented just like normal functions.
		* @param {number} numParam A number to add.
		* @param {string} strParam Another number to add that happens to be a string.
		* @return {number} The sum of the two parameters.
		*/
		const moduleLocalFunc = (numParam, strParam) => numParam + Number(strParam);

		// Uses the single expression syntax with `void` because the program logic does
		// not require returning a value.
		getValue((result) => void alert(`Got ${result}`));

		class CallbackExample {
		constructor() {
			/** @private {number} */
			this.cachedValue_ = 0;

			// For inline callbacks, you can use inline typing for parameters.
			// Uses a block statement because the value of the single expression should
			// not be returned and the expression is not a single function call.
			getNullableValue((/** ?number */ result) => {
			this.cachedValue_ = result == null ? 0 : result;
			});
		}
		}
	```
	
	不建议的写法,例:
	```javascript
		/**
		* A function with no params and no returned value.
		* This single expression body usage is illegal because the program logic does
		* not require returning a value and we're missing the `void` operator.
		*/
		const moduleLocalFunc = () => anotherFunction();
	```
	
	- 生成器(Generators)
	生成器带来许多有用的抽象概念，必要时可以使用。

	通过在`function`关键字后面加`*`号来定义一个生成器，后面加空格与生成器名称隔开。使用代理的`yield`时，在`yield`关键字后加`*`号,例:
	```javascript
		/** @return {!Iterator<number>} */
		function* gen1() {
			yield 42;
		}

		/** @return {!Iterator<number>} */
		const gen2 = function*() {
			yield* gen1();
		}

		class SomeClass {
		/** @return {!Iterator<number>} */
		* gen() {
			yield 42;
			}
		}
	```
	
	- 参数和返回类型
	函数参数和返回类型通常应使用JSDoc注释记录。
		- 默认参数
		参数列表中通过等号来指定可选参数。可选参数必须在`=`运算符的两侧都包含空格，命名上与正常参数一样（不使用 opt_ 前缀），JSDoc 指定类型时使用`=`后缀，顺序上置于正常参数之后，不要用初始化以确保代码明确。所有可选参数都需要指定默认值，哪怕它是`undefined`。
		```javascript
				/**
				* @param {string} required This parameter is always needed.
				* @param {string=} optional This parameter can be omitted.
				* @param {!Node=} node Another optional parameter.
				*/
				function maybeDoSomething(required, optional = '', node = undefined) {}

				/** @interface */
				class MyInterface {
				/**
				* Interface and abstract methods must omit default parameter values.
				* @param {string=} optional
				*/
				someMethod(optional) {}
				}
		```
		尽量少地使用可选参数。参数不定的情况下推荐使用解构的方式，这样所定义出来的 API 更加可读。
		**注意：与 Python 不同，初始化可选参数时返回新的非可变对象（[] 或 {}）是可以的。因为每次可选参数被使用时，都是重新赋值，不会与上一次的复用。包括函数调用的任何表达式都会用到初始化模块，所以初始化模块应该尽量简单。避免初始化模块暴露共享可变域，这容易导致函数调用之间的无意耦合。**

		- 剩余参数
		使用剩余参数而不是 arguments。JSDoc 中使用`...`标识剩余参数。剩余参数必需位于参数列表末尾。注意`...`与参数名间没有空格。也不要给剩余参数命名。千万不要给变量或参数取名`arguments`，这会覆盖内建的同名参数,例:
		```javascript
				/**
				* @param {!Array<string>} array This is an ordinary parameter.
				* @param {...number} numbers The remainder of arguments are all numbers.
				*/
				function variadic(array, ...numbers) {}
		```
	
	- 泛型
	定义泛型函数或方法时需在JS注文中加上@template TYPE。
	
	- 扩展运算符
	可使用展示操作符（`...`）来调用函数。当使用数组或可遍历对象解析后作为函数入参时，推荐使用展开操作符来替代 `Function.prototype.apply`。注意`...`后面没有空格,例:
	```javascript
		function myFunction(...elements) {}
		myFunction(...array, ...iterable, ...generator());
	```

- 字符串字面量
	- 使用单引号
	常规字符串使用单引号(`'`)而非双引号(`"`)来定义。
	通常情况下，字符串不能跨行。
	
	**提示:如果字符串包含单引号字符，请考虑使用模板字符串，以避免不得不对引号进行转义。**
	
	- 模板字符串
	使用模板字符串（` ` `）替代复杂的字符串拼接，特别是参与拼接的变量很多时。模板字符串是可以跨越多行的。
	模板字符串跨越多行时，可不受代码块缩进规则限制，如果加上缩进好看些的话也可以,例:

	```javascript
		function arithmetic(a, b) {
		return `Here is a table of arithmetic operations:
		${a} + ${b} = ${a + b}
		${a} - ${b} = ${a - b}
		${a} * ${b} = ${a * b}
		${a} / ${b} = ${a / b}`;
		}
	```
	
	- 不要使用多行接续
	无论是常规字符串还是模板字符串中都不要使用多行接续（即在行尾加反斜杠\\）。虽然 ES5 允许这么操作，但反斜杠后的空格会导致问题，并且这种形式也不易读。
	错误的写法,例:
	```javascript
		const longString = 'This is a very long string that far exceeds the 80 \
		column limit. It unfortunately contains long stretches of spaces due \
		to how the continued lines are indented.';
	```
	正确的写法,例:
	```javascript
		const longString = 'This is a very long string that far exceeds the 80 ' +
		'column limit. It does not contain long stretches of spaces since ' +
		'the concatenated strings are cleaner.';
	```

- 数字字面量
数字可有多种呈现形式：十进制，十六进制，八进制或二进制。分别使用小写的 0x，0o，0b 前缀表示十六进制，八进制以及二进制数字。除此之外不应该出现以0开头的数字。

- 控制结构
	- for循环
	ES6之后一共有三种for循环,推荐使用`for-of`
	`for-in`适用于字典类型（dist-style）而不要用于遍历数组。配合`Object.prototype.hasOwnProperty`来过滤掉非直接的属性。推荐使用`for-of `和` Object.keys`，其次才是 `for-in`。

	- 异常
	异常是语言中重要的一部分，发生异常时应尽可能抛出。始终抛出Error或子类的Error，而不是抛出字符串或其他对象作为异常。使用 `Error`时始终通过`new`来创建新实例。
	
	这种处理扩展到`Promise`拒绝值，因为在异步函数中`Promise.reject（obj）`等效于`throw obj;` 。
	
	自定义类型的错误提供了非常好的方式展示函数中的异常。当原生错误类型不能满足需求时，应尽可能创建自定义的异常。
	
	遇到错误后立即抛出优于将错误进行传递。

	空的 catch 块

	响应捕获到的异常，什么也不做是非常正确的。 当确实在catch块中不执行任何操作时，但记得加注释解释一下原因,例:
	```javascript
			try {
				return handleNumericResponse(response);
			} catch (ok) {
			// it's not numeric; that's fine, just continue
			}
			return handleTextResponse(response);
	```
		
	错误的写法,例:
	```javascript
			try {
				shouldFail();
				fail('expected an error');
			} catch (expected) {
			}
	```
	**提示:不像其他一些语言，上面示例行不通，因为会通过`fail`来捕获处理。所以使用`assertThrows`。**
		 
	 - switch 语句
	 术语解释：switch 语句体中其实是很多组的代码块。每一组又包含一个或多个 switch 标签（`case Foo:` 或 `default:`）以及标签后跟随的代码语句。

		- Fall-through：要加注释
		每个 switch 标签要么通过`break`，`return`，`throw` 结束，要么通过注释直接跳过到下一标签。在发生跳过的情况时，随便加个注释都行。如果是最后一个标签可以不加,例:
		```javascript
			switch (input) {
				case 1:
				case 2:
					prepareOneOrTwo();
				// fall through
				case 3:
					handleOneTwoOrThree();
					break;
				default:
					handleLargeNumber(input);
			}
		```
		
		- default 是不能省的
		即使 default 标签中不包含逻辑，也不要省略。

- this
	应该只在类的构造器或方法中使用`this`，或类中的箭头函数。其他情况下使用·this·需要在函数的 JSDoc 结尾处添加 @this 注释。
	不要使用`this`来引用全局对象，`eval`的执行上下文，事件的触发元素，以及函数这种不必要的用法中 `call()`，`apply()` 。

- 相等检查
使用恒等运算符（=== /！==），但以下情况除外。

	- 需要强制转换的异常
	捕捉`null`和`undefined`值：
	```javascript
		if (someObjectOrPrimitive == null) {
			// Checking for null catches both null and undefined for objects and primitives , 
			// but does not catch other falsy values like 0 or the empty string.
		}
	```

-  禁止使用的特性
	- with
	杜绝使用`with`关键字。这样的代码不易读，而且 ES5 之后就禁止掉了。
	
	- 代码的动态求值
	不要使用`eval`或`Function(...string) 构造器`代码加载器（code loader）中除外）。这些关键字很危险并且在 CSP 环境中是不工作的。
	**CSP 指 [Content Security Policy (CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)**

	- 自动分号添加
	始终以分号结束语句（类与函数的声明除外）。
	
	- 非标准的特性
	不要使用还不是标准的特性。包括已经被移除的特性（e.g. WeakMap.clear），还未纳入标准的新特性（e.g. TC39 目前的草稿，提议，通过提议但还未完成标准制定），或一些只被部分浏览器所实现的特性。只使用包含在 ECMA-262 或 WHATWG standards 标签中的特性。但一些有自己规范的项目是可以使用这些特性的，比如 Chrome 插件或 Node.js。三方转译器提供的特性也是被禁止的。
	
	- 原始类型的包装对象
	对于原始值的包装对象（`Boolean`, `Number`, `String`, `Symbol`）不能用`new`调用，也不能用来作类型声明。
	错误的写法,例:
	```javascript
		const /** Boolean */ x = new Boolean(false);
		if (x) alert(typeof x);  // alerts 'object' - WAT?
	```
	这些包装器可在需要类型转换时当作函数来调用（而不是使用拼接空字符串的方式来转成字符串），或用于创建 symbol。
	```javascript
		const /** boolean */ x = Boolean(0);
		if (!x) alert(typeof x);  // alerts 'boolean', as expected
	```
	
	- 修改原生对象
	千万不要修改原生对象，向其构造器或原型添加方法都是不行的。进行了这些操作的三方库也要避免使用。编译器在编译时会尽可能提供这些原生对象的原始版本；所以原生对象的任何东西都不要去动。

	不到万不得已，不要向全局对象添加属性（e.g. 三方库需要这样做）。
	
## JavaScript 编码风格
- JSDoc
JSDoc 使用在了所有的类，字段以及方法上。

	- 通用形式
	JSDoc 基本的形式如下：
	```javascript
		/**
		* Multiple lines of JSDoc text are written here,
		* wrapped normally.
		* @param {number} arg A number to do something to.
		*/
		function doSomething(arg) { … }
	```
	或者这种单行的形式：
	```javascript
		/** @const @private {!Foo} A short bit of JSDoc. */
		this.foo_ = foo;
	```
	如果单行形式长到需要折行，则需要切换到多行模式而不是使用单行形式。
	有许多工具会对 JSDoc 文档进行解析以提取出有效的信息对代码进行检查和优化。所以这些注释需要好好写。
	
	- Markdown
	JSDoc 支持 Markdown，所以必要时可包含 HTML。
	工具会自动提取 JSDoc 的内容，其中自己书写的格式会被忽略。比如如果你写成下面这个样子：
	```javascript
		/**
		* Computes weight based on three factors:
		*   items sent
		*   items received
		*   last timestamp
		*/
	```
	 最终提取出来是这样的：
	 `Computes weight based on three factors: items sent items received last timestamp`
	 取而代之的是，我们应该按 markdown 的语法来格式化,例：
	```javascript
		/**
		* Computes weight based on three factors:
		*
		*  - items sent
		*  - items received
		*  - last timestamp
		*/
	```
	
	- JSDoc标签
	 本规则可使用 JSDoc tags 的一个子集。详细列表见附录。大部分 tags 独占一行。
	 错误的写法,例：
	```javascript
			/**
			* The "param" tag must occupy its own line and may not be combined.
			* @param {number} left @param {number} right
			*/
			function add(left, right) { ... }
	```

	简单的 tag 无需额外数据（比如 @private，@const，@final，@export），可以合并到一行。
	```javascript
		/**
		* Place more complex annotations (like "implements" and "template")
		* on their own lines.  Multiple simple tags (like "export" and "final")
		* may be combined in one line.
		* @export @final
		* @implements {Iterable<TYPE>}
		* @template TYPE
		*/
		class MyClass {
		/**
		* @param {!ObjType} obj Some object.
		* @param {number=} num An optional number.
		*/
		constructor(obj, num = 42) {
			/** @private @const {!Array<!ObjType|number>} */
			this.data_ = [obj, num];
		}
		}
	```
	关于合并及合并后的顺序没有明确的规范，代码中保持一致即可。
	
	- 换行
	换行之后的 tag 块使用四个空格进行缩进。
	```javascript
		/**
		* Illustrates line wrapping for long param/return descriptions.
		* @param {string} foo This is a param with a description too long to fit in
		*     one line.
		* @return {number} This returns something that has a description too long to
		*     fit in one line.
		*/
		exports.method = function(foo) {
		return 5;
		};
	```
	@fileoverview 换行时不缩进。
	
	- 文件头部注释
	一个文件可以在头部有个总览。包括版权信息，作者以及默认可选的可见信息/visibility level等。文件中包含多个类时，头部这个总览显得很有必要。它可以帮助别人快速了解该文件的内容。如果写了，则应该有一个描述字段简单介绍文件中的内容以及一些依赖，或者其他信息。换行后不缩进,例:
	```javascript
		/**
		* @fileoverview Description of file, its uses and information
		* about its dependencies.
		* @package
		*/
	```
	 
	- 类的注释
	类，接口以及 records 需要有描述，参数，实现的接口以及可见性或其他适当的 tags 注释。类的描述需要告诉读者类的作用及何时使用该类，以及其他一些可以帮助别人正确使用该类的有用信息。构造器上的文本描述可省略。@constructor 和 @extends 不与 class 一起使用，除非该类是用来声明接口 @interface 或者扩展一个泛型类。
	```javascript
		/**
		* A fancier event target that does cool things.
		* @implements {Iterable<string>}
		*/
		class MyFancyTarget extends EventTarget {
		/**
		* @param {string} arg1 An argument that makes this more interesting.
		* @param {!Array<number>} arg2 List of numbers to be processed.
		*/
		constructor(arg1, arg2) {
			// ...
		}
		};

		/**
		* Records are also helpful.
		* @extends {Iterator<TYPE>}
		* @record
		* @template TYPE
		*/
		class Listable {
		/** @return {TYPE} The next item in line to be returned. */
		next() {}
		}
	```
	
	- 枚举和 typedef 注释
	所有枚举和typedef必须在上一行用适当的JSDoc标记（@typedef或@enum）进行记录。 公共枚举和typedef也必须有描述。 单个枚举项可能在上一行带有JSDoc注释的文档中。
	```javascript
		/**
		* A useful type union, which is reused often.
		* @typedef {!Bandersnatch|!BandersnatchType}
		*/
		let CoolUnionType;
		/**
		* Types of bandersnatches.
		* @enum {string}
		*/
		const BandersnatchType = {
		/** This kind is really frumious. */
		FRUMIOUS: 'frumious',
		/** The less-frumious kind. */
		MANXOME: 'manxome',
		};
	```
	Typedefs 可方便地用于定义 records 类型，或 unions 的别名，复杂函数，或者 泛型类型。Typedefs 不适合用来定义字段很多的 records，因为其不支持对每个字段进行文档书写，也不适合用于模板或递归引用中。对于大型 records 使用 @record。
	
	- 方法与函数注释
	在方法和命名函数中，必须记录参数和返回类型，除非相同签名@overrides省略所有类型。必要时应记录此类型。如果函数没有非空的return语句，则可以省略返回类型。

	如果方法，参数和返回描述（但不是类型）在方法的其余JSDoc或其签名中显而易见，则可以省略。

	方法的描述应使用第三人称。

	如果方法覆盖超类方法，则它必须包含@override注释。覆盖的方法从超类方法继承所有JSDoc注释（包括可见性注释），并且应在覆盖的方法中将其省略。但是，如果在类型注释中完善了任何类型，则必须显式指定所有@param和@return注释。
	```javascript
		/** A class that does something. */
		class SomeClass extends SomeBaseClass {
		/**
		* Operates on an instance of MyClass and returns something.
		* @param {!MyClass} obj An object that for some reason needs detailed
		*     explanation that spans multiple lines.
		* @param {!OtherClass} obviousOtherClass
		* @return {boolean} Whether something occurred.
		*/
		someMethod(obj, obviousOtherClass) { ... }

		/** @override */
		overriddenMethod(param) { ... }
		}

		/**
		* Demonstrates how top-level functions follow the same rules.  This one
		* makes an array.
		* @param {TYPE} arg
		* @return {!Array<TYPE>}
		* @template TYPE
		*/
		function makeArray(arg) { ... }
	```
	
	如果只需要记录函数的参数和返回类型，则可以选择在函数签名中使用内联JSDocs。 这些内联JSDocs指定不带标签的return和param类型,例:
	```javascript
		function /** string */ foo(/** number */ arg) {...}
	```
	
	如果需要描述或标签，请在方法上方使用单个JSDoc注释。 例如，返回值的方法需要@return标记。
	```javascript
		class MyClass {
		/**
		* @param {number} arg
		* @return {string}
		*/
		bar(arg) {...}
		}
	```
	以下写法是错误的:
	```javascript
		// Illegal inline JSDocs.

		class MyClass {
		/** @return {string} */ foo() {...}
		}

		/** Function description. */ bar() {...}
	```
	在匿名函数中，注释通常是可选的。 如果自动类型推断不足或显式注释提高了可读性，则对param进行注释并返回如下类型：
	```javascript
		promise.then(
		/** @return {string} */
		(/** !Array<string> */ items) => {
		doSomethingWith(items);
		return items[0];
		});
	```

- 命名
	- 适用于所有标识符的规则
	可用于标识符的有 ASCII 字符，数字，还有下划线 _ 以及不太常用的 $ (一些框架里面比如 Angular 会用)。
	
	标识符取名尽量表意。不要怕名字太长，因为代码是给人看的，别人能看懂最重要。不要使用带歧义的缩写或者项目之外的人看不懂的缩写，也不要通过删除某个单词中的字符来发明缩写，例:
	```javascript
		errorCount          // No abbreviation.
		dnsConnectionIndex  // Most people know what "DNS" stands for.
		referrerUrl         // Ditto for "URL".
		customerId          // "Id" is both ubiquitous and unlikely to be misunderstood.
	```
	不建议的写法例：
	```javascript
		n                   // Meaningless.
		nErr                // Ambiguous abbreviation.
		nCompConns          // Ambiguous abbreviation.
		wgcConnections      // Only your group knows what this stands for.
		pcReader            // Lots of things can be abbreviated "pc".
		cstmrId             // Deletes internal letters.
		kSecondsPerDay      // Do not use Hungarian notation.
	```
	
	- 标识符类型的命名规则
		- 包名
		包名全是**lowerCamelCase**。例如，` my.exampleCode.deepSpace`但不是`my.examplecode.deepspace`或`my.example_code.deep_space`。
		
		- 类名
		定义类，接口，记录和 typedef 名称时,使用大写开头的驼峰**UpperCamelCase**。

		未被导出的类只本地使用，并没有用 @private 标识，所以命名上不需要以下划线结尾。
		
		类型名称通常为名词或名词短语。比如，Request，ImmutableList，或者 VisibilityMode。此外，接口名有时会是一个形容词或形容短语（比如 Readable）。
		
		- 方法名
		方法名使用小写开头的驼峰。私有方法需以下划线结尾。

		方法名一般为动词或动词短语。比如`sendMessage`或者`stop_`。属性的 Getter 或 Setter 不是必需的，如果有的话，也是小写驼峰命名且需要类似这样 `getFoo`(对于布尔值使用 isFoo 或 hasFoo 形式)， `setFoo(value)`。

		单元测试代码中的方法名会出现用下划线来分隔组件形式。一种典型的形式是这样的 `test<MethodUnderTest>_<state>`，例如 `testPop_emptyStack`。对于这种测试代码中的方法，命名上没有统一的要求。
		- 枚举名
		枚举使用大写开头的驼峰，和类相似，一般一个单数形式的名词。枚举中的元素写成`CONSTANT_CASE`。
		
		- 常量名
		常量写成 CONSTANT_CASE：所有字母使用大写，以下划线分隔单词。私有静态属性可以用内部变量代替，所以不会有使用私有枚举的情况，也就无需将常量以下划线结尾来命名。
			- 常量的定义
			每个常量都是 @const 标识的静态属性或模块内部通过`const`声明的变量，但并不是所有 @const 标识的静态属性或 `const`声明的变量都是常量。需要常量时，先想清楚该对象是否真的不可变。例如，如果该对象中可观察状态中任何一个可被改变，那么几乎可以肯定它不是常数。只是想着不去改变它的值是不够的，我们要求它需要从本质上来说应该一成不变,例:
	```javascript
				// Constants
				const NUMBER = 5;
				/** @const */ exports.NAMES = ImmutableList.of('Ed', 'Ann');
				/** @enum */ exports.SomeEnum = { ENUM_CONSTANT: 'value' };

				// Not constants
				let letVariable = 'non-const';
				class MyClass { constructor() { /** @const {string} */ this.nonStatic = 'non-static'; } };
				/** @type {string} */ MyClass.staticButMutable = 'not @const, can be reassigned';
				const /** Set<string> */ mutableCollection = new Set();
				const /** ImmutableSet<SomeMutableType> */ mutableElements = ImmutableSet.of(mutable);
				const Foo = goog.require('my.Foo');  // mirrors imported name
				const logger = log.getLogger('loggers.are.not.immutable');
	```
			常量的名称通常是名词或名词短语。
			
			- 本地别名
			给导入的变量起别名来提高可读性是可行的。函数中也有使用别名的情况。别名必需是`const`类型。
			
		- 非常量字段名
		非常量字段（静态或其他）使用小写开头的驼峰，如果是私有的私有的则以下划线结尾。
		
		一般是名词或名词短语。例如 `computedValues`，`index_`。
		
		- 参数名
		参数使用小写开头的驼峰形式。即使参数需要一个构造器来初始化时，也是这一规则。
		
		公有方法的参数名不能只使用一个字母。
		
		**例外：**如果三方库需要，参数名可以用 $ 开头。此例外不适用于其他标识符（e.g. 本地变量或属性）。
		
		- 局部变量名
		如上所述，除了模块本地（顶级）常量外，本地变量名称都用**lowerCamelCase**形式编写。 函数作用域中的常量仍遵循lowerCamelCase形式。 请注意，即使变量包含构造函数，也将使用**lowerCamelCase**形式。
		
		- 模板参数名
		模板参数力求简洁，用一个单词，一个字母表示，全部使用大写，例如 TYPE，THIS。
		
		- 模板本地名
		未导出的模块本地名称是隐式私有的。 它们未被标记为@private，并且不以下划线结尾。这适用于类，函数，变量，常量，枚举和其他模块本地标识符。
	
	- 驼峰：定义
	有时将一个英文短语转成驼峰有很多形式，例如首字母进行缩略，IPv6 以及 iOS 这种都有出现。为保证代码可控，本规范规定出如下规则。
		- 将短语移除撇号转成**ASCII**表示。例如 Müller's algorithm 表示成 Muellers algorithm。
		- 将上述结果拆分成单词，以空格或其他不发音符号（中横线）进行分隔。
			- 推荐的做法：如果其中包含一个已经常用的驼峰翻译，直接提取出来（e.g. AdWords 会成为 adwords）。需要注意的是 iOS 本身并不是个驼峰形式，它不属性任何形式，所以它不适用本条规则。
		- 将所有字母转成小写，然后将以下情况中的首字母大写：
			- 每个单词的首字母，这样便得到了大写开头的驼峰
			- 除首个单词的其他所有单词的首字母，这样得到小写开头的驼峰
		- 将上述结果合并。
	
	过程中原来名称中的大小写均被忽略，例：
	
	```javascript
		Prose form               | Correct            | Incorrect
		-------------            | -------------      | -------------
		"XML HTTP request"       | XmlHttpRequest     | XMLHTTPRequest
		"new customer ID"        | newCustomerId      | newCustomerID
		"inner stopwatch"        | innerStopwatch     | innerStopWatch
		"supports IPv6 on iOS?"  | supportsIpv6OnIos  | supportsIPv6OnIOS
		"YouTube importe"        | YouTubeImporter    | YoutubeImporter
	```
	可以接受，但不推荐。
	
	**注意：一些英文词汇通过中横线连接的方式是有歧义的，比如 "nonempty" 和 "non-empty" 都是正确写法，所以方法名 checkNonempty checkNonEmpty 都算正确。**

- 格式化
**术语解释：**代码块（block-like construct）指类，函数，方法这些元素的正文部分，或花括号包裹的代码部分。参考数组字面量,对象字面量的定义，数组或对象也可以被视作一个类似于块的构造。
**提示:使用 clang-format。社区已经做了大量努力以使得 clang-format 能够很好地处理 JavaScript 文件。其中也集成了几位著名代码开发者的努力。**
	- 大括号(花括号)
		- 括号用于各种流程控制结构
		括号用在各种控制结构中（譬如 if，else，for，do，while 等），即使结构中只包含一句代码。第一条指令的非空块状结构必须另起一行。
		错误的写法,例:
		```javascript
			if (someVeryLongCondition())
				doSomething();
			
			for (let i = 0; i < foo.length; i++) bar(foo[i]);
		```
		**例外:**如果指令可以完全地用写在一行里，那么可以不用大括号以增加可读性,例:'if (shortCondition()) foo();`
		
		- 非空代码块：K&R 风格
		非空代码块使用的花括号遵循 Kernighan and Ritchie 风格 (也即 [Egyptian brackets](https://blog.codinghorror.com/new-programming-jargon/))：
			- 左花括号不另起新行
			- 左花括号后紧跟换行
			- 右花括号前需要换行
			- 如果右花括号结束了语句，或者它是函数、类、类中的方法的结束括号，则其后面需要换行。如果后面紧跟的是 else，catch 或 while，或逗号，分号以及右括号，则不需要跟一个换行。
			例:
		```javascript
				class InnerClass {
				constructor() {}
				
				/** @param {number} foo */
				method(foo) {
					if (condition(foo)) {
					try {
						// Note: this might fail.
						something();
					} catch (err) {
						recover();
					}
					}
				}
				}
		```

		- 空代码块：应紧凑
		对于空代码块打开的时候就应立即闭合，中间不留空格，换行以及其他任何字符（`{}`），除非该代码块处于一个连续的声明语境中（譬如这些带有多个代码块的语句 `if/else`，`try/catch/finally`）,例:`function doNothing() {}`。
		错误的写法，例：
		```javascript
			if (condition) {
			// …
			} else if (otherCondition) {} else {
			// …
			}
			
			try {
			// …
			} catch (e) {}
		```

	- 代码块中的缩进：2个空格
	每当新开一个区块或块状结构，增加两个空格的缩进。区块结束之后，缩进恢复到前一级水平。缩进对该区块内的代码和注释同样有要求。

		- 数组字面量：可作为块状结构
		任何数组都可以按块状结构的格式书写。例如，以下的写法都是有效（不代表全部写法）：
		```javascript
			const a = [
				0,
				1,
				2,
			];
			
			const b =
				[0, 1, 2];
				
			const c = [0, 1, 2];
			
			someMethod(foo, [
				0, 1, 2,
			], bar);
		```
		允许其他组合，尤其是在强调元素之间的语义分组时，而不是只用来减小较大数组的垂直大小。

		- 对象字面量：可作为块状结构
		对象字面量也可以当作代码块处理，规则与上面数组字面量类似。以下写法都是合法的：
		```javascript
			const a = {
				a: 0,
				b: 1,
			};
			
			const b =
				{a: 0, b: 1};
				
			const c = {a: 0, b: 1};
			
			someMethod(foo, {
				a: 0, b: 1,
			}, bar);
		```
		- 类
		类的声明（无论是内容声明[declarations]还是表达式声明[expressions]）都像块状结构一样缩进。在类的方法声明和类中的内容声明（表达式结束时仍然需要加分号）的右大括号（后一个大括号）之后不加分号。其中可以使用关键字extends，但是不要用@extends的JS注文（JSDoc），除非你继承了一个模板类型（templatized type）,例：
		```javascript
			class Foo {
			constructor() {
				/** @type {number} */
				this.x = 42;
				}
			
			/** @return {number} */
			method() {
				return this.x;
				}
			}
			Foo.Empty = class {};
		```
		```javascript
			/** @extends {Foo<string>} */
			foo.Bar = class extends Foo {
			/** @override */
			method() {
				return super.method() / 2;
				}
			};
			
			/** @interface */
			class Frobnicator {
			/** @param {string} message */
				frobnicate(message) {}
			}
		```

		- 函数表达式
		当声明匿名函数时，函数正文在原有缩进水平上增加两个空格的缩进，例：
		```javascript
			prefix.something.reallyLongFunctionName('whatever', (a1, a2) => {
			// Indent the function body +2 relative to indentation depth
			// of the 'prefix' statement one line above.
			if (a1.equals(a2)) {
				someOtherLongFunctionName(a1);
				} else {
				andNowForSomethingCompletelyDifferent(a2.parrot);
				}
			});

			some.reallyLongFunctionCall(arg1, arg2, arg3)
				.thatsWrapped()
				.then((result) => {
				// Indent the function body +2 relative to the indentation depth
				// of the '.then()' call.
				if (result) {
					result.use();
				}
				});

		```
		- Switch语句
		就像其他块状结构，该语句的缩进方式也是+2。
		
		开始新的一条Switch标签，格式要像开始一个新的块状结构，新起一行，缩进+2。适当时候可以用块状结构来明确Switch全文范围。而到下一条Switch标签的开始行，缩进（暂时）还原到原缩进水平。
		
		在break和下一条Switch标签之间可以适当地空一行。
		例：
		```javascript
			switch (animal) {
				case Animal.BANDERSNATCH:
					handleBandersnatch();
					break;
			
				case Animal.JABBERWOCK:
					handleJabberwock();
					break;

				default:
					throw new Error('Unknown animal');
			}
		```

	- 声明语句
	
		- 一个声明占一行
		每个声明语句后都跟换行。

		- 分号是必需的
		语句后需用分号结束。不能依赖于编辑器的自动分号插入功能。

	- 最大列宽：80
	对于 JavaScript 源码，规定其单行长度不超过 80 字符。除以下列出的情形外，超出的时候需要根据下面的换行规则来进行换行操作。
	例外的情形：
		- `goog.module`，`goog.require`和`goog.requireType`语句
		- ES模块的`import`和`export from`语句

	- 换行
	**术语解释：**换行指将一个表达式拆分成多行展示。
	
		并没有一个全面准确的的规则来指导每种场景下该如何换行，相反，对同一段代码往往存在多种合法的换行方式。

		**提示：尽管换行大多时候是为了满足列宽限制，但在满足的情况下，编码过程中每个人做法也不尽相同，这是可以的。抽取方法或变量有可能会规避掉换行的问题。**
			- 何处该换行
			换行的的准则是：尽量在优先级高的语法层面（higher syntactic level）进行，例：
			```javascript
				currentEstimate =
					calc(currentEstimate + x * currentEstimate) /
						2.0;
			```
			不推荐的写法，例：	
			```javascript
				currentEstimate = calc(currentEstimate + x *
					currentEstimate) / 2.0;
			```
			上面示例中，语法优先级从高到低依次为：赋值，除法，函数调用，参数，数字常量。
			操作符的规则：
			- 请在运算符之后换行（注意这和JAVA谷歌代码风格不同）。“.”并不是一个运算符，所以不适用上述规则。
			- 方法和构造函数之后的左圆括号不能换行。
			- 逗号紧跟前面的代码。
			**注意：换行首要目的是保持代码整洁，当最小行数能满足需求时，换行是不需要的。**

		- 换行后后续行至少有4个空格的缩进
		当发生换行时，第一行后面跟着的其他行至少缩进 4 个空格，除非满足代码块的缩进规则，可另说。

		换行后后续跟随多行时，缩进可适当大于 4 个空格。通常，语法中低优先级的后续行以 4 的倍数进行缩进，如果只有两行并且处于同一优先级，则保持一样的缩进即可。

	- 空格
	
		- 垂直方向的空格
		以下场景需要有一个空行：
			- 类或对象中的方法间
			例外的情形：对象中属性间的空行是可选的。如果有的话，一般是用来将属性进行分组。
			
			- 方法体中，尽量少地使用空行来进行代码的分隔。函数体开始和结束都不要加空行。
			
			- 类或对象中首个方法前及最后一个方法后的空行，既不提倡也不反对。
			
		连续多个空行是允许的，但不鼓励这么做。

		- 水平方向的空格
		水平方向的空格根据出现的位置不同分为三类：行首（一行的开始），行尾（一行的结束）以及行间（一行中除去行首及行尾的部分）。行首的空格（i.e. 缩进）无处不在。行尾的空格是禁止的。
		
		除了 Javascript 本身及其他规则的要求，还有字面量，注释，JSDoc 等需要的空格外，单个的 ASCII 类型的空格在以下情形中也是需要的。
		- 将关键字（比如 `if`，`for`，`catch`）与括号（`(`）分隔。
		
		- 将关键字（`else`，`catch`）与闭合括号（`}`） 分隔。
		
		- 对于左花括号有两种例外：
		
			- 作为函数首个参数的对象之前，数组中首个对象元素 （`foo({a: [{c: d}]})`）。
			
			- 在模板表达式中，因为模板语法的限制不能加空格（`abc${1 + 2}def`）。
			
		- 二元，三元操作符的两边。
		
		- 逗号或分号后，但其前面是不允许有空格的。
		
		- 对象字面量中冒号后面。
		
		- 双斜线（//）两边。这里可以使用多个空格，但也不是必需的。
		
		- JSDoc 注释及其两边
		
		比如简写的类型声明`this.foo = /** @type {number} */ (bar);` 或 `function(/** string */ foo) {;` 或 `baz(/* buzz= */ true)`

		- 水平对齐:不鼓励
		**术语解释：**水平对齐是在代码中添加可变数量的附加空格的一种做法，目的是使某些标记直接出现在前几行中其他标记的下面。
		下面的示例中展示了正常的代码及带水平对齐的代码，后者是不推荐的:
		```javascript
			{
			tiny: 42, // this is great
			longer: 435, // this too
			};
			{
			tiny:   42,  // permitted, but future edits
			longer: 435, // may leave it unaligned
			};
		```
		这种做法是允许的，但 Google 风格里面不推荐。甚至在已经存在的代码中也不鼓励继续使用这种方式进行维护。
		**注意：对齐可以增加可读性，但是对后续的维护增加了困难。考虑到后续改写代码可能只会该代码中的一行。修改可能会导致规则允许下格式的崩坏。这常常会错使代码编写者（比如你）调整附近几行的空格，从而导致一系列的格式重写。这样，只是一行的修改就会有一个“爆炸半径”（对附近代码的影响）。这么做最多会让你做一些无用功，但是至少是个失败的历史版本，降低了阅读者的速度，也会导致一些合并冲突。**
		- 函数参数
		本规则更倾向于把所有函数的参数放在函数名的同一行。如果这么做让代码超出了80字符的限制，那么就必须做基于可读性的自动换行。为了节约空间，最好每行都接近80字符，或者一个参数一行来增加可读性。缩进4个空格。允许和圆括号对齐，但是不推荐。
下列就是最常见的函数参数对齐模式：
		```javascript
			// Arguments start on a new line, indented four spaces. Preferred when the
			// arguments don't fit on the same line with the function name (or the keyword
			// "function") but fit entirely on the second line. Works with very long
			// function names, survives renaming without reindenting, low on space.
			doSomething(
				descriptiveArgumentOne, descriptiveArgumentTwo, descriptiveArgumentThree) {
			// …
			}
			
			// If the argument list is longer, wrap at 80. Uses less vertical space,
			// but violates the rectangle rule and is thus not recommended.
			doSomething(veryDescriptiveArgumentNumberOne, veryDescriptiveArgumentTwo,
				tableModelEventHandlerProxy, artichokeDescriptorAdapterIterator) {
			// …
			}
			
			// Four-space, one argument per line.  Works with long function names,
			// survives renaming, and emphasizes each argument.
			doSomething(
				veryDescriptiveArgumentNumberOne,
				veryDescriptiveArgumentTwo,
				tableModelEventHandlerProxy,
				artichokeDescriptorAdapterIterator) {
			// …
			}
		```
	- 分组括号（Grouping parentheses）：推荐的写法
	只有代码作者和审阅者都觉得如果不分组不会引起歧义，并且加了分组也不会让代码变得更易读，那么分组可以省略。因为，不是每个人都将操作符优先级熟记于心。
	
	对于这些关键字，不要添加额外的分组 `delete`，`typeof`，`void`，`return`，`throw`，`case`，`in`，`of` 以及 `yield`。
	
	类型转换时需要使用括号强制分组：`/** @type {!Foo} */ (foo)`。

	- 注释
	本规则讨论注释的写法。JSDoc 相关的注释单独在上面已经讨论过了。

		- 块注释风格
		块状注释与被注释代码保持相同缩进。`/* ... */ `和 `//` 都是。对于多行的` /* ... */ `注释，后续注释行以`*`开头且与上一行缩进保持一致。参数的注释紧随参数之后，用于在函数名或参数名无法完全表达其意思的情况,例：
		```javascript
			/*
			* This is
			* okay.
			*/

			// And so
			// is this.

			/* This is fine, too. */
		```
		不要将JSDoc（`/ **…* /`）用于实现注释。

		- 参数名称注释
		每当值和方法名称未能充分传达含义时，都应使用“参数名称”注释，并且将方法重构得更清晰是不可行的。 它们的首选格式是值之前使用`=`：
		```javascript
			someFunction(obviousParam, /* shouldRender= */ true, /* name= */ 'hello');
		```
				
		为了与周围的代码保持一致，可以将它们放在值后面，而不使用`=`：
		```javascript
			someFunction(obviousParam, true /* shouldRender */, 'hello' /* name */);
		```

## 写在最后
保持一致性.

当你在编辑代码之前，先花一些时间查看一下现有代码的风格。比如，如果现有的代码给算术运算符添加了空格，你也应该添加。

代码风格中一个关键点是整理一份常用词汇表, 开发者认同它并且遵循, 这样在代码中就能统一表述。 这里提出了一些全局上的风格规则, 但也要考虑自身情况形成自己的代码风格。 但如果你添加的代码和现有的代码有很大的区别, 这就让阅读者感到很不和谐。 所以, 避免这种情况的发生。