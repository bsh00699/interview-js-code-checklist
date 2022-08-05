#### 实现圣诞树
输入一个height,比如5，得到下面的输出结果
```
    *
   ***
  *****
 *******
*********
```
代码如下
```
const generateTree = (height) => {
  if (!height) return ''
  let res = ''
  for (let i = 1; i <= height; i++) {
    const blank = height - i
    // 空格
    for (let j = 0; j < blank; j++) {
      res += ' '
    }
    // *
    const start = 2 * i - 1
    for (let m = 0; m < start; m++) {
      res += '*'
    }
    // 换行符 \n
    res += '\n'
  }
  return res
}
```
#### 大数相加
```
const add = (a, b) => {
  let i = a.length - 1
  let j = b.length - 1

  let carry = 0
  let ret = ''
  while (i >= 0 || j >= 0) {
    let x = 0
    let y = 0
    let sum
    if (i >= 0) {
      // 取a的末位 转整数
      x = a[i] - '0'
      i--
    }

    if (j >= 0) {
      // 取b的末位 转整数
      y = b[j] - '0'
      j--
    }
    sum = x + y + carry
    if (sum >= 10) {
      carry = 1
      sum -= 10
    } else {
      carry = 0
    }
    ret = sum + ret
  }
  if (carry) {
    ret = carry + ret
  }
  return ret
}

console.log('res', add('123', '321'));
```