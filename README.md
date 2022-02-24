# interview-js-code-checklist
常见js手撕题汇总
* [手写继承](https://github.com/bsh00699/interview-js-code-checklist#%E6%89%8B%E5%86%99%E7%BB%A7%E6%89%BF)
* [手写new](https://github.com/bsh00699/interview-js-code-checklist#%E6%89%8B%E5%86%99new)
* [手写bind](https://github.com/bsh00699/interview-js-code-checklist#%E6%89%8B%E5%86%99bind)
* [手写call](https://github.com/bsh00699/interview-js-code-checklist#%E6%89%8B%E5%86%99call)
* [手写apply](https://github.com/bsh00699/interview-js-code-checklist#%E6%89%8B%E5%86%99apply)
* [手写instanceof](https://github.com/bsh00699/interview-js-code-checklist#%E6%89%8B%E5%86%99instanceof)
* [手写Promise](https://github.com/bsh00699/interview-js-code-checklist#%E6%89%8B%E5%86%99promise)
* [手写Promise.all()](https://github.com/bsh00699/interview-js-code-checklist#%E6%89%8B%E5%86%99promiseall)
* [手写防抖](https://github.com/bsh00699/interview-js-code-checklist#%E9%98%B2%E6%8A%96)
* [手写节流](https://github.com/bsh00699/interview-js-code-checklist#%E8%8A%82%E6%B5%81)
* [发布订阅](https://github.com/bsh00699/interview-js-code-checklist#%E5%8F%91%E5%B8%83%E8%AE%A2%E9%98%85)
* [单例模式](https://github.com/bsh00699/interview-js-code-checklist#%E5%8D%95%E4%BE%8B%E6%A8%A1%E5%BC%8F)
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
#### 手写Promise
```

class MockPromise {
  constructor(exec) {
    this.status = 'pending'
    this.value = null
    this.resolvedTasks = []
    this.rejectedTasks = []
    this._resolve = this._resolve.bind(this)
    this._reject = this._reject.bind(this)
    // catch err 到 then 中的 onRejected 的reason
    try {
      exec(this._resolve, this._reject)
    } catch (error) {
      this._reject(error)
    }
  }

  _resolve(value) {
    if (this.status === MockPromise.PENDING) {
      this.status = MockPromise.FULFILLED
      this.value = value
      this.resolvedTasks.forEach((fn) => {
        fn(value)
      })
    }
  }

  _reject(reason) {
    if (this.status === MockPromise.PENDING) {
      this.status = MockPromise.REJECTED
      this.value = reason
      this.rejectedTasks.forEach((fn) => {
        fn(reason)
      })
    }
  }

  then(onFulfilled, onRejected) {
    if (typeof onFulfilled !== 'function') {
      onFulfilled = (value) => {
        return value
      }
    }

    if (typeof onRejected !== 'function') {
      onRejected = (resaon) => {
        throw resaon
      }
    }
    /**
     * 实现链式调用 return 一个promise 
     * 判断 onFulfilled() 的返回值类型就好了 
     */
    return new MockPromise((resolve, reject) => {
      if (this.status === MockPromise.FULFILLED) {
        setTimeout(() => {
          try {
            const res = onFulfilled(this.value)
            if (res instanceof MockPromise) {
              res.then(value => {
                resolve(value)
              })
            } else {
              resolve(res)
            }
          } catch (error) {
            reject(error)
          }
        })
      }

      if (this.status === MockPromise.REJECTED) {
        setTimeout(() => {
          try {
            const res = onRejected(this.value)
            if (res instanceof MockPromise) {
              res.then(value => {
                resolve(value)
              })
            } else {
              resolve(res)
            }
          } catch (error) {
            reject(error)
          }
        })
      }
      /**解决 resolve() 异步处理
       * 比如 setTimeout(() => { resolve(3) }, 1000)
       * 这时候 先执行 的是 then 函数，我们可以先把要执行的函数 压入 一个数组中
       * 等到执行到 resolve 的时候，再去判断状态，然后遍历数组将数组中的函数执行
       */
      if (this.status === MockPromise.PENDING) {
        // push 的肯定是一个函数
        this.resolvedTasks.push((value) => {
          setTimeout(() => {
            const res = onFulfilled(value)
            console.log('res===',res );
            MockPromise.handlePromise(res, resolve, reject)
            // try {
            //   const res = onFulfilled(value)
            //   if (res instanceof MockPromise) {
            //     res.then(value => {
            //       resolve(value)
            //     })
            //   } else {
            //     resolve(res)
            //   }
            // } catch (error) {
            //   reject(error)
            // }
          })
        })

        this.rejectedTasks.push((reason) => {
          setTimeout(() => {
            try {
              const res = onRejected(reason)
              if (res instanceof MockPromise) {
                res.then(value => {
                  resolve(value)
                })
              } else {
                resolve(res)
              }
            } catch (error) {
              reject(error)
            }
          })
        })
      }
    })
  }

}

MockPromise.PENDING = 'pending'
MockPromise.FULFILLED = 'fulfilled'
MockPromise.REJECTED = 'rejected'
MockPromise.handlePromise = function (res, resolve, reject) {
  try {
    if (res instanceof MockPromise) {
      res.then(value => {
        resolve(value)
      })
    } else {
      resolve(res)
    }
  } catch (error) {
    reject(error)
  }
}


const dd = new MockPromise((resolve, reject) => {
  console.log('1');
  setTimeout(() => {
  resolve(3)
  }, 200)

}).then((val) => {
  console.log('val--', val);
  // return 'haha' + val
  return new MockPromise((resolve, reject) => {
    resolve('新的promise' + val)
  })
}, reason => {
  console.log('reason--', reason);
})
  .then(val => {
    console.log('then--', val);
  })

console.log('2');
```
#### 手写Promise.all()
```
var promise1 = Promise.resolve('promise1');
var promise2 = new Promise(function(resolve, reject) {
  setTimeout(resolve, 2000, 'promise2');
});
var promise3 = new Promise(function(resolve, reject) {
  setTimeout(resolve, 1000, 'promise3');
});

Promise.all([promise1, promise2, promise3]).then(function(values) {
  console.log(values);
});
// expected output: Array ["promise1","promise2", "promise3"]

function promiseAll(promises) {
  return new Promise(function(resolve, reject) {
    if (!isArray(promises)) {
      return reject(new TypeError('arguments must be an array'));
    }
    var resolvedCounter = 0;
    var promiseNum = promises.length;
    var resolvedValues = new Array(promiseNum);
    for (var i = 0; i < promiseNum; i++) {
      (function(i) {
        Promise.resolve(promises[i]).then(function(value) {
          resolvedCounter++
          resolvedValues[i] = value
          if (resolvedCounter == promiseNum) {
            return resolve(resolvedValues)
          }
        }, function(reason) {
          return reject(reason)
        })
      })(i)
    }
  })
}
```
#### 防抖
* 花名搜索
```
const debounce = (fn, delay) => {
  let timer = null
  return function () {
    if (timer) clearTimeout(timer)
    timer = setTimeout(() => {
      fn.apply(this, arguments)
    }, delay)
  }
}

const myDebounce = debounce(() => {
  console.log('+++')
}, 1000)
myDebounce()
```
#### 节流
* 抢购热卖商品、秒杀，在指定的时间间隔内只执行一次
```
const throttle = (fn, delay) => {
  let startTime = 0
  return function () {
    const nowTime = new Date().getTime()
    if (nowTime - startTime > delay) {
      fn.apply(this, arguments)
      startTime = nowTime
    }
  }
}

const myThrottle = throttle(() => {
  console.log('>>>')
}, 10)

for (let i = 0; i < 1000000; i++) {
  myThrottle()
}
```
#### 深拷贝
```
const deepClone = (obj = {}) => {
  if (type obj !== 'object' || obj === null) {
    return obj
  }
  let result = obj instanceof Array ? [] : {}
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      result[key] = deepClone(obj[key])
    }
  }
  return result
}
```
存在问题-循环引用,造成递归进入死循环导致栈内存溢出了
```
const obj = { name: 'zxc', age: 18 }
obj.obj = obj
deepClone(obj)
```
我们可以额外开辟一个存储空间，来存储当前对象和拷贝对象的对应关系，当需要拷贝当前对象时，先去存储空间中找，有没有拷贝过这个对象，如果有的话直接返回，如果没有的话继续拷贝，这样就巧妙化解的循环引用的问题
```
const deepClone = (obj = {}, map = new Map()) => {
  if (typeof obj !== 'object' || obj == null ) {
    return obj
  }
  // 或者 Array.isArray(target) ? [] : {};
  let result = obj instanceof Array ? [] : {}
  if (map.get(obj)) {
    console.log('缓存中获取', obj);
    return map.get(obj);
  }
  // 反正是对象的引用，先将对象 set 进去，
  // 后面再实现对象属性赋值 result[key] = deepClone(obj[key], map)
  // 依然能得到 赋值后的对象
  map.set(obj, result);
  for (let key in obj) {
    if(obj.hasOwnProperty(key)) {
      result[key] = deepClone(obj[key], map)
    }
  }
  return result
}
```

#### 单例模式
#### 发布订阅
* 主要解决是“类或对象之间的交互”问题
* 在对象之间定义一个一对多的依赖，当一个对象状态改变的时候，所有依赖的对象都会自动收到通知
* 将不同的行为代码解耦，具体到观察者模式，它将观察者和被观察者代码解耦
* 借助设计模式，我们利用更好的代码结构，将一大坨代码拆分成职责更单一的小类，让其满足开闭原则、高内聚低耦合等特性，以此来控制和应对代码的复杂性，提高代码的可扩展性
```
class Subject {
  constructor(state) {
    this.state = state
    this.observers = []
  }
  register(observers) {
    this.observers.push(observers)
  }
  changeState(state) {
    this,state = state
    //重点 通知到所有的观察者
    this.observers.forEach(o => o.update(state))
  }
}

class Observer {
  constructor(name) {
    this.name = name
  }
  update(newState) {
    console.log(newState);
  }
}

const subject = new Subject()
const a = new Observer('123')
const b = new Observer('456')

subject.register(a)
subject.register(b)
subject.changeState('zxc')
```
#### 单例模式
* 一个类只允许创建一个对象（或者实例），那这个类就是一个单例类
* 单例模式用来创建全局唯一的对象
* 举个栗子: 1.dialog 弹框 2.日志log的记录
```
// 比如这样
var People = function(name) {
  this.name = name
}

var Singleton = function(Obj) {
  var instance
  Singleton = function() {
    if (instance) return instance
    return instance = new Obj(arguments)
  }
  return Singleton
}

var peopleSingleton = Singleton(People)

var a = new peopleSingleton('a')
var b = new peopleSingleton('b')
console.log(a===b)
```
