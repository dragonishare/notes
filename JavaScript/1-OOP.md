# 面向对象编程

面向对象编程OOP(Object Oriented Programming)

## 特性

* 封装 encapsulation
* 继承 inheritance
* 多态 polymorphism

# ES6中类与对象

## 对象

1. 对象是无序属性的集合，其属性可以包含基本值、对象或者函数。
2. 每个对象都是基于一个引用类型创建的。

## 类

在ES6中类声明不能被提升。

constructor函数中的属性和方法，在new实例化后会作为实例对象的属性返回；不在constructor函数中的方法，是在类的prototype上定义的。

一般 `constructor` 方法返回实例对象 `this` ，但是也可以指定 `constructor` 方法返回一个全新的对象，让返回的实例对象不是该类的实例。

```javascript
// 使用class关键字创建类，类名习惯首字母大写。
// 创建类时类名后用{}，实例化时类名后用()。
class Animal {
  // 类里面默认保留constructor函数，可以接受传递过来的参数，同时返回实例对象。
  // 一般把共有属性写在constructor里边。
  constructor(kind) {
    this.kind = kind;
  }
  
  // 类中的函数不需要function关键字定义，多个函数之间也不需要逗号分隔。
  call(voice) {
    console.log(this.kind, voice);
  }
}

// 必须使用new实例化对象; new 生成实例时，会自动调用 constructor函数，如果我们不写，类也会自动生成这个函数。
var cat = new Animal('cat');
cat.call('mew'); // => cat mew

```

### 继承

```javascript
class Animal {
  constructor(kind) {
    this.kind = kind;
  }
  
  call(voice) {
    console.log(this.kind, voice);
  }
}

//继承 使用extends关键字
class Dog extends Animal {
  
}

// 子类默认继承了父类的属性和方法，并且可以给父类的constructor传递参数
var husky = new Dog('Husky');
husky.call('woof'); // => Husky woof
```

子类没有定义 constructor时，会默认添加。

```javascript
class Dog extends Animal {
}

// 等同于
class Dog extends Animal {
  constructor(...args) {
    super(...args);
  }
}
```

子类里边的方法会对父类里边的同名方法进行遮挡（优先执行子类的方法）。

### super关键字

`super` 这个关键字，既可以当做函数使用，也可以当做对象使用。

1、当super作为方法使用时，有以下六个注意点：

（1）super()方法相当于父类的构造函数。

（2）只有在子类的构造函数中才能调用super()方法。

（3）如果子类显式地定义了构造函数，那么必须调用super()方法，否则会报错。

（4）如果子类没有定义构造函数，那么会自动调用super()方法。

（5）当子类的构造函数显式地返回一个对象时，就能避免调用super()方法。

（6）在使用this之前，必须先调用super()方法。

```javascript
class Animal {
  constructor(kind) {
    this.kind = kind;
  }
  
  call(voice) {
    console.log(this.kind, voice);
  }
}

// ES6中子类的构造函数必须执行一次 super函数，如果没有写constructor，new实例化时会自动添加constructor和super。
class Dog extends Animal {
  constructor(color) {
    // 因为子类没有自己的 this 对象，而是继承父类的 this 对象，然后对其进行加工,而 super 就代表了父类的构造函数，使用this之前必须先调用super方法。
    super(kind); // 如果写了constructor，要手动传统参数
  }
}

// 子类默认继承了父类的属性和方法，并且可以给父类的constructor传递参数
var husky = new Dog('Husky');
husky.call('woof'); // => Husky woof
```

2、当super作为对象使用时，在不同的位置，其指向将不同，具体分为两种情况：

（1）如果在子类的原型方法中使用super，那么super指向父类的原型。

（2）如果在子类的静态方法中使用super，那么super指向父类。

# 构造函数与原型

**创建对象的方式：**

```javascript
// 1、使用 new Object() 创建对象
var person = new Object();
person.name = 'Gates';
person.sayName = function() {
  console.log(this.name);
};

// 2、使用对象字面量创建对象（现在首选方式）
var person = {
  name: 'Gates',
  sayName: function() {
    console.log(this.name);
  }
}

// 3、利用构造函数创建对象
```

**构造函数**

```javascript
// 按照惯例，构造函数始终都应该以一个大写字母开头
function Person(name) {
  this.name = name;
  this.sayName = function() {
    console.log(this.name);
  };
}

/* 要创建Person的新实例，必须使用new操作符。以这种方式调用构造函数实际上会经历4个步骤：
1、创建一个新对象；
2、将构造函数的作用域赋给新对象（因此this就指向了这个新对象）;
3、执行构造函数中的代码（为这个新对象添加属性）;
4、返回新对象（所以构造函数里面不需要return）。
*/
var person = new Person('Gates');
```

**原型**

每个函数都有一个**prototype（原型）属性**，这个属性是一个指针，指向一个对象，而这个对象的用途是**包含可以由特定类型的所有实例共享的属性和方法**。字面意思理解是，prototype是通过调用构造函数而创建的那个对象实例的**原型对象**。使用原型对象的好处是可以让所有对象实例共享它所包含的属性和方法。

在默认情况下，所有原型对象都会自动获得一个**constructor（构造函数）属性**，这个属性是一个指向prototype属性所在函数的指针。

当调用构造函数创建一个新实例后，该实例的内部将包含一个指针（内部属性），指向构造函数的原型对象。ES5中管这个指针叫**[[Prototype]]**。虽然在脚本中没有标准的方式访问[[Prototype]]，但是Firefox，Safari，Chrome在每个对象上都支持一个属性**`__proto__`**；而在其他实现中，这个属性对脚本则是完全不可见的。