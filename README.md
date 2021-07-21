# javasrcipt-design-pattern
JavaScript 设计模式与开发实践 读后笔记
# 前言

> 设计模式的定义：在 面向对象软件设计过程中 针对 特定问题 的 简洁而优雅 的 *解决方案* 。
> 设计模式的作用：让人们写出 *可复用*和*可维护性高*的程序。
模式应该用在正确的地方，不能在不该使用模式的地方刻意使用模式，必须理解模式的意图以及结合项目的实际场景。
> 分辨模式的关键是意图而不是结构。


# 第一部分 基础知识

学习设计模式的必要铺垫

## 1.面向对象的JavaScript

### 1.1 动态类型语言和鸭子类型

JavaScript是一门动态类型语言，对变量类型的宽容给实际编码带来了很大的灵活性。

* 静态类型语言：在编译时便已确认变量的类型，编译时能发现类型不匹配的错误，避免程序运行时可能发生的一些错误。缺点是迫使程序员依照契约编写程序，增加更多代码，并会将程序员的精力从思考业务逻辑分散开来。
* 动态类型语言：变量类型要到程序运行的时候，待变量被赋予某个值后才具有某种类型。编写的代码数量更少，看起来更简洁，更专注于逻辑表达，但无法保证变量的类型，运行期间可能会发生类型相关的错误。
* 鸭子类型（duck typing）: "如果它走起来路像鸭子，叫起来也是鸭子，那么它就是鸭子"
```javascript
var duck={
	duckSinging:function(){
		console.log('嘎嘎嘎')
	}
}

var chicken={
	duckSinging:function(){
		console.log('嘎嘎嘎')
	}
}

var choir=[] //合唱团

var joinChoir=function(animal){
	if(animal && typeof animal.duckSinging === 'function'){
		choir.push( animal );
		console.log( '恭喜加入合唱团')
		console.log( '合唱团已有成员数量：',choir.length);
	} 
}

joinChoir( duck ) //恭喜加入合唱团
joinChoir( chicken ) //恭喜加入合唱团
```
> 对于加入合唱团的动物，根本无需检查他们的类型，只要他们有duckSing这个方法就行，无论是小猫小狗只要能鸭子叫，都能顺利加入。
> 在动态类型语言的面向对象设计中，鸭子的概念至关重要。利用鸭子的思想，我们不必借助超类型的帮助，就能轻松在动态类型语言中实现一个重要的原则：* 面向接口变成，而不是面向实现编程*

### 1.2 多态
> 多态的含义：同一操作作用于不同的对象上面，可以产生不同的解释和不同的执行结果。 
 一段多态的代码：
 
 ```javascript
var makeSound=function( animal ){
	if( animal instanceof Duck){
		console.log('嘎嘎嘎')
	}else if( animal instanceof Chicken){
		console.log('咯咯咯')
	}
}

var Duck=function(){}
var Chicken=function(){}

makeSound(new Duck()) //嘎嘎嘎
makeSound(new Chicken()) //咯咯咯
} 
	
 ```
	
>	这段代码确实体现了“多态性”，当我们分别向鸭和鸡发出“叫唤”的消息时，它们根据此消息作出了各自不同的反应。但这样的“多态性”是无法令人满意的，如果后来又增加了一只动物，比如狗，显然狗的叫声是“汪汪汪”，此时我们必须得改动 makeSound 函数，才能让狗也发出叫声。修改代码总是危险的，修改的地方越多，程序出错的可能性就越大，而且当动物的种类越来越多时，makeSound 有可能变成一个巨大的函数。
> 多态背后的思想是将“做什么”和“谁去做以及怎样去做”分离开来，也就是将“不变的事物”与 “可能改变的事物”分离开来。在这个故事中，动物都会叫，这是不变的，但是不同类型的动物具体怎么叫是可变的。把不变的部分隔离出来，把可变的部分封装起来，这给予了我们扩展程序的能力，程序看起来是可生长的，也是符合开放—封闭原则的，相对于修改代码来说，仅仅增加代码就能完成同样的功能，这显然优雅和安全得多。

### 1.3 封装
#### 封装数据
创建作用域来实现封装数据，一般通过函数来创建作用域

#### 封装实现
封装不仅仅是隐藏数据，还包括隐藏 实现细节、设计细节 以及 隐藏对象的类型 等。
封装使得对象之间的耦合变松散，对象之间只听过暴露的API接口进行通信。

