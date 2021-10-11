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
- 函数去抖（debounce）：让一个函数在一定间隔内没有被调用时，才开始执行被调用方法。
- 函数节流（throttle）：是让一个函数无法在很短的时间间隔内连续调用，当上一次函数执行后过了规定的时间间隔，才能进行下一次该函数的调用。 函数节流的出发点，就是让一个函数不要执行得太频繁，减少一些过快的调用来节流。


> 举个例子：
> 想象每天上班大厦底下的电梯。把电梯完成一次运送，类比为一次函数的执行和响应。假设电梯有两种运行策略throttle和> > debounce，超时设定为15秒，不考虑容量限制。
> debounce策略的电梯。如果电梯里有人进来，等待15秒。如果又人进来，15秒等待重新计时，直到15秒超时，开始运送。
> throttle策略的电梯。保证如果电梯第一个人进来后，15秒后准时运送一次，不等待。如果没有人，则待机。

#### 4.分时函数
```js
var timeChunk = function( ary, fn, count ){ 
   var obj, t; 
   var len = ary.length; 
  
   var start = function(){ 
     for ( var i = 0; i < Math.min( count || 1, ary.length ); i++ ){ 
       var obj = ary.shift(); 
       fn( obj ); 
     } 
   }; 
  
   return function(){ 
     t = setInterval(function(){ 
     if ( ary.length === 0 ){ // 如果全部节点都已经被创建好
       return clearInterval( t ); 
     } 
     start(); 
     }, 200 ); // 分批执行的时间间隔，也可以用参数的形式传入
   }; 
};
```
最后我们进行一些小测试，假设我们有 1000 个好友的数据，我们利用 timeChunk 函数，每一 批只往页面中创建 8 个节点：
```js
 var ary = []; 
 for ( var i = 1; i <= 1000; i++ ){ 
  ary.push( i ); 
 }; 

 var renderFriendList = timeChunk( ary, function( n ){ 
   var div = document.createElement( 'div' ); 
   div.innerHTML = n; 
   document.body.appendChild( div ); 
 }, 8 ); 

 renderFriendList();
```
# 第二部分 设计模式
## 4.单例模式
保证一个类仅有一个实例，并提供一个访问它的全局访问点。
有一些对象我们往往只需要一个，比如 线程池、全局缓存、浏览器中的window对象。
### 实现单例模式
用一个变量来标志是否已经为某个类创建，如果是，那就返回之前被创建的实例。
```
var Singleton = function( name ){ 
   this.name = name; 
   this.instance = null; 
}; 

Singleton.prototype.getName = function(){ 
   alert ( this.name ); 
}; 

Singleton.getInstance = function( name ){ 
   if ( !this.instance ){ 
   this.instance = new Singleton( name ); 
   } 
   return this.instance; 
}; 

var a = Singleton.getInstance( 'sven1' ); 
var b = Singleton.getInstance( 'sven2' ); 

alert ( a === b ); // true
```
我们通过 Singleton.getInstance 来获取 Singleton 类的唯一对象，这种方式相对简单，但有一个问题，就是增加了这个类的“不透明性”，Singleton 类的使用者必须知道这是一个单例类， 跟以往通过 new XXX 的方式来获取对象不同，这里偏要使用Singleton.getInstance 来获取对象。
所以以上这段 单例模式代码的意义不大，接下来编写更好的单例模式
### 透明的单例模式
用户从这个类中创建对象的时候，可以像使用其他任何普通类一样。在下面的例子中，可以使用createDiv单例类，它的作用是负责在页面中创建唯一的 div 节点，代码如下：
```js
var CreateDiv = (function(){ 
   var instance; 
  
   var CreateDiv = function( html ){ 
       if ( instance ){ 
       		return instance; 
       } 
       this.html = html; 
       this.init();
          return instance = this; 
       }; 
  
     CreateDiv.prototype.init = function(){ 
         var div = document.createElement( 'div' ); 
         div.innerHTML = this.html; 
         document.body.appendChild( div ); 
		 }; 
  
   return CreateDiv; 
})(); 

var a = new CreateDiv( 'sven1' ); 
var b = new CreateDiv( 'sven2' ); 

alert ( a === b ); // true
```
虽然现在完成了一个透明的单例类的编写，但它同样有一些缺点。 为了把 instance 封装起来，我们使用了自执行的匿名函数和闭包，并且让这个匿名函数返回 真正的 Singleton 构造方法，这增加了一些程序的复杂度，阅读起来也不是很舒服。
 	观察现在的 Singleton 构造函数： 
