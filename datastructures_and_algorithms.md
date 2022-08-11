### 数据结构与算法
#### 数组
* 合并两个有序数组-[leetcode-88](https://leetcode.cn/problems/merge-sorted-array/)
* 1.通过比较两数组
```
var merge = function(nums1, m, nums2, n) {
  let il = 0
  let ir = 0
  const ans = []
  while (il < m && ir < n) {
    if (nums1[il] <= nums2[ir]) {
      ans.push(nums1[il])
      il++
    } else {
      ans.push(nums2[ir])
      ir++
    }
  }
  //比较完后，把依然还有元素的数组中的值
  //依次放入 临时数组result
  while (il < m) {
    ans.push(nums1[il])
    il++
  }
  while (ir < n) {
    ans.push(nums2[ir])
    ir++
  }
  console.log('aa', ans)
  return ans
};
```
* 2.双指针, 从后向前遍历，防止元素被覆盖
```
var merge = function(nums1, m, nums2, n) {
  // 双指针 原地算法
  let i = m - 1
  let j = n - 1
  let cnt = m + n - 1
  while (j >= 0) {
    if (nums1[i] <= nums2[j]) {
      nums1[cnt--] = nums2[j--]
    } else {
      nums1[cnt--] = nums1[i--]
    }
  }
}
```
* 删除有序数组中的重复项-[leetcode-26](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)
* 过滤器 + cnt 计数
```
var removeDuplicates = function(nums) {
  let cnt = 0
  for (let i = 0; i < nums.length; i++) {
    if (i === 0 || nums[i] !== nums[i - 1]) {
      nums[cnt] = nums[i]
      cnt++
    }
  }
  return cnt
};
```
#### 链表
* 反转链表-[leetcode-206](https://leetcode.cn/problems/reverse-linked-list/)
```
var reverseList = function(head) {
  let curr = head
  let prev = null
  while (curr) {
    const tempNext = curr.next
    curr.next = prev
    prev = curr
    curr= tempNext
  }
  return prev
}
```
* k个一组翻转链表-[leetcode-25](https://leetcode.cn/problems/reverse-nodes-in-k-group/)
```
const getEnd = (head, k) => {
  while (head) {
    k--
    if (k === 0) return head
    head = head.next
  }
  return null
}
var reverseList = function(head, stop) {
  let prev = head
  let curr = head.next
  while (curr !== stop) {
    const tempNext = curr.next
    curr.next = prev
    prev = curr
    curr= tempNext
  }
}
var reverseKGroup = function(head, k) {
  const protect = new ListNode(0, head)
  let prev = protect

  while (head) {
    // 1.分组
    const end = getEnd(head, k)
    if (end === null) break
    // 2.组内反转
    const nextGroupHead = end.next
    reverseList(head, nextGroupHead)
    // 3.组与组之间的关系
    prev.next = end
    head.next = nextGroupHead

    prev = head
    head = nextGroupHead
  }
  return protect.next
};
```
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