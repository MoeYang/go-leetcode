#### LCS 03. 主题空间

「以扣会友」线下活动所在场地由若干主题空间与走廊组成，场地的地图记作由一维字符串型数组 grid，字符串中仅包含 "0"～"5" 这 6 个字符。地图上每一个字符代表面积为 1 的区域，其中 "0" 表示走廊，其他字符表示主题空间。相同且连续（连续指上、下、左、右四个方向连接）的字符组成同一个主题空间。

假如整个 grid 区域的外侧均为走廊。请问，不与走廊直接相邻的主题空间的最大面积是多少？如果不存在这样的空间请返回 0。

示例 1:

![](https://pic.leetcode-cn.com/1613708145-rscctN-image.png)
```
输入：grid = ["110","231","221"]

输出：1

解释：4 个主题空间中，只有 1 个不与走廊相邻，面积为 1。
```

示例 2:

![](https://pic.leetcode-cn.com/1613707985-KJyiXJ-image.png)
```
输入：grid = ["11111100000","21243101111","21224101221","11111101111"]

输出：3

解释：8 个主题空间中，有 5 个不与走廊相邻，面积分别为 3、1、1、1、2，最大面积为 3。
```

提示：

- 1 <= grid.length <= 500
- 1 <= grid[i].length <= 500
- grid[i][j] 仅可能是 "0"～"5"

#### 解法
首先将各个区域通过并查集合并。

再次循环数组：
- 将需要排除的点换成集合的父节点记录下来
- 通过统计方格个数记录每个集合的面积

最终遍历一次所有集合，删除要排除的集合，并求最大面积
```go
func largestArea(grid []string) int {
    u := make(Union)
    n := len(grid[0])
    for i := range grid {
        for j := range grid[i] {
            if grid[i][j] == '0' {
                continue
            }
            s := i*n+j
            if j > 0 && grid[i][j-1] == grid[i][j] {
                u.Merge(s, i*n+j-1)
            }
            if i > 0 && grid[i-1][j] == grid[i][j] {
                u.Merge((i-1)*n+j,s)
            }
        }
    }
    needDel := make(map[int]struct{})
    notDel :=  make(map[int]int)
    for i := range grid {
        for j := range grid[i] {
            if grid[i][j] == '0' {
                if i > 0 && grid[i-1][j] != '0' {
                    needDel[u.Search((i-1)*n+j)] = struct{}{}
                }   
                if i < len(grid)-1 && grid[i+1][j] != '0'{
                    needDel[u.Search((i+1)*n+j)] = struct{}{}
                }
                if j > 0 && grid[i][j-1] != '0' {
                    needDel[u.Search(i*n+j-1)] = struct{}{}
                }
                if j < len(grid[0])-1 && grid[i][j+1] != '0' {
                    needDel[u.Search(i*n+j+1)] = struct{}{}
                }
                continue
            }
            p := u.Search(i*n+j)
            if i == 0 || j == 0 || i == len(grid)-1 || j == len(grid[0])-1 {
                needDel[p] = struct{}{}
            } else {
                notDel[p]++
            }
        }
    }
    for k := range needDel {
        delete(notDel, k)
    }
    var max = 0
    for _, n := range notDel {
        if max < n {
            max = n
        }
    }
    return max
}

type Union map[int]int

func (u Union) Search(s int) int {
	if _,ok := u[s]; !ok {
		return s
	}
	p := u[s]
	if u[p] != p {
		u[s] = u.Search(p)
	}
	return u[s]
}

func (u Union) Merge(a,b int) {
	pA,pB := u.Search(a), u.Search(b)
	if pA == pB {
		return
	}
	u[pA] = pB
}
```