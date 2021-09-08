#### LCP 30. 魔塔游戏
小扣当前位于魔塔游戏第一层，共有 N 个房间，编号为 0 ~ N-1。每个房间的补血道具/怪物对于血量影响记于数组 nums，其中正数表示道具补血数值，即血量增加对应数值；负数表示怪物造成伤害值，即血量减少对应数值；0 表示房间对血量无影响。

小扣初始血量为 1，且无上限。假定小扣原计划按房间编号升序访问所有房间补血/打怪，为保证血量始终为正值，小扣需对房间访问顺序进行调整，每次仅能将一个怪物房间（负数的房间）调整至访问顺序末尾。请返回小扣最少需要调整几次，才能顺利访问所有房间。若调整顺序也无法访问完全部房间，请返回 -1。

示例 1：
```
输入：nums = [100,100,100,-250,-60,-140,-50,-50,100,150]

输出：1

解释：初始血量为 1。至少需要将 nums[3] 调整至访问顺序末尾以满足要求。
```
示例 2：
```
输入：nums = [-200,-300,400,0]

输出：-1

解释：调整访问顺序也无法完成全部房间的访问。
```
提示：

- 1 <= nums.length <= 10^5
- -10^5 <= nums[i] <= 10^5

#### 解法
1. 首先把数组求和一遍，以排除注定无法成功的情况

2.1 从前往后遍历数组，每遇到一个负数，都把它放到堆中；遍历过程中维护前缀和sum，一旦sum为负，证明需要把一个负数挪到数组末尾了。

2.2 为了保证挪动数字的次数最少，每次都挪最小的负数。所以我们使用最小堆，以保证负数都是有序的。
```go
func magicTower(nums []int) int {
    var sum int 
    for _,n := range nums {
        sum += n
    }
    if sum < 0 {
        return -1
    }
    h := make(Heap, 0)
    sum = 0
    var res int 
    for _,n := range nums {
        if n < 0 {
            heap.Push(&h, n)
        }
        sum += n
        if sum < 0 {
            tmp := heap.Pop(&h).(int)
            sum += -tmp
            res++
        }
    }
    return res 
}

type Heap []int

func (h Heap) Len() int {
	return len(h)
}

func (h Heap) Less(i, j int) bool {
	return h[i] < h[j]
}

func (h Heap) Swap(i, j int) {
	h[i], h[j] = h[j], h[i]
}

func (h *Heap) Push(x interface{}) {
	*h = append(*h, x.(int))
}

func (h *Heap) Pop() interface{} {
	tmp := (*h)[len(*h)-1]
	*h = (*h)[:len(*h)-1]
	return tmp
}
```