#### 封装类型
是静态类型语言的一种重要封装方式，通过抽象类和接口进行的。把对象的真正类型隐藏在抽象类或者接口之后，相比对象的类型，客户更关心对象
的行为。在许多静态语言的设计模式中，想方设法地去隐藏对象的类型，也是促使这些模式诞生的原因之一。比如工厂方法模式、组合模式等。

#### 封装变化
从设计模式的角度出发，封装在更重要的层面体现为封装变化。

*找到变化并封装之* 

### 1.4 原型模式和基于原型继承的JavaScript对象系统
在原型编程的思想中，类不是必须的，对象未必是从类中创建的，一个对象是通过克隆另一个对象所得到的。

在 ECMAScript5 中提供了 `Object.create`方法，可以用来克隆对象：

```javascript
var Plane = function(){ 
 this.blood = 100; 
 this.attackLevel = 1; 
 this.defenseLevel = 1; 
}; 

var plane = new Plane(); 
plane.blood = 500; 
plane.attackLevel = 10; 
plane.defenseLevel = 7; 

var clonePlane = Object.create( plane );

console.log( clonePlane ); // 输出：Object {blood: 500, attackLevel: 10, defenseLevel: 7}
```

在不支持 `Object.create` 方法的浏览器中，则可以使用以下代码：

```
Object.create = Object.create || function( obj ){ 
 var F = function(){}; 
 F.prototype = obj; 
 return new F(); 
}
```
#### 克隆是创建对象的手段
#### JavaScript的原型继承
- 所有的数据都是对象。
- 要得到一个对象，不是通过实例化类，而是找到一个对象作为原型并克隆它。
- 对象会记住它的原型
- 如果对象无法响应某个请求，它会把这个请求委托给它自己的原型。


## 2.this、call、apply

### 2.1 this

> `this` 总是指向一个对象，而具体指向哪个对象 是在运行时 *基于函数的执行环境 动态绑定* 的，而非函数被声明时的环境。

#### this的指向
- 作为对象的方法调用
- 作为普通函数调用
- 构造器调用
- Function.prototype.call 或 Function.prototype.apply 调用

##### 1.作为对象的方法调用

```javascript
var obj = { 
 a: 1, 
 getA: function(){ 
   alert ( this === obj ); // 输出：true 
   alert ( this.a ); // 输出: 1 
 } 
}; 
obj.getA();
```

##### 2.作为普通函数调用
当函数不作为对象的属性被调用时，也就是我们常说的普通函数方式，此时的 `this` 总是指向全局对象。在浏览器的 JavaScript 里，这个全局对象是 `window`对象。

```javascript
window.name = 'globalName'; 
var getName = function(){ 
 return this.name; 
}; 
console.log( getName() ); // 输出：globalName 
```

##### 3.构造器调用
  JavaScript 中没有类，但是可以从构造器中创建对象，同时也提供了 new 运算符，使得构造器看起来更像一个类。
   除了宿主提供的一些内置函数，大部分 JavaScript 函数都可以当作构造器使用。构造器的外表跟普通函数一模一样，它们的区别在于被调用的方式。当用 new 运算符调用函数时，该函数总会返回一个对象，通常情况下，构造器里的 this 就指向返回的这个对象，见如下代码：
 ```javascript
var MyClass = function(){ 
  this.name = 'sven'; 
}; 
var obj = new MyClass(); 
alert ( obj.name ); // 输出：sven
 ```
 但用 new 调用构造器时，还要注意一个问题，如果构造器显式地返回了一个 `object` 类型的对象，那么此次运算结果最终会返回这个对象，而不是我们之前期待的 this：
```javascript
var MyClass = function(){ 
this.name = 'sven'; 
return { // 显式地返回一个对象
	name: 'anne' 
} 
}; 
var obj = new MyClass(); 
alert ( obj.name ); // 输出：anne
```
##### 4.Function.prototype.call 或 Function.prototype.apply 调用
跟普通的函数调用相比，用 Function.prototype.call 或 Function.prototype.apply 可以动态地改变传入函数的 this：
```javascript
var obj1 = { 
 name: 'sven', 
 getName: function(){ 
 return this.name; 
 } 
}; 
var obj2 = { 
 name: 'anne' 
}; 
console.log( obj1.getName() ); // 输出: sven 
console.log( obj1.getName.call( obj2 ) ); // 输出：anne
```
### call 和 apply
* 1.改变this指向

