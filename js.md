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
#### 冒泡排序（Bubble Sort）
```
var arr = [3,4,1,2];

for(let j = 0; j < arr.length; j++) {
  for(let i = 0; i < arr.length; i++) {
    if(arr[i] > arr[i+1]) {
      let temp = arr[i]
      arr[i] = arr[i+1];
      arr[i+1] = temp
    }
  }
}
```
这里 i < arr.length - 1 ，要思考思考合适吗？
虽然上面的代码已经实现冒泡排序了，但就像注释中提到的，内层 for 循环的次数写成，i < arr.length - 1 ，是不是合适呢？ 我们想一下，当第一次，找到最大数，放到最后，那么下一次，遍历的时候，是不是就不能把最后一个数算上了呢？因为他就是最大的了，不会出现，前一个数比后一个数大，要交换位置的情况，所以内层 for 循环的次数，改成 i < arr.length - 1 -j ，才合适，看下面的代码
```
xvar arr = [3, 4, 1, 2];
function bubbleSort (arr) {
  if (arr.length <= 1) return
  for (var j = 0; j < arr.length - 1; j++) {
    // 这里要根据外层for循环的 j，逐渐减少内层 for循环的次数
    for (var i = 0; i < arr.length - 1 - j; i++) {
      if (arr[i] > arr[i + 1]) {
        var temp = arr[i];
        arr[i] = arr[i + 1];
        arr[i + 1] = temp;
      }
    }
  }
  return arr;
}
bubbleSort(arr);
```
我们想下这个情况，当原数组是，
arr = [1,2,4,3];
在经过第一轮冒泡排序之后，数组就变成了
arr = [1,2,3,4];
此时，数组已经排序完成了，但是按上面的代码来看，数组还会继续排序，所以我们加一个标志位，如果某次循环完后，没有任何两数进行交换，就将标志位 设置为 true，表示排序完成，这样我们就可以减少不必要的排序，提高性能。
```
var arr = [3, 4, 1, 2];
function bubbleSort (arr) {
  if (arr.length <= 1) return
  for (var j = 0; j < arr.length - 1; j++) {
     var flag = false
    // 这里要根据外层for循环的 j，逐渐减少内层 for循环的次数
    for (var i = 0; i < arr.length - 1 - j; i++) {
      if (arr[i] > arr[i + 1]) { //交换
        var temp = arr[i];
        arr[i] = arr[i + 1];
        arr[i + 1] = temp;
        flag = true  // 表示有数据交换
      }
    }
    if(!flag) { // 如果没有数据交换，提前退出
      break
    }
  }
  return arr;
}
```
#### 插入排序
首先，我们将数组中的数据分为两个区间，已排序区间和未排序区间。
初始已排序区间只有一个元素，就是数组的第一个元素。
插入算法的核心思想是取未排序区间中的元素，在已排序区间中找到合适的插入位置将其插入，并保证已排序区间数据一直有序。
重复这个过程，直到未排序区间中元素为空，算法结束
```
const insert = (arr = []) => {
  const len = arr.length
  if (len <= 1) return;
  // 遍历右边未排序区间的元素,一定注意是从1开始
  for(let i = 1; i < len; i++) { 
    // 先拿出要插入的值
    let value = arr[i]
    // 在确定 左边 已经排序好的区间大小，就是 i 的前一位 即 i-1
    let j = i -1
    // 每次从右边未排序的数组中拿出一个元素，放到左边有序的数组中，那到底放哪里呢
    // 我们就要和左边有序的数组中的元素做一个大小比较，来找出要放的位置
    // 怎么比较呢?
    // 从 i 前面的位置即 j 也就是左边有序数组的最后一个元素 往前 依次比较
    // 并完成数据移动，如果 arr[j] > vaule , value 往前移， arr[j] 往后走
    // 此时要完成两个目标
    // ① 找出要插入元素的位置 ②并且给要插入元素腾出位置，即数据移动
    while (j >= 0 && arr[j] > value) { 
      arr[j + 1] = arr[j] // 只要条件满足arr[j]的值就往后挪一位 
      j--
    }
    //最后插入数据
    //我们要插入的位置是移过去元素arr[j]的位置
    //而while 循环完是 j-1 所以 要把 减去的 1 加上
    arr[j + 1] = value
  }
  return arr
} 
```
#### 归并排序
```
const arr = [5, 1, 6, 2, 3, 4]
const sortArr = (arr) => {
  //mergeSortRec:首先将大数组分解成小数组，即每个数组元素为1，这样小数组就是有序的
  return mergeSortRec(arr)
}

const mergeSortRec = (arr) => {
  //使用递归思想
  //1找到通项公式 2确认临界值
  let length = arr.length
  if (length === 1) { //临界值
    return arr
  }
  let mid = Math.floor(length / 2)
  let left = arr.slice(0, mid)
  let right = arr.slice(mid, length)
  //使用递推
  return merge(mergeSortRec(left), mergeSortRec(right))
}

/**
 * 
 * @param {*} left 有序数组
 * @param {*} right 有序数组
 * @returns 
 */
const merge = (left = [], right = []) => {
  const result = []
  let il = 0 // 表示左边数组第0位
  let ir = 0 // 表示右边数组的第0位
  while (il < left.length && ir < right.length) {
    //从左右两数组中取出元素，依次比较，谁小
    //小的放入 result[] 
    if (left[il] < right[ir]) {
      result.push(left[il++]);
    } else {
      result.push(right[ir++]);
    }
  }
  //比较完后，把依然还有元素的数组中的值
  //依次放入 临时数组result
  while (il < left.length) {
    result.push(left[il++]);
  }
  
  while (ir < right.length) {
    result.push(right[ir++]);
  }
  return result
}
```