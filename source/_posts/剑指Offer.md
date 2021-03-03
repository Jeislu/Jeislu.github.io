---
title: 剑指Offer
date: 2021-03-03 09:48:25
tags:
 - 算法
---

## 前言

​	想了想，还是打算来把剑指 Offer 的题目都做一遍，理论上每天做一道。

<!-- more -->

## 剑指 Offer

## [剑指 Offer 03.数组中重复的数字](https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)

找出数组中重复的数字。


在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

**示例 1：**

```
输入：
[2, 3, 1, 0, 2, 5, 3]
输出：2 或 3 
```

 

**限制：**

```
2 <= n <= 100000
```

### 思路

​	第一种解法，排序，然后判断前面是否出现相同的数据。

​	第二种解法，使用 Set ，由于 Set 的不允许存在重复的数据，如果尝试 add 一个重复的数据，那么会添加失败，并且返回一个 false

### 代码

​	第一种

```java
public int findRepeatNumber(int[] nums) {
        Arrays.sort(nums);
        for(int i = 1; i < nums.length; i++){
            if(nums[i] == nums[i-1]){
                return nums[i];
            }
        }

        return -1;
    }
```

​	第二种

```java
public int findRepeatNumber(int[] nums) {
    Set<Integer> data = new HashSet<>();
    for(int n : nums){
        if (!data.add(n)){
            return n;
        }
    }

    return -1;
}
```

## [剑指 Offer 04. 二维数组中的查找](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)

在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

**示例:**

现有矩阵 matrix 如下：

```
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```

给定 target = `5`，返回 `true`。

给定 target = `20`，返回 `false`。

 

**限制：**

```
0 <= n <= 1000
0 <= m <= 1000
```

### 思路

​	简单粗暴就直接搜！

​	另外一种做法就是，从左下角或者右上角开始搜。

​	以左下角为例，如果当前点的值比 target 大，则想上移动一行，如果小就想右移动一行，相等就返回。

### 代码

​	直接搜

```java
int[][] matrix;
public boolean findNumberIn2DArray(int[][] matrix, int target) {
    this.matrix = matrix;

    if(matrix.length == 0 || matrix[0].length == 0){
        return false;
    }

    int maxRow = findRow(target);
    for(int i = maxRow; i >= 0; i--){
        if(findCol(i,target)){
            return true;
        }
    }

    return false;
}

public int findRow(int target){
    for(int i = 0; i < matrix.length; i++){
        if(target < matrix[i][0]){
            return i - 1;
        }
    }

    return matrix.length-1;
}

public boolean findCol(int row, int target){
    for(int i = 0; i < matrix[row].length; i++){
        if(matrix[row][i] == target){
            return true;
        }

        if(target < matrix[row][i]){
            return false;
        }
    }

    return false;
}
```

​	有技巧的搜

```java
public static boolean findNumberIn2DArray(int[][] matrix, int target) {
    if(matrix.length == 0 || matrix[0].length == 0){
        return false;
    }
    int row = matrix.length;
    int col = matrix[0].length;
    int x = 0;
    int y = row-1;

    while(x < col && y >= 0){
        if(matrix[y][x] == target){
            return true;
        }else if(matrix[y][x] > target){
            y--;
        }else{
            x++;
        }
    }

    return false;
}
```