```js
var CreateDiv = function( html ){ 
   if ( instance ){ 
  	 return instance; 
   } 
   this.html = html; 
   this.init(); 
   return instance = this; 
}; 
```
在这段代码中，CreateDiv 的构造函数实际上负责了两件事情。第一是创建对象和执行初始化 init 方法，第二是保证只有一个对象。虽然我们目前还没有接触过“单一职责原则”的概念， 但可以明确的是，这是一种不好的做法，至少这个构造函数看起来很奇怪。
 假设我们某天需要利用这个类，在页面中创建千千万万的 div，即要让这个类从单例类变成一个普通的可产生多个实例的类，那我们必须得改写 CreateDiv 构造函数，把控制创建唯一对象的那一段去掉，这种修改会给我们带来不必要的烦恼。
### 用代理实现单例模式
```js
var CreateDiv = function( html ){ 
   this.html = html;
   this.init(); 
}; 

CreateDiv.prototype.init = function(){ 
   var div = document.createElement( 'div' ); 
   div.innerHTML = this.html; 
   document.body.appendChild( div ); 
}; 

接下来引入代理类 proxySingletonCreateDiv：
var ProxySingletonCreateDiv = (function(){ 
   var instance; 
   return function( html ){ 
     if ( !instance ){ 
   		  instance = new CreateDiv( html ); 
     } 
   return instance; 
 } 
})(); 

var a = new ProxySingletonCreateDiv( 'sven1' ); 
var b = new ProxySingletonCreateDiv( 'sven2' ); 
alert ( a === b );
```
通过引入代理类的方式，我们同样完成了一个单例模式的编写，跟之前不同的是，现在我们把负责管理单例的逻辑移到了代理类 proxySingletonCreateDiv 中。这样一来，CreateDiv 就变成了一个普通的类，它跟 proxySingletonCreateDiv 组合起来可以达到单例模式的效果。
### 通用的惰性单例
```js
var getSingle = function (fn) {
  var result;
  return function () {
    return result || (result = fn.apply(this, arguments));
  };
};

```
使用：
```js

var createLoginLayer = function () {
  var div = document.createElement("div");
  div.innerHTML = "我是登录浮窗";
  div.style.display = "none";
  document.body.appendChild(div);
  return div;
};

var createSingleLoginLayer = getSingle(createLoginLayer);
document.getElementById("loginBtn").onclick = function () {
  var loginLayer = createSingleLoginLayer();
  loginLayer.style.display = "block";
};

//    下面我们再试试创建唯一的 iframe 用于动态加载第三方页面：
var createSingleIframe = getSingle(function () {
  var iframe = document.createElement("iframe");
  document.body.appendChild(iframe);
  return iframe;
});

document.getElementById("loginBtn").onclick = function () {
  var loginLayer = createSingleIframe();
  loginLayer.src = "http://baidu.com";
};
```
## 5.策略模式
策略模式指的是定义一系列的算法，并且把它们封装起来。

计算奖金、缓动动画、表单校验、不同折扣计算等等方面都可以用到策略模式。避免if-else Switch。

