# Algorithm

## 查找

### 基本查找（顺序查找）

核心：从0索引开始按顺序往后找，直到查找成功,否则查找失败

### 二分查找

核心：数组必须是有序的，每次排除一半的查找范围

注意：（min + max）/2 时，min + max可能会溢出，需解决溢出问题

### 插值查找

核心：数组必须是有序的。

​			二分查找在寻找中值时，可用 mid + （num - arr[min]) / (arr[max] - arr[min]) * (max - min) 使mid 的值更加靠近所查值

### 分块查找

核心：数组是大致有序的（块间有序，块内无序）。先确定在哪个块内，然后在块内寻找。

分块数由数组长度开根号得出。可建立块对象来更好的操作数据

## 排序

### 冒泡排序

核心：每轮排序都将最大值放在本轮的最后，排序完后，已经排好序的数不在参与排序，共排序：数组长度-1 次

注意：不要数组越界

### 选择排序

核心：将选中的数按索引依次与后面的数比较，一轮结束后最小的数位于0索引处。以此类推。

### 插入排序

核心：将数组分为有序和无序。从无序的第一个索引开始，将其和有序中的数从后往前比较，若无序中的数小于有序中的数，交换。

首要先确定无序是从哪个索引开始的

### 快速排序

核心：将某个数(通常为数组中第一位数)提出来作为基准数，取两个指针start和end，end往前找小于基准数的数，停下，start往后找大于基准数的数，停下，交换两数位置，重复直到start==end，此时基准数左边的数都小于它，右边的数都大于它，交换基准数和此时的start（或end）。然后左边和右边的数分别进行递归，start > end条件为出口结束递归

注意：end先找，start后找，否则将是比基准数大的数在第一位，不满足条件。

## 递归算法

核心：在方法中调用本身。

首要先找出递归算法的出口，若无出口，则栈溢出

注意：每次调用本身都应该与出口更接近

## 问题

### 数据防溢出

int类型中两个数相加大于int范围时，求平均数可用**min+（max - min）/2**