```javascript
var obj1 = { 
 name: 'sven' 
}; 
var obj2 = { 
 name: 'anne' 
}; 
window.name = 'window'; 
var getName = function(){ 
 alert ( this.name ); 
}; 
getName(); // 输出: window 
getName.call( obj1 ); // 输出: sven 
getName.call( obj2 ); // 输出: anne
```
* 2.实现 `Function.prototype.bind`
```javascript
Function.prototype.bind=function(context){
	var self=this;
	return function(){
		return self.apply(context,arguments);
	}
}
var obj={
	name:'sven'
}
var func=function(){
	alert(this.name)
}.bind(obj)

func()
```
传入的对象context参数就是我们想修正的this对象。 Function.prototype.bind 的内部实现中，我们先把 func 函数的引用保存起来，然后返回一个新的函数。当我们在将来执行 func 函数时，实际上先执行的是这个刚刚返回的新函数。在新函数内部，self.apply( context, arguments )这句代码才是执行原来的 func 函数，并且指定 context
对象为 func 函数体内的 this。

* 3.借用其他对象的方法
借用构造函数：
```javascript
var A = function( name ){ 
 this.name = name; 
}; 

var B = function(){ 
 A.apply( this, arguments ); 
}; 

B.prototype.getName = function(){ 
 return this.name; 
}; 

var b = new B( 'sven' ); 

console.log( b.getName() ); // 输出： 'sven'
```

- `apply`接收两个参数，第一个参数指向函数体内this对象的指向，第二个参数为一个带下标的结合，可以为数组或者类数组。
- `call`参数不固定，第一个参数也是this指向，从第二个往后被依次传入函数。


## 3.闭包和高阶函数
### 一个闭包的经典应用
```javascript
for ( var i = 0, len = nodes.length; i < len; i++ ){ 
 (function( i ){ 
 nodes[ i ].onclick = function(){ 
 console.log(i); 
 } 
 })( i ) 
};
```
结果为 0，1，2，3，4，而不是5个5，因为在闭包的帮助下，将每次循环的i都封闭了，当事件函数顺着作用链中从内到外查找变量i时，会先找到被封闭在闭包环境中的i.
### 闭包的更多作用
#### 1.封装变量
闭包可以帮助把一些不需要暴露在全局的变量封装成“私有变量”。
假设有一个计算乘积的简单函数：
```javascript
var mult = function(){ 
 var a = 1; 
 for ( var i = 0, l = arguments.length; i < l; i++ ){ 
 a = a * arguments[i]; 
 } 
 return a; 
}; 
```
mult 函数接受一些 number 类型的参数，并返回这些参数的乘积。现在我们觉得对于那些相同的参数来说，每次都进行计算是一种浪费，我们可以加入缓存机制来提高这个函数的性能：
```javascript
var mult = (function(){ 
 var cache = {}; 
 return function(){ 
 	var args = Array.prototype.join.call( arguments, ',' ); 
 	if ( args in cache ){ 
 		return cache[ args ]; 
 	} 
 	var a = 1; 
	 for ( var i = 0, l = arguments.length; i < l; i++ ){ 
		 a = a * arguments[i]; 
 	 } 
 	return cache[ args ] = a; 
 } 
})();

```
提炼函数是代码重构中的一种常见技巧。如果在一个大函数中有一些代码块能够独立出来，我们常常把这些代码块封装在独立的小函数里面。独立出来的小函数有助于代码复用，如果这些小函数有一个良好的命名，它们本身也起到了注释的作用。如果这些小函数不需要在程序的其他地方使用，最好是把它们用闭包封闭起来。代码如下：
```javascript
var mult = (function(){ 
  	var cache = {}; 
	
  	var calculate = function(){ // 封闭 calculate 函数
  	var a = 1; 
  	for ( var i = 0, l = arguments.length; i < l; i++ ){ 
 		a = a * arguments[i]; 
 	} 
 	return a; 
 }; 
 
 	return function(){ 
 		var args = Array.prototype.join.call( arguments, ',' ); 
 		if ( args in cache ){ 
 			return cache[ args ]; 
 		}
	return cache[ args ] = calculate.apply( null, arguments ); 
 	} 
})();

```
#### 2.延续局部变量的寿命
img 对象经常用于进行数据上报，如下所示：
```javascript
var report = function( src ){ 
 var img = new Image(); 
 img.src = src; 
}; 
report( 'http://xxx.com/getUserInfo' ); 
```
但是通过查询后台的记录我们得知，因为一些低版本浏览器的实现存在 bug，在这些浏览器下使用 report 函数进行数据上报会丢失 30%左右的数据，也就是说，report 函数并不是每一次都成功发起了 HTTP 请求。丢失数据的原因是 img 是 report 函数中的局部变量，当 report 函数的调用结束后，img 局部变量随即被销毁，而此时或许还没来得及发出 HTTP 请求，所以此次请求就会丢失掉。