策略模式利用组合、委托和多态等技术和思想，可以有效地避免多重条件选择语句。 
策略模式提供了对开放—封闭原则的完美支持，将算法封装在独立的 strategy 中，使得它们易于切换，易于理解，易于扩展。 
策略模式中的算法也可以复用在系统的其他地方，从而避免许多重复的复制粘贴工作。 
在策略模式中利用组合和委托来让 Context 拥有执行算法的能力，这也是继承的一种更轻便的替代方案。
### 文本输入框对应多种校验规则
```js
<html>

<body>
    <form action="http:// xxx.com/register" id="registerForm" method="post">
        请输入用户名：<input type="text" name="userName" />
        请输入密码：<input type="text" name="password" />
        请输入手机号码：<input type="text" name="phoneNumber" />
        <button>提交</button>
    </form>
    <script>
        /***********************策略对象**************************/
        var strategies = {
            isNonEmpty: function (value, errorMsg) {
                if (value === '') {
                    return errorMsg;
                }
            },
            minLength: function (value, length, errorMsg) {
                if (value.length < length) {
                    return errorMsg;
                }
            },
            isMobile: function (value, errorMsg) {
                if (!/(^1[3|5|8][0-9]{9}$)/.test(value)) {
                    return errorMsg;
                }
            }
        };
        /***********************Validator 类**************************/
        var Validator = function () {
            this.cache = [];
        };
        Validator.prototype.add = function (dom, rules) {
            var self = this;
            for (var i = 0, rule; rule = rules[i++];) {
                (function (rule) {
                    var strategyAry = rule.strategy.split(':');
                    var errorMsg = rule.errorMsg;
                    self.cache.push(function () {
                        var strategy = strategyAry.shift();
                        strategyAry.unshift(dom.value);
                        strategyAry.push(errorMsg);
                        return strategies[strategy].apply(dom, strategyAry);
                    });
                })(rule)
            }
        };
        Validator.prototype.start = function () {
            for (var i = 0, validatorFunc; validatorFunc = this.cache[i++];) {
                var errorMsg = validatorFunc();
                if (errorMsg) {
                    return errorMsg;
                }
            }
        };
        /***********************客户调用代码**************************/
        var registerForm = document.getElementById('registerForm');
        var validataFunc = function () {
            var validator = new Validator();
            validator.add(registerForm.userName, [{
                strategy: 'isNonEmpty',
                errorMsg: '用户名不能为空'
            }, {
                strategy: 'minLength:6',
                errorMsg: '用户名长度不能小于 10 位'
            }]);
            validator.add(registerForm.password, [{
                strategy: 'minLength:6',
                errorMsg: '密码长度不能小于 6 位'
            }]);
            validator.add(registerForm.phoneNumber, [{
                strategy: 'isMobile',
                errorMsg: '手机号码格式不正确'
            }]);
            var errorMsg = validator.start();
            return errorMsg;
        }
        registerForm.onsubmit = function () {
            var errorMsg = validataFunc();
            if (errorMsg) {
                alert(errorMsg);
                return false;
            }

        };
    </script>
</body>

</html>
```
## 6.代理模式
**代理模式是为一个对象提供一个代用品或占位符，以便控制对它的访问。**
举个例子： 在四月一个晴朗的早晨，小明遇见了他的百分百女孩，我们暂且称呼小明的女神为 A。两天之后，小明决定给 A 送一束花来表白。刚好小明打听到 A 和他有一个共同的朋 友 B，于是内向的小明决定让 B 来代替自己完成送花这件事情。
假设当 A 在心情好的时候收到花，小明表白成功的几率有 60%，而当 A 在心情差的时候收到花，小明表白的成功率无限趋近于 0。 
小明跟 A 刚刚认识两天，还无法辨别 A 什么时候心情好。如果不合时宜地把花送给 A，花被直接扔掉的可能性很大，这束花可是小明吃了 7 天泡面换来的。
但是 A 的朋友 B 却很了解 A，所以小明只管把花交给 B，B 会监听 A 的心情变化，然后选择 A 心情好的时候把花转交给 A，代码如下：
```js
var Flower = function () {};

var xiaoming = {
  sendFlower: function (target) {
    var flower = new Flower();
    target.receiveFlower(flower);
  },
};

var B = {
  receiveFlower: function (flower) {
    A.listenGoodMood(function () {
      // 监听 A 的好心情
      A.receiveFlower(flower);
    });
  },
};

var A = {
  receiveFlower: function (flower) {
    console.log("收到花 " + flower);
  },
  listenGoodMood: function (fn) {
    setTimeout(function () {
      // 假设 10 秒之后 A 的心情变好
      fn();
    }, 10000);
  },
};

xiaoming.sendFlower(B);
```
### 保护代理和虚拟代理
虽然这只是个虚拟的例子，但我们可以从中找到两种代理模式的身影。代理 B 可以帮助 A 过滤掉一些请求，比如送花的人中年龄太大的或者没有宝马的，这种请求就可以直接在代理 B 处被拒绝掉。这种代理叫作保护代理。A 和 B 一个充当白脸，一个充当黑脸。白脸 A 继续保持 良好的女神形象，不希望直接拒绝任何人，于是找了黑脸 B 来控制对 A 的访问。 
另外，假设现实中的花价格不菲，导致在程序世界里，new Flower 也是一个代价昂贵的操作， 那么我们可以把 new Flower 的操作交给代理 B 去执行，代理 B 会选择在 A 心情好时再执行 new Flower，这是代理模式的另一种形式，叫作虚拟代理。虚拟代理把一些开销很大的对象，延迟到 真正需要它的时候才去创建。代码如下：
```js
var B = {
  receiveFlower: function (flower) {
    A.listenGoodMood(function () {
      // 监听 A 的好心情
      var flower = new Flower(); // 延迟创建 flower 对象
      A.receiveFlower(flower);
    });
  },
};
```
 保护代理用于控制不同权限的对象对目标对象的访问，但在 JavaScript 并不容易实现保护代理，因为我们无法判断谁访问了某个对象。而虚拟代理是最常用的一种代理模式，本章主要讨论的也是虚拟代理。

