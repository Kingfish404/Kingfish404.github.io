---
layout: post        # page:单页面,不在归档索引;post:有前后页面，索引;encrypted:放protected文件夹中的加密文档
title: ACM-N皇后问题及hash查找思想     # 标题
date: 2021-02-18    # 日期
categories:         # 集合,暂未使用
author:  jinyu      # 作者
tags: [ACM,笔记]              # 标签，博客分类页面显示
comments: true          # bool,是否可以评论
toc:                # bool,是否有目录
pinned:             # bool,是否置顶
desc:  花式做了下N皇后问题，感觉比别的题有意思，写一下记录
hide: 
---

> **题目描述**
>
> 在N*N的方格棋盘放置了N个皇后，使得它们不相互攻击（即任意2个皇后不允许处在同一排，同一列，也不允许处在与棋盘边框成45角的斜线上。
>
> 你的任务是，对于给定的N，求出有多少种合法的放置方法。

<!-- more -->

题目地址：[http://acm.hdu.edu.cn/showproblem.php?pid=2553](http://acm.hdu.edu.cn/showproblem.php?pid=2553)，具体的IO格式可以在这里看。

典型的DFS题目，比较难得地方就是设计数据结构模拟棋盘，记录不可以走的位置，然后就是要注意通过减枝提高效率。

这里一般不容易想到的是斜对角如何标记不可以访问。

如果直接通过二维数组来进行标记，非常的低效，所以用一种数据结构来模拟标记斜线很重要。这里记录的方式如下：

```c++
    0 1 2 3
0 | 1 0 2 0 			// 用一维数组来标记斜线
1 | 0 1 0 2 			// 可以发现，这里面都为1都一个斜线，任意一个点位置[i,j],i-j为0
2 | 3 0 1 0 			// 同理，对于3的斜线，坐标[i,j],有i-j为2,同样也适用于2的斜线
3 | 0 3 0 1 			// 所以，下方代码里吗，我用mark1来标记正斜对角线
```

对于反斜对角线，也和正斜对角线有相同的性质，不过这里是换成`i+j`来计算，这样就可以用两个数组来标记整个棋盘的斜对角是否访问，同理，列也可以一样。

上方数据结构的设计思想应该是属于`hash`查找的思想，速度非常的快，感觉很多的类似的查找都可以通过建立`hash`联系的方式来实现`O(1)`的复杂度。比如统计最长不重复字串时，就可以通过一个26位长度数组标记字母是否访问过。

最后，AC代码如下，简单来说，就是提前DFS打了个表，然后根据输入输出对应结果。

```c++
#include <cstdio>
#include <cstring>

using namespace std;

class Solution
{
    // cols 标记已经访问过的列，mark1表示正斜线，mark2为负斜线
    int cols[40], mark1[40], mark2[40];

    void dfs(int i, int n, int &num)
    {
        for (int j = 0; j < n; j++)
        {
            if (mark1[i - j + n] || mark2[i + j] || cols[j])
            {
                continue;
            }
            if (i == n - 1)
            {
                num++;
                continue;
            }
            mark1[i - j + n] = 1, mark2[i + j] = 1, cols[j] = 1;
            dfs(i + 1, n, num);
            mark1[i - j + n] = 0, mark2[i + j] = 0, cols[j] = 0;
        }
    }

public:
    void solve()
    {
        int n, num;
        // 初始化描述数组
        memset(cols, 0, sizeof(cols));
        memset(mark1, 0, sizeof(mark1));
        memset(mark2, 0, sizeof(mark2));
        int res[10];
      	// 提前打表算出各个值
        for (int i = 0; i < 10; i++)
        {
            num = 0;
            dfs(0, i + 1, num);
            res[i] = num;
        }

        while (~scanf("%d", &n) && n)
        {
            printf("%d\n", res[n - 1]);
        }
    }
};

int main()
{
    Solution s;
    s.solve();
    return 0;
}
```

