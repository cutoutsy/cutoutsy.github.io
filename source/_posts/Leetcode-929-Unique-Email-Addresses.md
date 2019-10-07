---
layout: post
title: 'Leetcode 929: Unique Email Addresses'
categories: 算法
date: 2018-11-21 09:59:55
tags:
    - Leetcode
    - 929
    - Python3
---
**难度：Easy**
### 题目描述
每封电子邮件都由一个本地名称和一个域名组成，以@符号分隔。

例如，在alice@leetcode.com中， alice是本地名称，而leetcode.com是域名。

除了小写字母，这些电子邮件还可能包含','或'+'。

如果在电子邮件地址的本地名称部分中的某些字符之间添加句点（'.'），则发往那里的邮件将会转发到本地名称中没有点的同一地址。例如，"alice.z@leetcode.com” 和 “alicez@leetcode.com”会转发到同一电子邮件地址。 （请注意，此规则不适用于域名。）
<!--more-->
如果在本地名称中添加加号（'+'），则会忽略第一个加号后面的所有内容。这允许过滤某些电子邮件，例如 m.y+name@email.com 将转发到my@email.com。（同样，此规则不适用于域名。）

可以同时使用这两个规则。

给定电子邮件列表emails，我们会向列表中的每个地址发送一封电子邮件。实际收到邮件的不同地址有多少？

### 示例
```shell
输入：["test.email+alex@leetcode.com","test.e.mail+bob.cathy@leetcode.com","testemail+david@lee.tcode.com"]
输出：2
解释：实际收到邮件的是 "testemail@leetcode.com" 和 "testemail@lee.tcode.com"。
```

### 提示
> * 1 <= emails[i].length <= 100
> * 1 <= emails.length <= 100
> * 每封emails[i]都包含有且仅有一个 '@' 字符。

### 解题思路
这个题目比较简单，按照题目中的意思来编程即可。首先每封eamil中仅包含一个'@'字符，那么可以以'@'切分为本地名称和域名，域名无需处理，本地名称首先去除'+'后面的，然后再将'.'去掉，放入set中去重，最后返回set的大小。

具体可参加下面的代码。

### 解题代码
**python3**
```python
class Solution:
    def numUniqueEmails(self, emails):
        """
        :type emails: List[str]
        :rtype: int
        """
        ans = set()
        for email in emails:
            local_name, domain_name = email.split('@')
            idx = local_name.find('+')
            if idx != -1:
                local_name = local_name[0:idx]
            local_name = local_name.replace('.', '')
            ans.add(local_name +'@' + domain_name)
        return len(ans)
```

### 运行时间截图

![runtime](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_leetcode929_runtime.png)
