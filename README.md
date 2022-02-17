# interview-js-code-checklist
常见js手撕题汇总
* [手写继承](https://github.com/bsh00699/interview-js-code-checklist#%E6%89%8B%E5%86%99%E7%BB%A7%E6%89%BF)
* [手写new](https://github.com/bsh00699/interview-js-code-checklist#%E6%89%8B%E5%86%99new)
* [手写bind](https://github.com/bsh00699/interview-js-code-checklist#%E6%89%8B%E5%86%99bind)
* [手写call]()
* [手写apply]()
* [手写instanceof]()
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
#### 手写call
```
// 第一种
Function.prototype.mockCall = function (...args) {
  const _this = args.shift()
  return this.apply(_this, args)
}

function test(a){
  this.name = '>>>>>>>>'
  return a
}

const obj = {
  name: 'zxc'
}
console.log('+++', test.mockCall(obj, 'a'));
console.log('---', obj);

// 第二种
Function.prototype.mockCall = function (...args) {
  const ctx = args.shift()
  // this指向问题，这里的this就是mockCall的调用者
  /**
   * 核心：把mockCall的调用者的this, 指向参数中的第一个值
   * 比如 show.mockCall(obj), 就是把show中的this，指向了 obj
   */
  ctx.fn = this
  const res = ctx.fn(...args)
  delete ctx.fn
  return res
}
console.log('+++', test.mockCall(obj, 'a'));
console.log('---', obj);
```
#### 手写apply
```
// 第一种
Function.prototype.mockApply = function (...args) {
  const _this = args.shift()
  return this.call(_this, ...args)
}
const obj = {
  name: 'zxc'
}

function test () {
  return `输出name--${this.name}`
}
console.log('res', test.mockApply(obj));
// 第二种
Function.prototype.mockApply = function (...args) {
  console.log('--args', args);
  const ctx = args.shift()this.apply
  ctx.fn = this
  const res = ctx.fn(...args)
  delete ctx.fn
  return res
}
console.log('res', test.mockApply(obj, ['z', 'x']));
```
#### 手写instanceof
* JavaScript 的每个对象都包含了一个隐藏属性 __proto__,我们就把该隐藏属性 __proto__ 称之为该对象的原型 (prototype)，__proto__ 指向了内存中的另外一个对象，我们就把 __proto__ 指向的对象称为该对象的原型对象，那么该对象就可以直接访问其原型对象的方法或者属性。
* 函数有一个隐藏属性，那就是 prototype
* 核心: 对象.__proto__ = 构造函数.prototype
```
const mockInstanceof = (left, right) => {
  if (typeof left !== 'object' || left == null || right == null) {
    return false
  }
  let leftProto = left.__proto__
  let rightPrototype = right.prototype
  while (1) {
    if (leftProto === rightPrototype) return true
    if (leftProto === null) return false
    // 一直往上找就好
    leftProto = leftProto.__proto__
  }
}

class Parent { }
class Child extends Parent { }
const child = new Child()
console.log(mockInstanceof(child, Parent), mockInstanceof(child, Child), mockInstanceof(child, Array))
```