### 虚拟代理实现图片预加载
```js
const myImage = ( () =>{
  const imgNode = document.createElement("img");
  document.body.appendChild(imgNode);
  return {
    setSrc:  (src)=> {
      imgNode.src = src;
    },
  };
})();

const proxyImage = ( () =>{
  const img = new Image();
  img.onload =  () =>{
    myImage.setSrc(this.src);
  };
  return {
    setSrc:  (src)=> {
      myImage.setSrc("file:// /C:/Users/svenzeng/Desktop/loading.gif");
      img.src = src;
    },
  };
})();

proxyImage.setSrc("http:// imgcache.qq.com/music/photo/k/000GGDys0yA0Nk.jpg");
```
### 代理的意义
 在说明代理的意义之前，我们先引入一个面向对象涉及的原则----**单一职责原则**
 **单一职责原则** ：一个类（对象和函数等），应该仅有一个引起它变化的原因。如果一个对象承担了多项职责，也就意味着这个对象将变得巨大，引起它变化的原因可能有多个。面向对象设计鼓励将行为分布到细粒度的对象之中，如果一个对象承担的职责过多，等于把这些职责耦合到了一起，这种耦合会导致脆弱和低内聚的设计。当变化发生时，设计可能会遭到意外的破坏。
 
### 虚拟代理合并HTTP请求
假设我们在做一个文件同步的功能，当我们选中一个 checkbox 的时候，它对应的文件就会被同步到另外一台备用服务器上面，我们先在页面中放置好checkbox节点，接下来，给这些 checkbox 绑定点击事件，并且在点击的同时往另一台服务器同步文件
```js
var synchronousFile = function (id) {
  console.log('开始同步文件，id 为: ' + id);
};
var checkbox = document.getElementsByTagName('input');
for (var i = 0, c; c = checkbox[i++];) {
  c.onclick = function () {
    if (this.checked === true) {
      synchronousFile(this.id);
    }
  }
};
```
我们可以通过一个代理函数 proxySynchronousFile 来收集一段时间之内的请求，最后一次性发送给服务器。比如我们等待 2 秒之后才把这 2 秒之内需要同步的文件 ID 打包发给服务器，如果不是对实时性要求非常高的系统，2 秒的延迟不会带来太大副作用，却能大大减轻服务器的压力。代码如下：
```js
var synchronousFile = function (id) {
  console.log('开始同步文件，id 为: ' + id);
};
var proxySynchronousFile = (function () {
  var cache = [], // 保存一段时间内需要同步的 ID 
    timer; // 定时器
  return function (id) {
    cache.push(id);
    if (timer) { // 保证不会覆盖已经启动的定时器
      return;
    }
    timer = setTimeout(function () {
      synchronousFile(cache.join(',')); // 2 秒后向本体发送需要同步的 ID 集合
      clearTimeout(timer); // 清空定时器
      timer = null;
      cache.length = 0; // 清空 ID 集合
    }, 2000);
  }
})();
var checkbox = document.getElementsByTagName('input');
for (var i = 0, c; c = checkbox[i++];) {
  c.onclick = function () {
    if (this.checked === true) {
      proxySynchronousFile(this.id);
    }
  }
};
```

