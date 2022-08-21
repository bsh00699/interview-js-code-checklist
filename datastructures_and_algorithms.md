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
* 合并两个有序链表-[leetcode-21](https://leetcode.cn/problems/merge-two-sorted-lists/)
```
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
/**
 * @param {ListNode} list1
 * @param {ListNode} list2
 * @return {ListNode}
 */
var mergeTwoLists = function(list1, list2) {
  const head = new ListNode(-1)
  let curr = head
  while (list1 && list2) {
    if (list1.val < list2.val) {
      curr.next = list1
      list1 = list1.next
    } else {
      curr.next = list2
      list2 = list2.next
    }
    curr = curr.next
  }
  curr.next = list1 ? list1 : list2
  return head.next
};
```
* 合并K个升序链表-[leetcode-23](https://leetcode.cn/problems/merge-k-sorted-lists/)   
1.在合并两个有序链表基础之上，使用循环合并
```
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
/**
 * @param {ListNode[]} lists
 * @return {ListNode}
 */
var mergeKLists = function(lists) {
  let ans = null
  for (let i = 0; i < lists.length; i++) {
    const arr = lists[i]
    ans = mergeTwoLists(ans, arr)
  }
  return ans
};

var mergeTwoLists = function(list1, list2) {
  const head = new ListNode(-1)
  let curr = head
  while (list1 && list2) {
    if (list1.val < list2.val) {
      curr.next = list1
      list1 = list1.next
    } else {
      curr.next = list2
      list2 = list2.next
    }
    curr = curr.next
  }
  curr.next = list1 ? list1 : list2
  return head.next
};
```
2.分治,转化为合并2个有序链表
```
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
const merge = (list, start, end) => {
  // 临界点
  if (start === end) return list[start]
  const mid = Math.floor((start + end) / 2)
  const l1 = merge(list, start, mid)
  const l2 = merge(list, mid + 1, end)
  return mergeTwoLists(l1, l2)
}
/**
 * @param {ListNode[]} lists
 * @return {ListNode}
 */
var mergeKLists = function(lists) {
  if (!lists.length) return null
  return merge(lists, 0, lists.length - 1)
};

var mergeTwoLists = function(list1, list2) {
  const head = new ListNode(-1)
  let curr = head
  while (list1 && list2) {
    if (list1.val < list2.val) {
      curr.next = list1
      list1 = list1.next
    } else {
      curr.next = list2
      list2 = list2.next
    }
    curr = curr.next
  }
  curr.next = list1 ? list1 : list2
  return head.next
};
```
#### 栈与队列
* 有效括号-[leetcode-20](https://leetcode.cn/problems/valid-parentheses/)
```
/**
 * @param {string} s
 * @return {boolean}
 */
var isValid = function(s) {
  const stack = []
  for (let i = 0; i < s.length; i++) {
    const ch = s[i]
    if (ch === '(' || ch === '[' || ch === '{') {
      stack.push(ch)
    } else {
      if (!stack.length) return false
      const curr = stack.pop()
      if (ch === ')' && curr !== '(') return false
      if (ch === ']' && curr !== '[') return false
      if (ch === '}' && curr !== '{') return false
    }
  }
  return stack.length === 0
};
```
* 最小栈-[leetcode-155](https://leetcode.cn/problems/min-stack/)
```
var MinStack = function() {
  this.stack = []
  this.preMin = []
};

/** 
 * @param {number} val
 * @return {void}
 */
MinStack.prototype.push = function(val) {
  this.stack.push(val)
  if (this.preMin.length === 0) {
    this.preMin.push(val)
  } else {
    this.preMin.push(
      Math.min(val, this.preMin[this.preMin.length - 1])
    )
  }
};

/**
 * @return {void}
 */
MinStack.prototype.pop = function() {
  this.stack.pop()
  this.preMin.pop()
};

/**
 * @return {number}
 */
MinStack.prototype.top = function() {
  return this.stack[this.stack.length - 1]
};

/**
 * @return {number}
 */
MinStack.prototype.getMin = function() {
   return this.preMin[this.preMin.length - 1]
};

/**
 * Your MinStack object will be instantiated and called as such:
 * var obj = new MinStack()
 * obj.push(val)
 * obj.pop()
 * var param_3 = obj.top()
 * var param_4 = obj.getMin()
 */
```
#### 单调栈
* 柱状图中最大矩形-[leetcode-84](https://leetcode.cn/problems/largest-rectangle-in-histogram/)
```
var largestRectangleArea = function(heights) {
  heights.push(0)
  let ans = 0
  const stack = []
  for (let i = 0; i < heights.length; i++) {
    let tempWidth = 0
    const height = heights[i]
    // 不满足单调递增
    while (stack.length && stack[stack.length - 1].height >= height) {
      tempWidth += stack[stack.length - 1].width
      ans = Math.max(ans, stack[stack.length - 1].height * tempWidth)
      stack.pop()
    }
    stack.push({
      height,
      width: tempWidth + 1
    })
  }
  return ans
};
```
* 接雨水-[leetcode-42](https://leetcode.cn/problems/trapping-rain-water/)
```
var trap = function(height) {
  let ans = 0
  const stack = []
  for (let i = 0; i < height.length; i++) {
    const h = height[i]
    let tempWidth = 0
    // 不满足单调递减
    while (stack.length && stack[stack.length - 1].height <= h) {
      const bottom = stack[stack.length - 1].height
      tempWidth += stack[stack.length - 1].width
      stack.pop()
      let minHeight = 0
      if (!stack.length) {
        minHeight = 0
      } else {
        minHeight = Math.min(stack[stack.length - 1].height, h) - bottom
      }
      ans += minHeight * tempWidth
    }
    stack.push({
      height: h,
      width: tempWidth + 1
    })
  }
  return ans
};
```
#### 单调队列
* 滑动窗口最大值-[leetcode-239](https://leetcode.cn/problems/sliding-window-maximum/)
```
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number[]}
 */
var maxSlidingWindow = function(nums, k) {
  const ans = []
  const que = [] // 下标（下标递增， 值递减）
  for (let i = 0; i < nums.length; i++) {
    // 判断出界 (窗口里只能放三个)
    while (que.length && que[0] + k <= i) {
      que.shift()
    }
    // 维护单调性
    while (que.length && nums[que[que.length - 1]] <= nums[i]) {
      que.pop()
    }
    que.push(i)
    // 更新答案
    if (i >= k - 1) {
      ans.push(nums[que[0]])
    }
  }
  return ans
};
```
#### 哈希表、集合
* 两数之和-[leetcode-1](https://leetcode.cn/problems/two-sum/)
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
* 模拟行走机器人-[leetcode-874](https://leetcode.cn/problems/walking-robot-simulation/)
```
/**
 * @param {number[]} commands
 * @param {number[][]} obstacles
 * @return {number}
 */
var robotSim = function(commands, obstacles) {
  // 1. 把障碍物坐标保存为无序集合
  const s = new Set()
  for (const obs of obstacles) {
    s.add(`${obs[0]}_${obs[1]}`)
  }
 
  // 2.定义初始坐标
  let x = 0
  let y = 0
  //dir:  北 0, 东 1, 南 2, 西 3
  let dir = 0

  // 3.方向数组
  const dx = [0, 1, 0, -1]
  const dy = [1, 0, -1, 0]

  let ans = 0
  for (let i = 0; i < commands.length; i++) {
    const cmd = commands[i]
    if (cmd === -1) {
      dir = (dir + 1) % 4
    } else if (cmd === -2) {
      dir = (dir + 3) % 4
    } else {
      for (let j = 0; j < cmd; j++) {
        let nx = x + dx[dir]
        let ny = y + dy[dir]
        if (s.has(`${nx}_${ny}`)) break
        x = nx
        y = ny
      }
    }
    ans = Math.max(ans, x * x + y * y)
  }
  return ans
};
```
* LRU缓存-[leetcode-146](https://leetcode.cn/problems/lru-cache/)
```
/**
 * @param {number} capacity
 */
var LRUCache = function(capacity) {
   this.cacheMap = new Map()
   this.size = capacity
};

/** 
 * @param {number} key
 * @return {number}
 */
LRUCache.prototype.get = function(key) {
  if (!this.cacheMap.has(key)) return -1
  const prev = this.cacheMap.get(key)
  // 从原来位置删除
  this.cacheMap.delete(key)
  // 插入到尾部
  this.cacheMap.set(key, prev)
  return prev
};

/** 
 * @param {number} key 
 * @param {number} value
 * @return {void}
 */
LRUCache.prototype.put = function(key, value) {
  // 如果存在线删除
  if (this.cacheMap.has(key)) {
    this.cacheMap.delete(key)
  }
  // 正常插入，判断size
  this.cacheMap.set(key, value)
  if (this.cacheMap.size > this.size) {
    // 删除头节点
    // this.cacheMap.keys() 返回有序键名的遍历器
    // this.cacheMap.keys().next().value 返回最先前插入的头节点的key
    this.cacheMap.delete(this.cacheMap.keys().next().value)
  }
};
```
* 字母异位分词-[leetcode-49](https://leetcode.cn/problems/group-anagrams/)
```
/**
 * @param {string[]} strs
 * @return {string[][]}
 */
var groupAnagrams = function(strs) {
  const ans = []
  const map = new Map()
  for (let i = 0; i < strs.length; i++) {
    const str = strs[i]
    const temp = str.split('').sort().join()
    if (!map.has(temp)) {
      map.set(temp, [])
    }
    if (map.has(temp)) {
      map.set(temp, [...map.get(temp), str])
    }
  }
  for (const[_, value] of map) {
    ans.push(value)
  }
  return ans
};
```
* 子域名访问次数-[leetcode-811](https://leetcode.cn/problems/subdomain-visit-count/)
```
/**
 * @param {string[]} cpdomains
 * @return {string[]}
 */
var subdomainVisits = function(cpdomains) {
  const map = new Map()
  const ans = []
  for(const main of cpdomains) {
    const [num, str] = main.split(' ')
    const mainArr = str.split('.')
    let currMain = ''
    for (let i = mainArr.length - 1; i >= 0; i--) {
      currMain = currMain ? `${mainArr[i]}.${currMain}` : mainArr[i]
      if (!map.has(currMain)) {
        map.set(currMain, parseInt(num))
      } else {
         map.set(currMain, parseInt(num) + map.get(currMain))
      }
    }
  }
  for (const item of map) {
    ans.push(`${item[1]} ${item[0]}`)
  }
  return ans
};
```
数组的度-[leetcode-697](https://leetcode.cn/problems/degree-of-an-array/)
```
/**
 * @param {number[]} nums
 * @return {number}
 */
var findShortestSubArray = function(nums) {
  const map = new Map() // 映射到每个数子 存放[度, 元素开始位置, 元素结束位置]
  for (let i = 0; i < nums.length; i++) {
    const num = nums[i]
    if (!map.has(num)) {
      map.set(num, [1, i, i])
    } else {
      const [cnt, start] = map.get(num)
      map.set(num, [cnt + 1, start, i])
    }
  }
  // console.log('map', map)
  let maxCnt = 0
  let minLen = 0
  for (const [_, [cnt, left, right]] of map) {
    if (cnt > maxCnt) {
      maxCnt = cnt
      minLen = right - left + 1
    } else if (cnt === maxCnt) {
      // 最短子数组
      minLen = Math.min(minLen, right - left + 1)
    }
  }
  return minLen
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
##### 树
* 翻转二叉树-[leetcode-226](https://leetcode.cn/problems/invert-binary-tree/)
```
/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @return {TreeNode}
 */
var invertTree = function(root) {
  if (root === null) return null
  const temp = root.left
  root.left = root.right
  root.right = temp
  invertTree(root.left)
  invertTree(root.right)
  return root
};
```
* 验证二叉搜索树-[leetcode-98](https://leetcode.cn/problems/validate-binary-search-tree/)
```
/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @return {boolean}
 */
var isValidBST = function(root) {
  return check(root, -Infinity, Infinity)
};

const check = (root, rangeLeft, rangeRight) => {
  if (root === null) return true
  // 本来 root.val > rangeLeft && root.val < rangeRight
  if (root.val <= rangeLeft || root.val >= rangeRight) return false
  // 检查左右节点
  return check(root.left, rangeLeft, root.val) && check(root.right, root.val, rangeRight)
}
```
##### 排列
##### 分治

#### 树的遍历