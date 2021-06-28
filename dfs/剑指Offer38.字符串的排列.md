#### 剑指 Offer 38. 字符串的排列
输入一个字符串，打印出该字符串中字符的所有排列。

 

你可以以任意顺序返回这个字符串数组，但里面不能有重复元素。

 

示例:
```
输入：s = "abc"
输出：["abc","acb","bac","bca","cab","cba"]
```

限制：

- 1 <= s 的长度 <= 8

#### 解法
直接递归求解，需要考虑重复字符去重
```go
func permutation(s string) []string {
    if len(s) == 1 {
        return []string{s}
    }
    used := make(map[byte]struct{})
    var res []string
    for i := range s {
        if _, has := used[s[i]]; has {
            continue
        }
        used[s[i]] = struct{}{}
        tmp := permutation(s[:i]+s[i+1:])
        for _, ts := range tmp {
            res = append(res, string(s[i])+ts)
        }
    }
    return res
}
```