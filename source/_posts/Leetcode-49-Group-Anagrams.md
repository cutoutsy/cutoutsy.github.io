---
layout: post
title: 'Leetcode 49: Group Anagrams'
categories: 算法
date: 2018-08-02 14:41:07
tags:
    - Leetcode
    - 算法
    - 哈希表
    - 字符串
---
### 题目描述
给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。

<!-- more -->

### 示例
```shell
输入: ["eat", "tea", "tan", "ate", "nat", "bat"],
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```

### 说明
* 所有输入均为小写字母。
* 不考虑答案输出的顺序。

### 解题思路
这道题在昨天在牛客网上看面经看到的，今天来做做。

这是一道关于字符串的题目，可以采用hash的方式，把字符串按照字母顺序与相应的个数组合在一起生成key，然后将key相同的放在一起。题目还是比较简单。

如果只要求字符串含有的字母相同的话，可以不计算字母个数。

### 解题代码
```Java
class Solution {
    
    public List<List<String>> groupAnagrams(String[] strs) {
        HashMap<String, List<String>> hash = new HashMap<>();
        for (String str : strs) {
            String key = getKey(str);
            if (hash.containsKey(key)) {
                hash.get(key).add(str);
            } else {
                List<String> tmp = new LinkedList<String>();
                tmp.add(str);
                hash.put(key, tmp);
            }
        }
        List<List<String>> ans = new LinkedList<>();
        for (String key : hash.keySet()) {
            ans.add(hash.get(key));
        }
        return ans;
    }
    
    public String getKey(String str) {
        StringBuffer sb = new StringBuffer();
        int[] alph = new int[26];
        char[] charArray = str.toCharArray();
        for (char c : charArray) {
            alph[c-'a'] += 1;
        }
        for (int i = 0; i < 26; i++) {
            if (alph[i] > 0) {
                sb.append((char)(i+'a'));
                sb.append(alph[i]);
            }
        }
        return sb.toString();
    }
}
```