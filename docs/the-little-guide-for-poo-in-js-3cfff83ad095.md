# JS 中面向对象的小指南

> 原文：<https://medium.com/hackernoon/the-little-guide-for-poo-in-js-3cfff83ad095>

在本指南中，我将尝试解释 es6 JavaScript 在**面向对象范例中的新特性。**

![](img/c303e35d2b93997fb5fd6423c9cfca31.png)

首先，

# 什么是编程范例？

范型是某事物的一个例子或模型，因此，在这种情况下，是创建计算机程序所遵循的一种模式。

# 什么是**面向对象？**

显然，你意识到这是一种编程模式，但像 exist 这种选择，我们还有很多其他选择，如函数式编程、反应式编程等。

# 这种范式的特点是什么？

我们在这个范例中所做的是以一种更接近现实的方式编程，我们根据类、对象、方法、属性等来编程。，尤其是集成了诸如:抽象、伪装、模块化、隐私、多态、继承等术语。

> JavaScript 的问题在于它并不是一种便便语言，为什么呢？因为在 JavaScript 中 all 是一个对象，所以我们可以使用著名的**原型来解决这个问题。**

在 ES5 中，我们可以使用工厂模式做下一个示例:

```
console.log('*** PERSON ***');function Person (name) {
 this.name = name;
}
*// We define our properties/methods*
Person.prototype = {
   eyes: 2,
   mouth: 1,
   sleep: function () {
    return 'zzz';
   }
};
*// We create a person*
const p1 = new Person('Nick');
// and we can do:
console.log(
  `name: ${p1.name}`,
  `eyes: ${p1.eyes}`,
  `mouth: ${p1.mouth}`,
   p1.sleep()
);console.log('*** EMPLOYEE ***')*// But now, if we have a employee 'class' we can inheritance person's properties.*
function Employee (name, salary) {
  this.name = name;
  this.salary = salary;
}
*// Prototype Inheritance*
Employee.prototype = Object.create(Person.prototype);
Employee.prototype.constructor = Employee; *// Set his own constructor
// So now, we just can do the same thing
// We create a employee*
const em1 = new Employee('John', 3000);
*// and we can do:*
console.log(
  `name: ${em1.name}`,
  `salary: ${em1.salary} USD`,
  `eyes: ${em1.eyes}`,
  `mouth: ${em1.mouth}`,
   em1.sleep()
);
```

现在，在 ES6 中，我们可以用一种简单的方式完成所有这些，但我们必须记住，这只是语法上的好处:

ES6 syntac 的最后一个例子。

```
class Person {
  constructor (name) {
    this.name = name;
    this.eyes = 2;
    this.mouth = 1;
  }
  sleep () {
    return 'zzz';
  }
}class Employee extends Person {
  constructor (name, salary) {
    super(name);
    this.salary = salary;
  }
}const p1 = new Person('Nick');
// and we can do:
console.log(
  `name: ${p1.name}`,
  `eyes: ${p1.eyes}`,
  `mouth: ${p1.mouth}`,
   p1.sleep()
);*// We create a employee*
const em1 = new Employee('John', 3000);
*// and we can do:*
console.log(
  `name: ${em1.name}`,
  `salary: ${em1.salary} USD`,
  `eyes: ${em1.eyes}`,
  `mouth: ${em1.mouth}`,
   em1.sleep()
);
```

在这种情况下，使用**扩展**关键字，我们只需说:‘好吧，我想继承 Person 类属性’。但是在幕后，这和我们在 es5 例子中使用原型做的是一样的。

## 静态方法:

```
class Dog {
  static whatIs() {
   return 'A dog is a beatiful animal';
  }
}// So, with static we can access to methods without the need to instantiate a new object of the class.
console.log( Dog.whatIs() );
```

## 私有方法

在 JavaScript 中，我们没有像 Java 和 C#那样的 private 关键字，重要的是，在 JavaScript 中，我们有一个用于*‘private’*值的约定，该约定是**在单词前使用下划线，**让我向您展示:

```
class Person {
 constructor (name, phone) { 
   this.name = name;
   this._phone = phone;
 }
}
const p1 = new Person('John', 544342212);
// But 'phone' is not a private propertie because we can do this:
console.log(p1._phone);
```

然而在 ES6 中，我们有一个名为 WeakMap 的对象，它允许我们创建私有属性，让我们看看:

```
*// Don't use private like variable name because is a reserved word*
const secret = new WeakMap();
class Person {
  constructor (name, phone) {
    this.name = name;
    secret.set(this, {_phonenumber: phone});
  }
}
const p1 = new Person('John', 544342212);
*// Now the phonenumber is a private propertie.*
console.log(p1._phone); *// Print's undefined*
```

## Getters 和 Setters

当我们有私有方法时，通常会创建一个返回私有值的公共方法，所以我们必须返回一个值并设置定义一个新值。

```
const secret = new WeakMap();
class Person {
  constructor (name, phone) {
    this.name = name;
    secret.set(this, {_phonenumber: phone});
  }
  get phoneNumber() {
    return secret.get(this)._phonenumber;
  }
  set phoneNumber(newNumber) {
    secret.get(this)._phonenumber = newNumber;
  }
}
const p1 = new Person('John', 544342212);
*// Now we can access to the phone by using the getter:* 
console.log(p1.phoneNumber); *// Print's the number
// Set a new number* p1.phoneNumber = 432232323;console.log(p1.phoneNumber)*; // We get the new number*
```

## 多态性

是对象在执行过程中引用其类的一个实例或其任何后代类的一个实例的能力。后代类可以重定义一个方法。

```
class Person {
  constructor(name) {
    this.name = name;
  }
  me() {
    return `My name is ${this.name}`;
  }
}
const axel = new Person('Axel');
console.log(axel.me());
  // ->  'My name is Axel'
class Employee extends Person {
  constructor (name, salary) {
    super(name);
    this.salary = salary;
  }
  me() {
    return `My name is ${this.name} and my salary is ${this.salary}`;
  } 
}const nick = new Employee('Nick', 3000);
console.log(nick.me());
  // ->  'My name is Nick and my salary is 3000'
```

## 一些概念:

**类:**创建新的类/模型。
**方法:**函数在一个类里面。
**构造函数:**类实例化时初始化对象的方法。
**扩展:**用于设置继承。
**super:** 调用父节点设置继承属性的方法。构造函数。supe 必须在构造函数方法的第一行。
**get:** 方法返回值。
**set:** 方法重新定义一个新的现有值。
**新增:**通过类构造器方法创建对象。

可以去我的 [Github](https://github.com/germancutraro) 找 JavaScript 例子。

谢谢你😊！