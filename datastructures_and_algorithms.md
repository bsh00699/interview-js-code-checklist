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