现在我们把 img 变量用闭包封闭起来，便能解决请求丢失的问题：
```javasript
 var report = (function(){ 
 	var imgs = []; 
 	return function( src ){ 
		 var img = new Image(); 
		 imgs.push( img ); 
 		 img.src = src; 
	 } 
})();
```
### 闭包和面向对象设计
过程与数据的结合是形容面向对象中的“对象”时经常使用的表达。
对象以方法的形式包含了过程，而闭包则是在过程中以环境的形式包含了数据。
下面来看看这段跟闭包相关的代码：
```javascript
var extent = function(){ 
  var value = 0; 
  return { 
  call: function(){ 
    value++; 
    console.log( value ); 
 }
 } 
}; 
var extent = extent(); 
extent.call(); // 输出：1 
extent.call(); // 输出：2 
extent.call(); // 输出：3
```

如果换成面向对象的写法，就是：

```javascript

var extent = { 
	 value: 0, 
	 call: function(){ 
	 	 this.value++; 
		 console.log( this.value ); 
	 } 
}; 
extent.call(); // 输出：1 
extent.call(); // 输出：2 
extent.call(); // 输出：3
```
### 用闭包完成命令模式
命令模式的意图是把请求封装为对象，从而分离请求的发起者和请求的接收者（执行者）之间的耦合关系。在命令被执行之前，可以预先往命令对象中植入命令的接收者。
```javascript
var Tv = { 
 open: function(){ 
 	console.log( '打开电视机' ); 
 },
close: function(){ 
	 console.log( '关上电视机' ); 
 } 
}; 

var createCommand = function( receiver ){ 
	 var execute = function(){ 
		 return receiver.open(); // 执行命令，打开电视机
	 } 
	 var undo = function(){ 
		 return receiver.close(); // 执行命令，关闭电视机
	 } 
	 return { 
	 execute: execute, 
	 undo: undo 
	 } 
}; 

var setCommand = function( command ){ 
	 document.getElementById( 'execute' ).onclick = function(){ 
	 command.execute(); // 输出：打开电视机
 } 
 
 document.getElementById( 'undo' ).onclick = function(){ 
	 command.undo(); // 输出：关闭电视机
 } 
}; 

setCommand( createCommand( Tv ) );
```

## 高阶函数
- 函数可以作为参数被传递
- 函数可以作为返回值输出

### 函数作为参数被传递
#### 回调函数
最常见的是将callback函数作为参数传递给方法，例如Ajax请求：
```javascript
var getUserInfo = function( userId, callback ){ 
	 $.ajax( 'http://xxx.com/getUserInfo?' + userId, function( data ){ 
	 if ( typeof callback === 'function' ){ 
		 callback( data ); 
	 } 
 }); 
} 

getUserInfo( 13157, function( data ){ 
 	alert ( data.userName ); 
});

```
在页面中创建100个div节点，并将这些div隐藏起来：
```javascript

 var appendDiv = function( callback ){ 
	 for ( var i = 0; i < 100; i++ ){ 
	 var div = document.createElement( 'div' ); 
	 div.innerHTML = i; 
	 document.body.appendChild( div ); 
	 if ( typeof callback === 'function' ){ 
		 callback( div ); 
	 } 
 } 
}; 

appendDiv(function( node ){ 
 	node.style.display = 'none'; 
});
```

