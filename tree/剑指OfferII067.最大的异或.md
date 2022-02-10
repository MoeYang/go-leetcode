### 剑指 Offer II 067. 最大的异或
给定一个整数数组 nums ，返回 nums[i] XOR nums[j] 的最大运算结果，其中 0 ≤ i ≤ j < n 。

 

示例 1：
```
输入：nums = [3,10,5,25,2,8]
输出：28
解释：最大运算结果是 5 XOR 25 = 28.
```
示例 2：
```
输入：nums = [0]
输出：0
```
示例 3：
```
输入：nums = [2,4]
输出：6
```
示例 4：
```
输入：nums = [8,10,2]
输出：10
```
示例 5：
```
输入：nums = [14,70,53,83,49,91,36,80,92,51,66,70]
输出：127
```

提示：

- 1 <= nums.length <= 2 * 10^4
- 0 <= nums[i] <= 2^31 - 1

### 解法
把每个数字看做由0或1组成的32位字符串，我们可以把所有的数字存储在一棵二叉树上。

遍历每一个数字s1，在树上寻找能使s1^s2的结果最大的数字，

方式是，每一层遍历都选择树上存在的、与s1[i]相反的子树，使得高位异或的结果为1.

```go
func findMaximumXOR(nums []int) int {
    tree := &node{}
    numArr := make([]string, len(nums))
    for i, n := range nums {
		// 数字转为2进制字符串
        b := make([]byte, 32)
        for idx := 31; idx >= 0; idx-- {
            if n % 2 == 1 {
                b[idx] = '1'
            } else {
                b[idx] = '0'
            }
            n = n >> 1
        }
        numArr[i] = string(b)
		// 字符串添加到树上
        tree.Add(numArr[i], i)
    }
    max := 0
    for i := range numArr {
		// 在树上找每一位都尽量与i相反的数字，求异或结果
        idx := tree.Search(numArr[i])
        if tmp := nums[i]^nums[idx]; tmp > max {
            max = tmp
        }
    }
    return max 
}

type node struct {
    left *node
    right *node
    idx int
}

func (r *node) Add(s string, idx int) {
    pre := r
    for i := range s {
        pre = r
        if s[i] == '0' {
            if pre.left == nil {
                pre.left = new(node)
            }
            r = pre.left
        } else {
            if pre.right == nil {
                pre.right = new(node)
            }
            r = pre.right
        }
    }
    r.idx = idx
}

func (r *node) Search(s string) int {
    for i := range s {
        n := r.left
		// 从高位到低位，尽量选择存在的、与s[i]相反的节点向下遍历
        if (s[i] == '0' && r.right != nil) || n == nil {
            n = r.right
        } 
        r = n
    }
    return r.idx
}
```
