---
layout: post
title: "Python知识积累——字典与元祖"
date: 2017-11-05 周日
author: "Johan Niu"
header-img : "img/post-bg-rwd.jpg"
tags:
    - Python
    - 学习
 
---


## 需求一
已知两个list a,b，求list a中的元素不在b中出现的子list **[5,7]**
 
	a = [1,3,5,7]
	b = [1,3,4,6,8]

### 解法一（错误）：
刚开始 最笨的方便 直接 for + if 判断：

	>>> for i in a :
	...     if i in b :
	...         a.remove(i)
	... 
	>>> a
	[3, 5, 7]

心想不对啊，不应该是`[5,7]`嘛，逻辑也没错啊。找了半天没找出问题。于是用集合set方法解决先：
### 解法二：

	>>> list(set(a)-set(b))
	[5, 7]
	
但是第一组解法究竟错在哪里？于是一步一步print试试：

	>>> for i in a :
	...     print i,a
	...     if i in b :
	...         a.remove(i)
	... 
	1 [1, 3, 5, 7]
	5 [3, 5, 7]
	7 [3, 5, 7]

可以发现当remove(1)的之后，`a=[3,5,7]`,但是此时的循环直接已经指向第二个元素了，也就是a的第二个元素5，而不再是老的list的第二个元素。明白问题出在哪，就好解决了。

### 解法一 debug
先将list转成不可修改的tuple，

	>>> c = tuple(a)
	>>> for i in c :
	...     if i in b:
	...         a.remove(i)
	... 
	>>> a
	[5, 7]

终于看到想要的了。

	>>> for i in c :
	...     print i,c
	...     if i in b:
	...         a.remove(i)
	... 
	1 (1, 3, 5, 7)
	3 (1, 3, 5, 7)
	5 (1, 3, 5, 7)
	7 (1, 3, 5, 7)
	
可以看到tuple c 并有改变。

## 需求二
已知list a,b 求list a中不在list b出现的子list 并统计个数。预期答案：`list = [5,5,7]; ans = [{'5':2,'7':1}]`

	a = [1,3,5,5,7]
	b = [1,3,4,6,8]
	
### 解法一（错误）

	>>> from collections import Counter
	>>> Counter(list(set(a)-set(b)))
	Counter({5: 1, 7: 1})
	
不对啊，5的元素个数明明是2。这是没有充分理解set的含义啊。

### 解法二：

	>>> c = tuple(a)
	>>> for i in c :
	...     if i in b :
	...         a.remove(i)
	... 
	>>> a
	[5, 5, 7]
	>>> Counter(a)
	Counter({5: 2, 7: 1})
	
## 总结
当遍历list不想改变其原来值得时候，用tuple。元祖的性质就是不可修改，只可访问。所有这是关键，体会到了元祖的好处吧！
