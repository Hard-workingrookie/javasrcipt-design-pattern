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
#### apply
`apply`接收两个参数，第一个参数指向函数体内this对象的指向，第二个参数为一个带下标的结合，可以为数组或者类数组。
`call`参数不固定，第一个参数也是this指向，从第二个往后被依次传入函数。
## 3.闭包和高阶函数
# 第二部分 设计模式
## 4.单例模式
