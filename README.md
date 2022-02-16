# interview-js-code-checklist
常见js手撕题汇总
* [手写继承]()
* [手写new]()
* [手写bind]()
* [手写call]()
#### 手写继承
* 原型继承，核心 Son.prototype = new Father()
* 要想为子类新增属性和方法，必须要在new Father()这样的语句之后执行
```
function Father () { 
  this.name = "father"; 
}
Father.prototype.say = function () {
  console.log('say hello')
}
function Son () { 
  this.name = "son"; 
}

// 注意前后顺序
Son.prototype = new Father()
Son.prototype.eat = function () {
  console.log('eat apple')
}
const son = new Son()
console.log(son.say())
console.log(son.eat())
```
* class 继承,核心 A extends B 
```
class Animal{
  constructor(name){
    this.name=name;
  }
  eat(){
    console.log('animal can eat');
  }
}

class Dog extends Animal{
    constructor(name){
      super(name)
    }
    break(){
      console.log('dog can break');
    }
}
const dog = new Dog('xiaomei');
console.log(dog.eat())
console.log(dog.break())
```
#### 手写new
* instance.__proto__ = Func.prototype
* Func.apply(instance, args)
```
function myNew(Func, ...args) {
  const instance = {}
  instance.__proto__ = Func.prototype
  Func.apply(instance, args)
  return instance
}

// 或者这样
function myNew(Func, ...args) {
  const instance = {};
  if (Func.prototype) {
    Object.setPrototypeOf(instance, Func.prototype)
  }
  const res = Func.apply(instance, args)
  if (typeof res === "function" || (typeof res === "object" && res !== null)) {
    return res
  }
  return instance
}

// 测试 func
function Person (name) {
  this.name = name
}
Person.prototype.sayName = function() {
  console.log(`My name is ${this.name}`)
}
const me = myNew(Person, 'Jack')
me.sayName()
console.log(me)
```
#### 手写bind
```
Function.prototype.mockBind = function(){
  const args = Array.prototype.slice.call(arguments)
  const _this = args.shift()
  const fn = this
  return () => {
    return fn.apply(_this, args)
  }
}

//或者使用 es6扩展 ...
Function.prototype.mockBind = function(...args){
  const _this = args.shift()
  const fn = this
  return () => {
    return fn.apply(_this, args)
  }
}

// 验证
function test(a,b,c){
  console.log('this--', this);
  console.log(a,b,c);
}

const res = test.mockBind({name: 'zxc'}, 1,2,3)
console.log(res());
```