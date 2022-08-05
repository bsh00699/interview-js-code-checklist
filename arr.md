#### 随机打乱数组
一个数组 x = [1, 2, 3, 4, 5]，经过函数shuffle(x) 处理后，返回一个新的数组x, 要求数组x被随机打乱
思路：遍历数组，每次从数组中随机取一个值，然后删除，再次重复随机取和删除操作
```
const x = [1, 2, 3, 4, 5];
const shuffle = (x) => {
  const temp = []
  for (let i = 0; i < 5; i++) {
    const randomIndex = Math.floor(Math.random() * x.length)
    temp[i] = x.splice(randomIndex, 1)[0]
  }
  return temp
}

console.log(shuffle(x))
```