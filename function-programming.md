#### 柯里化
以下是一个加法函数，存在curry 这个函数使得以下结果都相同
```
var add = (a, b, c) => a + b + c
add(1, 2, 3) // 6

const curryAdd = curry(add)
// 以下输出结果都相同
curryAdd(1, 2, 3) // 6
curryAdd(1, 2)(3) // 6
curryAdd(1)(2)(3) // 6
curryAdd(1)(2, 3) // 6
```
核心: 期待传入的参数，满足预期参数
```
const curry = (...args) => {
  const fn = args.shift()
  const length = fn.length
  let paramsList = args || [] // 记录参数个数
  return function (..._args) {
    paramsList = [...paramsList, ..._args]
    if (paramsList.length < length) {
      return curry(fn, ...paramsList)
    } else if (paramsList.length === length) {
      return fn.apply(this, paramsList)
    }
  }
}
const addInter = curry(add)
const res = addInter(1)(3)(2)
console.log('res--', res);
```
#### 组合函数
函数组合的目的是将多个函数组合成一个函数，比如像下面这样
```
function compose(f, g) {
  return function (x) {
    return f(g(x))
  }
}

function add(x) {
  var num = 1;
  return num + x
}

function multiply(x) {
  var num = 2;
  return num * x
}

var res = compose(multiply, add)(1)
console.log(res); // 4
```
函数最终执行从右至左，实现一个compose函数
```
const compose = (...args) => {
  let len = args.length - 1
  return function (x) {
    const fn = args[len]
    let res = fn(x)
    while (len--) {
      res = args[len](res)
    }
    return res
  }
}
const func = compose(multiply, add)
console.log('res', func(2)); // 输出 9
```
我们发现  res = args[len](res)  都是拿上一次的结果来传给下一个 args[len--]，所以 while 去掉，使用reduce。由于，我们调用 args 数组里的值（即函数）是从右到左，所以使用 reduceRight
```
const compose = (...args) => {
  return function (x) {
    return args.reduceRight((prev, curr) => {
      const res = curr(prev)
      return res
    }, x)
  }
}
const func = compose(multiply, add)
console.log('res', func(3)); // 输出 12
```
