### 数据结构与算法
#### 数组
#### 链表
#### 栈与队列
#### 单调栈
#### 单调队列
#### 哈希表、集合
* 两数之和
```
var twoSum = function(nums, target) {
  const len = nums.length
  if (!len) return []
  const map = new Map()
  for (let i = 0; i < len; i++) {
    const x = target - nums[i]
    if (map.has(x)) {
      return [map.get(x), i]
    }
    map.set(nums[i], i)
  }
};
```

#### 双指针扫描
* 三数之和
```
var threeSum = function(nums) {
  const ans = []
  nums = nums.sort((a, b) => a - b)
  // x + y + z = 0 => x + y = -z
  for (let i = 0; i < nums.length; i++) {
    const num = nums[i]
    if (i > 0 && nums[i] === nums[i - 1]) continue
    // num + (i + 1) + x = 0
    // (i + 1) + x = -num
    const twoSumRes = twoSum(nums, i + 1, -num)
    for (const [a, b] of twoSumRes) {
      ans.push([num, a, b])
    }
  }
  return ans
};

var twoSum = function(nums, start, target) {
  const ans = []
  let j = nums.length - 1
  for (let i = start; i < nums.length; i++) {
    if (i > start && nums[i] === nums[i - 1]) continue
    while (i < j && nums[i] + nums[j] > target) j--
    if (i < j && nums[i] + nums[j] === target) {
      ans.push([nums[i], nums[j]])
    }
  }
  return ans
};
```
#### 递归
#### 树的遍历