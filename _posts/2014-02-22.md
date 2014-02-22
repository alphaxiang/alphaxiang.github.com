---
layout: post
title: perfect shuffle 算法的一个线性复杂度实现
tags:  perfect_shuffle
---

今天又发现一个关于完美洗牌的算法。这个比较简单一些，由 microsoft的Peiyush Jain提出。

原论文：      A Simple In-Place Algorithm for In-Shuffle. 
                 Peiyush Jain, Microsoft Corporation. 
                            July 2004 
问题描述： 
 \\( 1/x^{2} \\)
所谓完美洗牌算法即是把输入为: 
\\(a_{1}\\),\\(a_{2}\\)........\\(a_{n}\\),\\(b_{1}\\),\\(b_{2}\\).........\\(b_{n}\\)的序列变为 
