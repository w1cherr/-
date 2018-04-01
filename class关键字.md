#深入理解 JavaScript 中的 class

在 ES6 规范中，引入了 class 的概念。使得 JS 开发者终于告别了，直接使用原型对象模仿面向对象中的类和类继承时代。

但是JS 中并没有一个真正的 class 原始类型， class 仅仅只是对原型对象运用语法糖。所以，只有理解如何使用原型对象实现类和类继承，才能真正地用好 class。

##ES6：`class`
通过类来创建对象，使得开发者不必写重复的代码，以达到代码复用的目的。它基于的逻辑是，两个或多个对象的结构功能类似，可以抽象出一个模板，依照模板复制出多个相似的对象。就像自行车制造商一遍一遍地复用相同的蓝图来制造大量的自行车。

使用 ES6 中的 class 声明一个类，是非常简单的事。它的语法如下:

```javascript
class Person {
  constructor(name){
    this.name = name
  }

  hello(){
    console.log('Hello, my name is ' + this.name + '.');
  }
}

var xiaoMing = new Person('xiaoMing');
xiaoMing.hello() // Hello, my name is xiaoMing.
```
`xiaoMing` 是通过类 `Person` 实例化出来的对象。对象 `xiaoMing` 是按照类 `Person` 这个模板，实例化出来的对象。实例化出来的对象拥有类预先订制好的结构和功能。

ES6 的语法很简单，但是在实例化的背后，究竟是什么在起作用呢？

##`class` 实例化的背后原理
使用 class 的语法，让开发者告别了使用 prototype 模仿面向对象的时代。但是，class 并不是 ES6 引入的全新概念，它的原理依旧是原型继承。


`typeof class == "function"`

通过类型判断，我们可以得知，class 的并不是什么全新的数据类型，它实际只是 

```javascript
function (或者说 object)。

class Person {
  // ...
}

typeof Person // function
```
为了更加直观地了解 Person 的实质，可以将它在控制台打印出来，如下。


![zxc](/Users/wakexbear/Documents/笔记/images/68503303-58a19aadbe1ee_articlex.jpeg)
`Person` 的属性并不多，除去用 [[...]] 包起来的内置属性外，大部分属性根据名字就能明白它的作用。需要我们重点关注的是 `prototype`和` __proto__` 两个属性。

(关于 __proto__ 可以在本文的姊妹篇 找到答案)

##实例化的原理： `prototype`
先来讲讲 prototype 属性，它指向一个特殊性对象：原型对象。

原型对象所以特殊，是因为它拥有一个普通对象没有的能力：将它的属性共享给其他对象。

在 ES6 规范 中，对 原型对象 是如下定义的：

`object that provides shared properties for other objects`
原型对象是如何将它的属性分享给其他对象的呢？

这里使用 ES5 创建一个类，并将它实例化，来看看它的实质。

```javascript
function Person() {
  this.name = name
}

// 1. 首先给 Person.prototype 原型对象添加了 describe 方法 。
Person.prototype.describe = function(){
  console.log('Hello, my name is ' + this.name + '.');
}

// 2. 实例化对象的 __proto__ 指向 Person.prototype
var jane = new Person('jane');
jane.__proto__ === Person.prototype;


// 3. 读取 describe 方法时，实际会沿着原型链查找到 Person.prototype 原型对象上。
jane.describe() // Hello, my name is jane.
```
上述使用 JS 模仿面向对象实例化的背后，实际有三个步骤：

* 首先给 Person.prototype 属性所指的原型对象上添加了一个方法 describe。

* 在使用 new 关键字创建对象时，会默认给该对象添加一个原型属性 __proto__，该属性指向 Person.prototype 原型对象。

* 在读取 describe 方法时，首先会在 jane 对象查找该方法，但是 jane 对象并不直接拥有 describe 方法。所以会沿着原型链查找到 Person.prototype 原型对象上，最后返回该原型对象的 describe 方法。

JS 中面向对象实例化的背后原理，实际上就是 原型对象。

为了方便大家理解，从网上扒了一张的图片，放到这来便于大家理解。
![](/Users/wakexbear/Documents/笔记/images/bVI9us.png)


##prototype 与 __proto__ 区别
理解上述原理后，还需要注意 prototype 与 __proto__ 属性的区别。

__proto__ 所指的对象，真正将它的属性分享给它所属的对象。所有的对象都有 __proto__ 属性，它是一个内置属性，被用于继承。

prototype 是一个只属于 function 的属性。当使用 new 方法调用该构造函数的时候，它被用于构建新对象的 __proto__。另外它不可写，不可枚举，不可配置。

```javascript
( new Foo() ).__proto__ === Foo.prototype
( new Foo() ).prototype === undefined
```
##class 定义属性
当我们使用 class 定义属性（方法）的时候，实际上等于是在 class 的原型对象上定义属性。

```javascript
class Foo {
  constructor(){ /* constructor */  }

  describe(){ /* describe */  }
}

// 等价于
function Foo (){
  /* constructor */
}

Foo.prototype.describe = function(){  /* describe */  }
```
constructor 是一个比较特殊的属性，它指向构造函数（类）本身。可以通过以下代码验证。

```Foo.prototype.constructor === Foo // true```

##类继承
在传统面向对象中，类是可以继承类的。这样子类就可以复制父类的方法，达到代码复用的目的。

ES6 也提供了类继承的语法 extends，如下：

```javascript
class Foo {
  constructor(who){
    this.me = who;
  }

  identify(){
    return "I am " + this.me;
  }
}


class Bar extends Foo {
  constructor(who){
    // super() 指的是调用父类
    // 调用的同时，会绑定 this 。
    // 如：Foo.call(this, who)
    super(who);
  }

  speak(){
    alert( "Hello, " + this.identify() + "." );
  }
}

var b1 = new Bar( "b1" );

b1.speak();
```

当实例 b1 调用 `speak` 方法时，b1 本身没有 `speak`，所以会到 `Bar.prototype` 原型对象上查找，并且调用原型对象上的 speak 方法。调用 `identify` 方式时，由于 `this` 指向的是 b1 对象。所以也会先在 b1 本身查找，然后沿着原型链，查找 `Bar.prototype`，最后在 `Foo.prototype` 原型对象上找到 `identify` 方法，然后调用。

实际上，在 JavaScript 中，类继承的本质依旧是原型对象。

他们的关系如下图所示:

![](/Users/wakexbear/Documents/笔记/images/bVCzB8.png)

