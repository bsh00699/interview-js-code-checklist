#### 深拷贝
* 将一个对象从内存中完整的拷贝一份出来,从堆内存中开辟一个新的区域存放新对象,且修改新对象不会影响原对象
```
const deepClone = (obj = {}) => {
  if (typeof obj !== 'object' || obj == null ) {
    return obj
  }
  // 或者 Array.isArray(target) ? [] : {};
  let result = obj instanceof Array ? [] : {}
  for (let key in obj) {
    if(obj.hasOwnProperty(key)) {
      result[key] = deepClone(obj[key])
    }
  }
  return result
}
const res = deepClone(data)
console.log('res', res)
```
存在问题-循环引用
* 循环引用，造成递归进入死循环导致栈内存溢出了
```
const obj = { name: 'zxc', age: 18 }
obj.obj = obj
deepClone(obj)

// Error: Maximum call stack size exceeded
```
我们可以额外开辟一个存储空间，来存储当前对象和拷贝对象的对应关系，当需要拷贝当前对象时，先去存储空间中找，有没有拷贝过这个对象，如果有的话直接返回，如果没有的话继续拷贝，这样就巧妙化解的循环引用的问题
这个存储空间，需要可以存储key-value形式的数据，且key可以是一个引用类型，我们可以选择Map这种数据结构
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
  map.set(obj, result);
  for (let key in obj) {
    if(obj.hasOwnProperty(key)) {
      result[key] = deepClone(obj[key], map)
    }
  }
  return result
}
```
#### flatten()
请实现 flatten() 函数，入参为一个 javascript 对象（Object 或者 Array），返回值为扁平化后的结果。
```
// 入参
{
  a: 1,
  b: [1, 2, { c: {d: true} }, [3], {e: [6, 7, 8]}],
  f: {g: 2, h: 3},
  i: null, 
}
// 返回
{
  "a": 1,
  "b[0]": 1,
  "b[1]": 2,
  "b[2].c.d": true,
  "b[3][0]": 3,
  "b[4].e[0]": 6,
  "b[4].e[1]": 7,
  "b[4].e[2]": 8,
  "f.g": 2,
  "f.h": 3,
  "i": null
}
```
```
let result = {};
const flatten = (params, key) => {
  if (params instanceof Array) {
    params.forEach((param, index) => {
      if (param instanceof Object || param instanceof Array) {
        flatten(param, `${key}[${index}]`);
      } else {
        result[`${key}[${index}]`] = flatten(param, `${key}[${index}]`);
      }
    });
  } else if (params instanceof Object) {
    for (var itemKey in params) {
      const itemValue = params[itemKey];
      if (itemValue instanceof Array || itemValue instanceof Object) {
        if (key) {
          flatten(itemValue, `${key}.${itemKey}`)
        } else {
          flatten(itemValue, itemKey);
        }
      } else if (itemValue === null || itemValue === undefined) {
        result[itemKey] = flatten(itemValue, itemKey);
      } else {
        if (key) {
          result[`${key}.${itemKey}`] = flatten(itemValue, itemKey);
        } else {
          result[itemKey] = flatten(itemValue, itemKey);
        }
      }
    }
  } else {
    return params;
  }
}
```