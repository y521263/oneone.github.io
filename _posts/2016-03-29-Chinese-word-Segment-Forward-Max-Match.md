---
layout: post
title:  "中文分词－正向最大匹配"
date:   2016-03-29 10:43:16 +0800
categories: Algorithm
#header-img: "img/post-bg-js-module.jpg"
tags:
- Algorithm 
- 中文分词
---


### 中文分词－正向最大匹配

中文分词，一个很常见的问题。分词就是将连续的字序列按照一定的规范重新组合成词序列的过程。我们知道，在英文的行文中，单词之间是以空格作为自然分界符的，而中文只是字、句和段能通过明显的分界符来简单划界。

#### 常用分词方式
* 字符串匹配

这类分词方式有正向/反向最大匹配，等等。
实现方式简单，速度也较快；
不过缺点是比较依赖字典，对于字典里没有的词(也就是未登录词)匹配效果不好。

* 统计学习以及机器学习

这类分词基于人工标注的词性和统计特征，建立模型，然后训练。在分词阶段，根据模型计算得到的概率来切分文本。常见的序列标注模型有<code>HMM</code>和<code>CRF</code>。

这次先说下相对简单的**正向最大匹配**。

正向最大匹配：从左到右将待切分的文本与字典库中的词作匹配，匹配上了就切出一个词。

待切分的文本

> 小明毕业于中国科学院
>
> dict={"小明","中国","科学","毕业"}

首先取出5个词，**小明毕业于**。为啥是5个，这个可以自己设定的，比如8，10个。。最长匹配长度

那么接下来会去字典库中查找。

> 小明毕业于   n
> 
> 小明毕业 n
> 
> ....
> 
> 小明 y

1.<code>小明毕业于</code>在字典中没找到

2.那么右边减少一个字，变成<code>小明毕业</code>

3.再重复第1步。。。

直到<code>小明</code>匹配上，顺利切分出一个词。
然后会切分出<code>毕业</code>

那么在做<code>于中国科学</code>匹配的时候，发现5次匹配结束了，都没有一个能匹配上，那么此时需要往后挪一位，取出<code>中国科学院</code>。在做下一轮的切分。

整个过程简单粗暴。如果字典里没有这个词那么是无论如何都匹配不出来的。这里还有个问题就是。字典的存储问题，如果词库有几十w的词组，用<code>hashmap</code>?

考虑到快速查找，可以尝试使用<code>Trie</code>树。
具体实现：
还需要实现一个[Trie树](https://github.com/y521263/LeetCode/blob/master/LeetCode/WordSearch2/WordSearch2.cpp#L29)作存储和查找(可以参考我刷过的leetcode题目里的一个实现).
区别在于，英文的子节点只有26个情况，中文却有很多情况，存储子节点的地方可以改成 <code>hashmap</code>

``` c++
void WordSplit(string s ,int maxlen,vector<string>& result)
{
    int len = maxlen;
    string ret;
    for (int i=0; i<s.size();) {
        string tmp = s.substr(i,len);
        if (isWord(tmp))
        {    
            ret = tmp;
        }
        len--;
        if (len >0) continue;
        if (!ret.empty())
        {
            result.push_back(ret);
            i = i+ret.size();
            ret="";
        }
        else
        {
            i++;
        }
        len = maxlen;
    }
}

```

#### 参考

[正向最大匹配中文分词](http://blog.csdn.net/yangyan19870319/article/details/6399871)