### 虚拟代理在惰性加载中的应用
	miniConsole.js可以帮助开发者在浏览器上进行一些简单调试工作，`miniConsole.log(1)`,这句话会在页面中创建一个 div，并且把 log 显示在 div 里面，miniConsole.js的代码量大概有1000行左右，也许我们并不想一开始就加载这么大的JS文件，因为也许并不是每个用户都需要打印 log。我们希望在有必要的时候才开始加载它，比如当用户按下 F2 来主动唤出控制台的时候。
	在miniConsole.js加载之前，为了能够让用户正常地使用里面的API，通常我们的解决方案是用一个占位的miniConsole代理对象来给用户提前使用，这个代理对象提供给用户的接口，跟实际的miniConsole是一样的。
	用户使用这个代理对象来打印log的时候，并不会真正在控制台内打印日志，更不会在页面中创建任何DOM节点。即使我们想这样做也无能为力，因为真正的miniConsole.js还没有被加载。
	于是，我们可以把打印log的请求都包裹在一个函数里面，这个包装了请求的函数就相当于其他语言中命令模式中的`Command`对象。随后这些函数将全部被放到缓存队列中，这些逻辑都是在miniConsole代理对象中完成实现的。等用户按下F2唤出控制台的时候，才开始加载真正的miniConsole.js的代码，加载完成之后将遍历miniConsole代理对象中的缓存函数队列，同时依次执行它们。
	当然，请求的到底是什么对用户来说是不透明的，用户并不清楚它请求的是代理对象，所以他可以在任何时候放心地使用miniConsole对象。
	未加载真正的miniConsole.js之前的代码如下:
```js
let cache = []
const miniConsole = {
  log:function() {
    const args = arguments;
    cache.push(function () {
      return miniConsole.log.apply(miniConsole,args)
    })
  }
}

miniConsole.log(1);
```
当用户按下 F2时，开始加载真正的miniConsole.js，代码如下：

```js
let handler = function (ev) {
  if (ev.keyCode === 113) {
    const script = document.createElement('script');
    script.onload = function () {
      for (let i = 0, fn; fn = cache[i++];) {
        fn();
      }
    };
    script.src = 'miniConsole.js';
    document.getElementsByTagName('head')[0].appendChild(script);
  }
};

document.body.addEventListener('keydown', handler, false);
```
miniConsole.js代码：
```js
miniConsole={
log:function(){
//真正代码略
console.log(Array.prototype.join.call(arguments));}
};
```
虽然我们没有给出miniConsole.js的真正代码，但这不影响我们理解其中的逻辑。当然这里还要注意一个问题，就是我们要保证在F2被重复按下的时候，miniConsole.js只被加载一次。另外我们整理一下miniConsole代理对象的代码，使它成为一个标准的虚拟代理对象，代码如下：

