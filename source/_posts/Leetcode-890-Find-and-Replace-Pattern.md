---
layout: post
title: 'Leetcode 890: Find and Replace Pattern'
categories: 算法
date: 2018-09-10 10:15:17
tags:
    - Leetcode
    - 890
    - String
---
**难度：Medium**
### 题目描述
给定一个字符串列表words和匹配字符串pattern，想要知道words中的哪些字符串和pattern匹配。

字符串和模式串匹配表示存在一个字母序列p以至于在将x替换完p(x)后，我们得到pattern字符串。字母序列是一个字母到字母的双射：即每个字母映射到另一个字母，没有两个字母映射到同一个字母。

返回匹配的字符串列表，顺序不限。

<!--more-->

### 示例
**示例1**
```shell
输入: words = ["abc","deq","mee","aqq","dkd","ccc"], pattern = "abb"
输出: ["mee","aqq"]
解释: "mee" 匹配pattern通过序列 {a -> m, b -> e, ...}.
"ccc" 不匹配是因为{a -> c, b -> c, ...}不是一个序列, 因为a和b都映射到同一字符c.
```

### 解题思路
这个题目和昨天京东笔试题中的一道题一样，所以今天来做一做。

这个题目我们主要是用到map，来进行字符之间的映射，并要满足题目的要求即可。比如我用两个map，一个表示从字符串s1到字符串s2的映射，一个表示字符串s2到字符串s1的映射，那么当遍历s1时，如果两个map中存在，那么一定是要是一一对应的，具体可以参考下面的代码。

这道题并不需要我们去真正的替换字符串，昨天在做这个题时，去替换了字符串，不应该，思维局限了。

### 解题代码
**Java8代码**
```Java
class Solution {
    public List<String> findAndReplacePattern(String[] words, String pattern) {
        List<String> ans = new ArrayList<>();
        for (String word : words) {
            if (match(word, pattern)) ans.add(word);
        }
        return ans;
    }
    
    public boolean match(String str1, String str2) {
        if (str1.length() != str2.length()) return false;
        HashMap<Character, Character> s1s2 = new HashMap<>();
        HashMap<Character, Character> s2s1 = new HashMap<>();
        char[] str1Char = str1.toCharArray();
        char[] str2Char = str2.toCharArray();
        for (int i = 0; i < str1Char.length; i++) {
            if (s1s2.containsKey(str1Char[i]) || s2s1.containsKey(str2Char[i])) {
                if (!(s1s2.getOrDefault(str1Char[i], '1') == str2Char[i] && s2s1.getOrDefault(str2Char[i], '1') == str1Char[i])) {
                    return false;
                }
            } else {
                s1s2.put(str1Char[i], str2Char[i]);
                s2s1.put(str2Char[i], str1Char[i]);
            }
        }
        return true;
    }
}
```
**Python3代码**
```python
def findAndReplacePattern(self, words, pattern):
    """
    :type words: List[str]
    :type pattern: str
    :rtype: List[str]
    """
    res = []

    def match(word, pattern):
        if len(word) != len(pattern):
            return False
        dir = {}
        for w, p in zip(word, pattern):
            if w not in dir:
                if p in dir.values():
                    return False
                dir[w] = p
            else:
                if dir[w] != p:
                    return False
        return True
    for w in words:
        if match(w, pattern):
            res.append(w)
    return res
```