
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
#### 工厂模式
核心就是解耦
* 代码中存在 if-else 分支判断，动态地根据不同的类型创建不同的对象。针对这种情况，我们就考虑使用工厂模式，将这一大坨 if-else 创建对象的代码抽离出来，放到工厂类中
* 单个对象本身的创建过程比较复杂，比如前面提到的要组合其他类对象，做各种初始化操作。在这种情况下，我们也可以考虑使用工厂模式，将对象的创建过程封装到工厂类中
判断要不要使用工厂模式的最本质的参考标准
* 封装变化：创建逻辑有可能变化，封装成工厂类之后，创建逻辑的变更对调用者透明
* 代码复用：创建代码抽离到独立的工厂类之后可以复用
* 隔离复杂性：封装复杂的创建逻辑，调用者无需了解如何创建对象
* 控制复杂度：将创建代码抽离出来，让原本的函数或类职责更单一，代码更简洁

#### 代理模式
##### 应用场景
业务系统的非功能性需求开发
* 比如：监控、统计、鉴权、限流、事务、幂等、日志。我们将这些附加功能与业务功能解耦，放到代理类中统一处理

代理模式在 RPC 的应用
* 实际上，RPC 框架也可以看作一种代理模式，GoF 的《设计模式》一书中把它称作远程代理。通过远程代理，将网络通信、数据编解码等细节隐藏起来。客户端在使用 RPC 服务的时候，就像使用本地函数一样，无需了解跟服务器交互的细节

代理模式在 缓存中的应用
#### 手写 Express
```
const http = require('http')
const slice = Array.prototype.slice

class LikeExpress {
    constructor() {
        this.router = {
            all: [], // app.use()
            get: [],
            post: []
        }
    }

    register(path) {
        const info = {}
        if (typeof path === 'string') {
            info.path = path
            // 从第二个参数开始取出，然后转换为数组
            info.stack = slice.call(arguments, 1)
        } else {
            info.path = '/'
            // 从第一个参数开始取出，然后转换为数组
            info.stack = slice.call(arguments, 0)
        }
        return info
    }

    use() {
        const info = this.register.apply(this, arguments)
        this.router.all.push(info)
    }

    get() {
        const info = this.register.apply(this, arguments)
        this.router.get.push(info)
    }

    post() {
        const info = this.register.apply(this, arguments)
        this.router.post.push(info)
    }

    match(url, method) {
        let stack = []
        if(url === 'favicon.ico') {
            return stack
        }
        // 获取 路由
        const curRoutes = []
        curRoutes = curRoutes.concat(this.router.all)
        curRoutes = curRoutes.concat(this.router[method]) // 根据 method 区别
        // 根据 url 来区别
        curRoutes.forEach(routerInfo => {
            // 比如访问 /api/get-cookie
            if(url.indexOf(routerInfo.path) === 0) {
                // /
                // /api
                // /api/get-cookie
                stack = stack.concat(routerInfo.stack)
            }
        })
        return stack
    }
    // next 核心机制
    handle(req, res, stack) {
        const next = () => {
            const middleware = stack.shift()
            if(middleware) {
                middleware(req, res, next)
            }
        }
        next()
    }

    callback() {
        return (req, res) => {
            res.json = (data) => {
                res.setHeader('Content-Type', 'application/json')
                res.end(
                    JSON.stringify(data)
                )
            }
        }
        const url = req.url
        const method = req.method.toLowerCase()
        const resultInfoList = this.match(url, method)
        // 处理 next 机制
        this.handle(req, res, resultInfoList)
    }

    listen(...args) {
        const server = http.createServer(this.callback())
        server.listen(...args)
    }
}

module.exports = () => {
    return new LikeExpress()
}
```
#### 手写 Koa
##### 与Express相比
*  express框架中间件实质还是异步回调，koa2框架中间件原理原生支持async / await
*  express框架中包括路由处理模块，而koa2框架则就是一个精简的web服务
*  express框架本身是符合洋葱圈模型的，但是中间件中如果涉及到异步，则打破洋葱圈的模型，这时就需要将中间件函数全部替换成 async/await 
*  koa 框架的可扩展性比较好
```
const http = require('http')

// 组合中间件
function compose(middlewareList) {
    return function (ctx) {
        function dispatch(i) {
            const fn = middlewareList[i]
            try {
                // return Promise 主要是处理中间件既是async函数 又是 普通函数都可以执行
                // 而中间件中的 next 函数就是 dispatch.bind(null, i + 1)
                return Promise.resolve(
                    fn(ctx, dispatch.bind(null, i + 1)) 
                )
            } catch (err) {
                return Promise.reject(err)
            }
        }
        return dispatch(0)
    }
}

class LikeKoa2 {
    constructor() {
        this.middlewareList = []
    }

    use(fn) {
        this.middlewareList.push(fn)
        return this
    }

    createContext(req, res) {
        const ctx = {
            req,
            res
        }
        // 将req 上的属性赋值给ctx
        ctx.query = req.query // 等等 
        return ctx
    }

    // handleRequest(ctx, fn) {
    //     return fn(ctx)
    // }

    callback() {
        const fn = compose(this.middlewareList)

        return (req, res) => {
            const ctx = this.createContext(req, res)
            return fn(ctx)
            // return this.handleRequest(ctx, fn)
        }
    }

    listen(...args) {
        const server = http.createServer(this.callback())
        server.listen(...args)
    }
}

module.exports = LikeKoa2
```
