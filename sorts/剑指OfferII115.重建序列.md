### 剑指 Offer II 115. 重建序列
请判断原始的序列 org 是否可以从序列集 seqs 中唯一地 重建 。

序列 org 是 1 到 n 整数的排列，其中 1 ≤ n ≤ 104。重建 是指在序列集 seqs 中构建最短的公共超序列，即  seqs 中的任意序列都是该最短序列的子序列。

 

示例 1：
```
输入: org = [1,2,3], seqs = [[1,2],[1,3]]
输出: false
解释：[1,2,3] 不是可以被重建的唯一的序列，因为 [1,3,2] 也是一个合法的序列。
```
示例 2：
```
输入: org = [1,2,3], seqs = [[1,2]]
输出: false
解释：可以重建的序列只有 [1,2]。
```
示例 3：
```
输入: org = [1,2,3], seqs = [[1,2],[1,3],[2,3]]
输出: true
解释：序列 [1,2], [1,3] 和 [2,3] 可以被唯一地重建为原始的序列 [1,2,3]。
```
示例 4：
```
输入: org = [4,1,5,2,6,3], seqs = [[5,2,6,3],[4,1,5,2]]
输出: true
```

提示：

- 1 <= n <= 10^4
- org 是数字 1 到 n 的一个排列
- 1 <= segs[i].length <= 10^5
- seqs[i][j] 是 32 位有符号整数

### 解法
根据题意，我们判断org是否是唯一的拓扑排序结果。

则按照拓扑排序的方案，我们先建依赖图和度数表。

之后排序的时候需要遵循的规则： 
1. 每一次bfs，都只能释放出一个度数为0的新数字
2. 每次释放出来的度数为0的新数字，都要和org中对应下标的数字相同
3. org数组的长度，恰好能被我们bfs完全覆盖到

```go
func sequenceReconstruction(org []int, seqs [][]int) bool {
    graph := make(map[int][]int)
    inDegree := make(map[int]int)
    for _, s := range seqs {
        for j := range s {
            if _, ok := inDegree[s[j]]; !ok {
                inDegree[s[j]] = 0
            }
            if j < len(s)-1 {
                graph[s[j]] = append(graph[s[j]], s[j+1])
                inDegree[s[j+1]]++
            }
        }
    }
    queue := []int{}
    for c, degree := range inDegree {
        if degree == 0 {
            queue = append(queue, c)
            delete(inDegree, c)
        }
    }
    var idx int
    for len(queue) > 0 {
        if len(queue) > 1 {
            return false
        }
        if idx >= len(org) || org[idx] != queue[0] {
            return false
        }
        idx++
        for _, c := range graph[queue[0]] {
            inDegree[c]--
            if inDegree[c] == 0 {
                
                delete(inDegree, c)
                queue = append(queue, c)
            }
        }
        queue = queue[1:]
    }
    return len(inDegree) == 0 && idx == len(org)
}
```