这里将每个div隐藏抽离出来，单独写成callback，因为这段语句不能写着appendDiv中，避免难以复用。
#### Array.prototype.sort
`Array.prototype.sort`接收一个函数作为参数，这个函数封装了 数组元素的排序规则。
可变的是排序的规则，不变的是对数组排序，所以将可变的部分封装到函数参数中，动态的传入。
升序
```javascript
[ 1, 4, 3 ].sort( function( a, b ){ 
	 return a - b;
}); 
// 输出: [ 1, 3, 4 ]
```
降序
```javascript
[ 1, 4, 3 ].sort( function( a, b ){ 
	 return b - a;
}); 
// 输出: [ 4, 3, 1 ]
```
### 函数作为返回值输出
#### 1.判断数据的类型
```javascript
var Type = {}; 
for ( var i = 0, type; type = [ 'String', 'Array', 'Number' ][ i++ ]; ){ 
	 (function( type ){ 
		 Type[ 'is' + type ] = function( obj ){ 
			 return Object.prototype.toString.call( obj ) === '[object '+ type +']'; 
	 } 
 })( type ) 
}; 

Type.isArray( [] ); // 输出：true 
Type.isString( "str" ); // 输出：true
```
#### 2.getsingle
```javascript
var getSingle = function ( fn ) { 
	 var ret; 
	 return function () { 
		 return ret || ( ret = fn.apply( this, arguments ) ); 
	 }; 
};

var getScript = getSingle(function(){ 
	 return document.createElement( 'script' ); 
}); 

var script1 = getScript(); 
var script2 = getScript(); 

alert ( script1 === script2 ); // 输出：true
```
传入一个函数，返回一个函数，保证一个类仅有一个实例。《单例模式》

### 高阶函数实现AOP
AOP（面向切面编程）的主要作用是把一些跟核心业务逻辑模块无关的功能抽离出来，这些跟业务逻辑无关的功能通常包括日志统计、安全控制、异常处理等。把这些功能抽离出来之后，再通过*“动态织入”*的方式掺入业务逻辑模块中。

```javascript
Function.prototype.before = function( beforefn ){ 
	 var __self = this; // 保存原函数的引用
	 return function(){ // 返回包含了原函数和新函数的"代理"函数
		 beforefn.apply( this, arguments ); // 执行新函数，修正 this 
		 return __self.apply( this, arguments ); // 执行原函数
	 } 
}; 

Function.prototype.after = function( afterfn ){ 
	 var __self = this; 
	 return function(){ 
	 var ret = __self.apply( this, arguments ); 
		 afterfn.apply( this, arguments ); 
		 return ret; 
	 } 
}; 

var func = function(){ 
 console.log( 2 ); 
}; 

func = func.before(function(){ 
	 console.log( 1 ); 
}).after(function(){ 
 	console.log( 3 ); 
}); 

func();
````
### 高阶函数的其他应用
#### 1.currying 柯里化

**部分求值**
一个 currying 的函数首先会接受一些参数，接受了这些参数之后，该函数并不会立即求值，而是继续返回另外一个函数，刚才传入的参数在函数形成的闭包中被保存起来。待到函数被真正需要求值的时候，之前传入的所有参数都会被一次性用于求值。
```js
var currying = function( fn ){ 
	 var args = []; 
	 return function(){ 
	 if ( arguments.length === 0 ){ 
		 return fn.apply( this, args ); 
 }else{ 
	 [].push.apply( args, arguments ); 
	 return arguments.callee; 
 } 
 } 
}; 

var cost = (function(){ 
	 var money = 0; 
	 return function(){ 
		 for ( var i = 0, l = arguments.length; i < l; i++ ){ 
			 money += arguments[ i ]; 
		 } 
	 return money; 
	 } 
})(); 

var cost = currying( cost ); // 转化成 currying 函数

cost( 100 ); // 未真正求值
cost( 200 ); // 未真正求值
cost( 300 ); // 未真正求值

alert ( cost() ); // 求值并输出：600