```js
const miniConsole = (function () {
  var cache = [];
  const handler = function (ev) {
    if (ev.keyCode === 113) {
      var script = document.createElement('script');
      script.onload = function () {
        for (var i = 0, fn; fn = cache[i++];) {
          fn();
        }
      };
      script.src = 'miniConsole.js';
      document.getElementsByTagName('head')[0].appendChild(script);
      document.body.removeEventListener('keydown', handler);//只加载一次miniConsole.js }
    };
    document.body.addEventListener('keydown', handler, false);
    return {
      log: function () {
        var args = arguments;
        cache.push(function () {
          return miniConsole.log.apply(miniConsole, args);
        });
      }
    }
  }) ();

  miniConsole.log(11);//开始打印log
```

### 缓存代理
	缓存代理可以为一些开销大的运算结果提供暂时的存储，在下次运算时，如果传递进来的参数跟之前一致，则可以直接返回前面存储的运算结果。
#### 缓存代理的例子---计算乘积
 先创建一个用于求乘积的函数：
 ```js
 const mult = function (...args:number[]) {
  console.log('开始计算乘积')
  let a = 1
  for (let i = 0, l = args.length; i < l; i++) {
    a *= args[i]
  }
  return a
}
mult(2, 3) //6

 ```
现在加入缓存代理函数：
```js
const proxyMult = (function () {
  let cache = {};
  return function () {
    const args = Array.prototype.join.call(arguments, ','); 
    if (args in cache) {
      return cache[args];
    }
    return cache[args] = mult.apply(this, arguments);
  }
})();
```

#### 缓存代理用于Ajax异步请求数据

	我们在常常在项目中遇到分页的需求，同一页的数据理论上只需要去后台拉取一次，这些已经拉取到的数据在某个地方被缓存之后，下次再请求同一页的时候，便可以直接使用之前的数据。
	显然这里也可以引入缓存代理，实现方式跟计算乘积的例子差不多，唯一不同的是，请求数据是个异步的操作，我们无法直接把计算结果放到代理对象的缓存中，而是要通过回调的方式。具体代码不再赘述，读者可以自行实现。


### 用高阶函数动态创建代理
	通过传入高阶函数这种更加灵活的方式，可以为各种计算方法创建缓存代理。现在这些计算方法被当作参数传入一个专门用于创建缓存代理的工厂中，这样一来，我们就可以为乘法、加法、减法等创建缓存代理，代码如下：

```js
/****************计算乘积*****************/
var mult = function () {
  var a = 1;
  for (var i = 0, l = arguments.length; i < l; i++) {
    a = a * arguments[i];
  }
  return a;
};
```
```js

/****************计算加和*****************/
var plus = function () {
  var a = 0;
  for (var i = 0, l = arguments.length; i < l; i++) {
    a = a + arguments[i];
  }
  return a;
};
```

```js
/****************创建缓存代理的工厂*****************/
var createProxyFactory = function (fn) {
  var cache = {};
  return function(){
    var args = Array.prototype.join.call(arguments, ','); if (args in cache) {
      return cache[args];
    }
    return cache[args] = fn.apply(this, arguments);
  }
};
```


```js
var proxyMult=createProxyFactory(mult),proxyPlus=createProxyFactory(plus);
```
```
alert(proxyMult(1, 2, 3, 4));//输出：24 
alert(proxyMult(1, 2, 3, 4));//输出：24 
alert(proxyPlus(1, 2, 3, 4));//输出：10 
alert(proxyPlus(1, 2, 3, 4));//输出：10
```
## 迭代器模式
