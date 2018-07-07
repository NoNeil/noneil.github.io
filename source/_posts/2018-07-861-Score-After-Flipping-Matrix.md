---
title: 861. Score After Flipping Matrix
description: 贪心法求解矩阵的行列翻转问题。
categories: LeetCode
tags:
  - LeetCode
  - 贪心
date: 2018-07-07 17:14:34
updated: 2018-07-07 17:14:34
---


[LeetCode链接：861. Score After Flipping Matrix](https://leetcode.com/problems/score-after-flipping-matrix)

# 题目
给定一个二维数组`A`，数组中的数值是`0`或`1`。

你可以对每一行或每一列进行翻转：该行（或该列）的所有`0`变`1`或所有`1`变成`0`。

矩阵的每一行表示一个二进制的数值，矩阵的得分为所有行的值的总和。

求经过若干次翻转之后，矩阵得分的最大值。

# 举例
> Input: `[[0,0,1,1],[1,0,1,0],[1,1,0,0]]`
> Output: 39
> Explanation:
> 翻转的最终结果为：`[[1,1,1,1],[1,0,0,1],[1,1,1,1]]`.
> `0b1111 + 0b1001 + 0b1111 = 15 + 9 + 15 = 39`

# 解答

## 暴力法
分别遍历每一行、每一列，尝试进行翻转，如果翻转后的`得分`比翻转前的高，就执行翻转。

直到`得分`不再增加为止。

C++代码如下：
```C++
int matrixScore(vector<vector<int>>& A) {
    int row = A.size();
    if (row == 0)   return 0;
    int col = A[0].size();
    
    int result = score(A, row, col);        
    while (true){
        // cout << result << endl;
        bool flag = false;
        for (int row_op = 0; row_op < row; row_op++){
            change(A, row, col, row_op, -1, result, flag);
        }
        for (int col_op = 0; col_op < col; col_op++){
            change(A, row, col, -1, col_op, result, flag);
        }
        if (!flag){
            break;
        }
    }
    return result;
}

int score(vector<vector<int>> A, int row, int col){
    int total = 0;
    for (int i = 0; i < row; i++){
        for (int j = 0; j < col; j++){
            total += A[i][j] * pow(2, col - 1 - j);
        }
    }
    return total;
}

void change(vector<vector<int>>& A, int row, int col, int row_op, int col_op, int& old_score, bool& flag){
    if (row_op >= 0){
        for (int j = 0; j < col; j++){
            A[row_op][j] = 1 - A[row_op][j];
        }
        int new_score = score(A, row, col);
        if (old_score >= new_score){
            for (int j = 0; j < col; j++){
                A[row_op][j] = 1 - A[row_op][j];
            }
        }else{
            flag = true;
            old_score = new_score;
        }
    }
    else{
        for (int i = 0; i < row; i++){
            A[i][col_op] = 1 - A[i][col_op];
        }
        int new_score = score(A, row, col);
        if (old_score >= new_score){
            for (int i = 0; i < row; i++){
                A[i][col_op] = 1 - A[i][col_op];
            }
        }else{
            flag = true;
            old_score = new_score;
        }
    }
}
```


## 贪心法
由于二进制数的特点：$2^n>​2^{n−1}+2^{n−2}​+...+2^{0}​$​​。
也就是：左边的一个`1`比右边的所有`1`都更重要。

贪心的步骤如下：
1. 第一步，先将第一列的所有`0`都变成`1`。
2. 然后，对于剩下的列，如果该列中`1`的个数小于一半，就对这一列进行翻转。

第`1`步详细解释：
对于`A[i][j]`，如果`A[i][0]=1`（第一列），则不变；如果`A[i][0]=0`，则进行翻转。
> `A[i][0]=1` 时：
> 如果：`A[i][j]=1`，则：`A[i][j]=1`
> 如果：`A[i][j]=0`，则：`A[i][j]=0`
> `A[i][0]=0` 时：
> 如果：`A[i][j]=1`，则：`A[i][j]=0`
> 如果：`A[i][j]=0`，则：`A[i][j]=1`

上述操作可以用一行代码表示：`A[i][j] = 1 - A[i][j] ^ A[i][0]`

第`2`步：
我们要统计剩下的列中`1`的个数，循环剩下的所有列，对于某一列：
```C++
for (int r = 0; r < row; r++){
    one += 1 + A[r][c] ^ A[r][0];  
}
```
那么翻转后`one = max(one, row - one)`. 其中`row`表示矩阵的列数。
这个`max`函数，使得统计`1`的个数与统计`0`的个数是一样的，所以在统计`1`的个数的时候，可以简化为:
```C++
for (int r = 0; r < row; r++){
    one += A[r][c] ^ A[r][0];  
}
```
上述分析对第一列也适用。

矩阵总分的计算：
```C++
score += one * pow(2, col - 1 - c);
// score += one *  1 << (col - 1 - c);
```

样例分析：
```C++
// 初始
0, 0, 1, 1
1, 0, 1, 0
0, 0, 1, 1
// 1.先将第一列为0的进行翻转
1, 1, 0, 0
1, 0, 1, 0
1, 1, 0, 0
// 2.对于第3、4列，1的个数比0的个数少，进行翻转
1, 1, 1, 1
1, 0, 0, 1
1, 1, 1, 1
// 结果15+9+15=39
```


```C++
int matrixScore(vector<vector<int>>& A) {
    int row = A.size();
    if (row == 0)   return 0;
    int col = A[0].size();

    int score = 0;
    for(int c = 0; c < col; c++){
        // 对于某一列，初始有多少个1
        int one = 0;
        for (int r = 0; r < row; r++){
            // 其实这里是在统计有多少个0，加上下面的max函数之后，就变成了统计有多少个1了
            one += A[r][c] ^ A[r][0];  
        }
        // 如果这一列中1的个数少于一半，那么只翻转这一列，翻转完之后，1的个数就多于一半了
        one = max(one, row - one);
        score += one * pow(2, col - 1 - c);
    }
    return score;
}
```