```

#### 2. uncurrying 反柯里化
反柯里化的作用在与扩大函数的适用性，使本来作为特定对象所拥有的功能的函数可以被任意对象所用.
#### 3.函数节流
#### 4.分时函数
# 第二部分 设计模式
## 4.单例模式
保证一个类仅有一个实例，并提供一个访问它的全局访问点。
有一些对象我们往往只需要一个，比如 线程池、全局缓存、浏览器中的window对象。
实现单例模式
用一个变量来标志是否已经为某个类创建，如果是，那就返回之前被创建的实例。

我们通过 Singleton.getInstance 来获取 Singleton 类的唯一对象，这种方式相对简单，但有一个问题，就是增加了这个类的“不透明性”，Singleton 类的使用者必须知道这是一个单例类， 跟以往通过 new XXX 的方式来获取对象不同，这里偏要使用Singleton.getInstance 来获取对象。
所以以上这段 单例模式代码的意义不大，接下来编写更好的单例模式
透明的单例模式
用户从这个类中创建对象的时候，可以像使用其他任何普通类一样。在下面的例子中，可以使用createDiv单例类，它的作用是负责在页面中创建唯一的 div 节点，代码如下：

虽然现在完成了一个透明的单例类的编写，但它同样有一些缺点。 为了把 instance 封装起来，我们使用了自执行的匿名函数和闭包，并且让这个匿名函数返回 真正的 Singleton 构造方法，这增加了一些程序的复杂度，阅读起来也不是很舒服。
 	观察现在的 Singleton 构造函数： 

在这段代码中，CreateDiv 的构造函数实际上负责了两件事情。第一是创建对象和执行初始化 init 方法，第二是保证只有一个对象。虽然我们目前还没有接触过“单一职责原则”的概念， 但可以明确的是，这是一种不好的做法，至少这个构造函数看起来很奇怪。
 假设我们某天需要利用这个类，在页面中创建千千万万的 div，即要让这个类从单例类变成一个普通的可产生多个实例的类，那我们必须得改写 CreateDiv 构造函数，把控制创建唯一对象的那一段去掉，这种修改会给我们带来不必要的烦恼。
用代理实现单例模式

通过引入代理类的方式，我们同样完成了一个单例模式的编写，跟之前不同的是，现在我们把负责管理单例的逻辑移到了代理类 proxySingletonCreateDiv 中。这样一来，CreateDiv 就变成了一个普通的类，它跟 proxySingletonCreateDiv 组合起来可以达到单例模式的效果。
通用的惰性单例

使用：

## 5.策略模式
策略模式指的是定义一系列的算法，并且把它们封装起来。

计算奖金、缓动动画、表单校验、不同折扣计算等等方面都可以用到策略模式。避免if-else Switch。

策略模式利用组合、委托和多态等技术和思想，可以有效地避免多重条件选择语句。 
策略模式提供了对开放—封闭原则的完美支持，将算法封装在独立的 strategy 中，使得它们易于切换，易于理解，易于扩展。 
策略模式中的算法也可以复用在系统的其他地方，从而避免许多重复的复制粘贴工作。 
在策略模式中利用组合和委托来让 Context 拥有执行算法的能力，这也是继承的一种更轻便的替代方案。
文本输入框对应多种校验规则

## 6.代理模式
代理模式是为一个对象提供一个代用品或占位符，以便控制对它的访问。
举个例子： 在四月一个晴朗的早晨，小明遇见了他的百分百女孩，我们暂且称呼小明的女神为 A。两天之后，小明决定给 A 送一束花来表白。刚好小明打听到 A 和他有一个共同的朋 友 B，于是内向的小明决定让 B 来代替自己完成送花这件事情。
假设当 A 在心情好的时候收到花，小明表白成功的几率有 60%，而当 A 在心情差的时候收到花，小明表白的成功率无限趋近于 0。 
小明跟 A 刚刚认识两天，还无法辨别 A 什么时候心情好。如果不合时宜地把花送给 A，花被直接扔掉的可能性很大，这束花可是小明吃了 7 天泡面换来的。
但是 A 的朋友 B 却很了解 A，所以小明只管把花交给 B，B 会监听 A 的心情变化，然后选择 A 心情好的时候把花转交给 A，代码如下：

保护代理和虚拟代理
虽然这只是个虚拟的例子，但我们可以从中找到两种代理模式的身影。代理 B 可以帮助 A 过滤掉一些请求，比如送花的人中年龄太大的或者没有宝马的，这种请求就可以直接在代理 B 处被拒绝掉。这种代理叫作保护代理。A 和 B 一个充当白脸，一个充当黑脸。白脸 A 继续保持 良好的女神形象，不希望直接拒绝任何人，于是找了黑脸 B 来控制对 A 的访问。 
另外，假设现实中的花价格不菲，导致在程序世界里，new Flower 也是一个代价昂贵的操作， 那么我们可以把 new Flower 的操作交给代理 B 去执行，代理 B 会选择在 A 心情好时再执行 new Flower，这是代理模式的另一种形式，叫作虚拟代理。虚拟代理把一些开销很大的对象，延迟到 真正需要它的时候才去创建。代码如下：

 保护代理用于控制不同权限的对象对目标对象的访问，但在 JavaScript 并不容易实现保护代理，因为我们无法判断谁访问了某个对象。而虚拟代理是最常用的一种代理模式，本章主要讨论的也是虚拟代理。

虚拟代理实现图片预加载
